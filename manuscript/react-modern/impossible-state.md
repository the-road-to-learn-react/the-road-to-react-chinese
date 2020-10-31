## 不合理的状态

可能你已经注意到了在 App 组件里每个 state 之间是没有关联的，虽然看起来好像应该是属于一起的，因为都使用了 `useState` hook 的缘故。技术上来讲，所有和异步数据相关的 state 应该是统一的，不仅仅是 stories 作为实际的数据，还应包括它的加载和错误状态。

在一个 React 组件中出现多个 `useState` 本身没什么问题，但要警惕连续使用 state 更新函数。这些条件状态可能会导致**不合理的状态**，以及在 UI 上意想不到的行为。我们试着把伪数据获取函数改成下面这样，来模拟错误处理：

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
  new Promise((resolve, reject) => setTimeout(reject, 2000));
~~~~~~~

不合理的状态会出现在获取异步数据出错的时候。保存 error 的 state 更新了，但标识 loading 的 state 没被取消。在 UI 上，这会让用户同时看到无休止的 loading 标识和一条错误信息；比较好的处理方式应该只给用户展示错误信息，隐藏 loading 标识。不合理的状态很难定位到，却经常引发 UI 上的问题，这也是它臭名远扬的原因。

所幸的是，我们可以通过把分散在多个 `useState` 和 `useReducer` 里但是同属一体的状态，挪到一个 `useReducer` hook 里，来减少出错的机率。找到下面这些 `useState` hooks：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    []
  );
  const [isLoading, setIsLoading] = React.useState(false);
  const [isError, setIsError] = React.useState(false);

  ...
};
~~~~~~~

把它们合并到一个 `useReducer` hook 里，用一个更复杂的 state 对象，作为统一管理 state 的方式：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
# leanpub-start-insert
    { data: [], isLoading: false, isError: false }
# leanpub-end-insert
  );

  ...
};
~~~~~~~

所有和异步数据读取相关的操作必须通过这个新的 dispatch 函数来更新 state：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    { data: [], isLoading: false, isError: false }
  );

  React.useEffect(() => {
# leanpub-start-insert
    dispatchStories({ type: 'STORIES_FETCH_INIT' });
# leanpub-end-insert

    getAsyncStories()
      .then(result => {
        dispatchStories({
# leanpub-start-insert
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-end-insert
          payload: result.data.stories,
        });
      })
      .catch(() =>
# leanpub-start-insert
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
# leanpub-end-insert
      );
  }, []);

  ...
};
~~~~~~~

因为这里引入了新的类型来转换 state，所以在 `storiesReducer` 里需要处理它们：

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
  switch (action.type) {
# leanpub-start-insert
    case 'STORIES_FETCH_INIT':
      return {
        ...state,
        isLoading: true,
        isError: false,
      };
    case 'STORIES_FETCH_SUCCESS':
      return {
        ...state,
        isLoading: false,
        isError: false,
        data: action.payload,
      };
    case 'STORIES_FETCH_FAILURE':
      return {
        ...state,
        isLoading: false,
        isError: true,
      };
    case 'REMOVE_STORY':
      return {
        ...state,
        data: state.data.filter(
          story => action.payload.objectID !== story.objectID
        ),
      };
# leanpub-end-insert
    default:
      throw new Error();
  }
};
~~~~~~~

每次 state 转换，都返回一个新的 state 对象，包含所有当前 state 对象里的键值对（使用 JavaScript 的展开运算符），再用新的属性覆盖。举例来说，`STORIES_FETCH_FAILURE` 重置了 `isLoading`、并设置了 `isError` 布尔标识，但保留了其他的 state，例如 `stories`。这就是如何绕过之前可能会出现的问题：如果发生了错误，应该取消加载状态。

也请仔细观察 `REMOVE_STORY` action 的变化。它需要操作的是 `state.data`，而不再是一个简单的 `state`。现在的 state 是一个复杂的对象，里面有 data、loading 和 error 状态，不仅仅是一个单纯的 stories 列表。在其余的代码里也需要解决这个变化产生的影响：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    { data: [], isLoading: false, isError: false }
  );

  ...

# leanpub-start-insert
  const searchedStories = stories.data.filter(story =>
# leanpub-end-insert
    story.title.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <div>
      ...

# leanpub-start-insert
      {stories.isError && <p>Something went wrong ...</p>}
# leanpub-end-insert

# leanpub-start-insert
      {stories.isLoading ? (
# leanpub-end-insert
        <p>Loading ...</p>
      ) : (
        <List
          list={searchedStories}
          onRemoveItem={handleRemoveStory}
        />
      )}
    </div>
  );
};
~~~~~~~

再试着用抛错的数据获取函数，检查是否所有功能都能正常工作：

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
  new Promise((resolve, reject) => setTimeout(reject, 2000));
~~~~~~~

我们从多个 `useState` 带来的不可靠的 state 转换脱离出来，转而使用 React 的 `useReducer` hook 来实现可预见的 state 转换。这个用 reducer 管理的 state 对象囊括了所有关于 stories 的数据，包括加载中和错误状态，也实现了如何从 stories 列表中删除特定 story 这样的功能。我们没有完全摆脱掉不合理的状态，因为始终都有可能遗漏某个关键的布尔值，但我们向更可预见的状态管理迈进了一步。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Impossible-States)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Advanced-State...hs/React-Impossible-States?expand=1)。
* 阅读教程里之前提到的关于 JavaScript 和 React 中的 reducer 的章节。
* 阅读更多关于[该如何选用 useState 和 useReducer](https://www.robinwieruch.de/react-usereducer-vs-usestate) 的文章。
