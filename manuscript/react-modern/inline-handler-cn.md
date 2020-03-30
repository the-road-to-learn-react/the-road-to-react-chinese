## JSX中的内联处理函数

到目前为止，我们拥有的stories列表只是一个无状态的变量。我们可以使用搜索的功能来筛选渲染的列表，但是如果我们删除过滤器，列表本身将保持不变。过滤器只是通过第三方临时更改，但我们不能操作真实的列表

为了获得对列表的控制权，把它作为React的useState Hook中的初始状态，可以让它具有状态。返回值是当前状态（`stories`）和状态更新函数（`setStories`）。我们没有使用自定义`useSemiPersistentState`hook,因为我们不想每次都用缓存列表打开浏览器。相反，我们总是想要用初始列表开始。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const initialStories = [
  {
    title: 'React',
    ...
  },
  {
    title: 'Redux',
    ...
  },
];
# leanpub-end-insert

const useSemiPersistentState = (key, initialState) => { ... };

const App = () => {
  const [searchTerm, setSearchTerm] = ...

# leanpub-start-insert
  const [stories, setStories] = React.useState(initialStories);
# leanpub-end-insert

  ...
};
~~~~~~~

这应用程序的行为是一样的，因为现在从`useState`返回`stories`，仍然会被过滤到`searchedStories`中，并展示在列表中。接下来，我们将要通过移除列表中的一个项目来操作列表：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState(initialStories);

# leanpub-start-insert
  const handleRemoveStory = item => {
    const newStories = stories.filter(
      story => item.objectID !== story.objectID
    );

    setStories(newStories);
  };
# leanpub-end-insert

  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

      ...

      <hr />

# leanpub-start-insert
      <List list={searchedStories} onRemoveItem={handleRemoveStory} />
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

App组件中的回调函数将要删除的项目作为参数接受，并且会筛选当前stories根据删除所有不符合条件的项目的信息。然后将返回的stories作为新的状态，并且List组件会传递函数给它的子组件。它没有使用这个新的信息，它仅是传递下去：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = ({ list, onRemoveItem }) =>
# leanpub-end-insert
  list.map(item => (
    <Item
      key={item.objectID}
      item={item}
# leanpub-start-insert
      onRemoveItem={onRemoveItem}
# leanpub-end-insert
    />
  ));
~~~~~~~

最后，我们可以在Item组件的另一个处理函数中使用传入函数，将`item`传递给它。一个按钮元素可以被使用来触发实际的事件。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const Item = ({ item, onRemoveItem }) => {
  const handleRemoveItem = () => {
    onRemoveItem(item);
  };
# leanpub-end-insert

  return (
    <div>
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
      <span>{item.num_comments}</span>
      <span>{item.points}</span>
# leanpub-start-insert
      <span>
        <button type="button" onClick={handleRemoveItem}>
          Dismiss
        </button>
      </span>
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

我们可能只能传递项目的`objectID`，因为这是我们在App组件的回调处理函数中所需要的全部，但是我们不确定这个处理函数之后可能需要什么信息。删除一个项目可能需要的不仅是一个标识符。如果我们调用`onRemoveItem`处理函数，这个Item应该被传递，不仅仅是它的标识符。

我们已经使用React的useState Hook，让stories列表具有状态。传递搜索到的stories作为List组件的props。为了能够被使用在各自的组件，实现了回调处理函数(`handleRemoveStory`)和处理函数(`handleRemoveItem`)。由于处理程序仅是一个函数，在这种情况下它不会返回任何内容，为了完整性，我们可以移除这个块体。

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => {
# leanpub-start-insert
  const handleRemoveItem = () =>
    onRemoveItem(item);
# leanpub-end-insert

  ...
};
~~~~~~~

由于我们在函数组件中积累了处理函数，这会造成我们源代码可读性降低。有时我会在函数组件中，重构处理函数，从箭头函数返回普通函数语句，仅是为了制作组件更加容易探索。

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => {
# leanpub-start-insert
  function handleRemoveItem() {
    onRemoveItem(item);
  }
# leanpub-end-insert

  ...
};
~~~~~~~

在本节中，我们使用了props，处理函数，回调处理函数和状态。这些都是之前的全部课程。现在我们将追踪**内联处理函数**，它允许我们在JSX中正确执行函数。在Item组件中传入函数作为内联处理函数有两种解决方案。首先，使用JavaScript的bind方法：

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
    <span>
# leanpub-start-insert
      <button type="button" onClick={onRemoveItem.bind(null, item)}>
# leanpub-end-insert
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

在函数上使用 [JavaScript's bind method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) 允许我们直接绑定参数，在函数执行的时候使用它。bind的方法返回一个附加了绑定参数的新函数。

第二个更多流行的解决方案是使用一个包装箭头函数，它允许我们潜入像`item`类的参数：

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
    <span>
# leanpub-start-insert
      <button type="button" onClick={() => onRemoveItem(item)}>
# leanpub-end-insert
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

这是一个快速的解决方案，因为有时我们不想把一个函数组件的简短函数体重构回块体，为了定义一个适当的函数处理在函数签名和返回语句之间。尽管这个方式比起其他的是更加简洁的，但是由于JavaScript逻辑可能会被隐藏在JSX中，它也是更加困难的对于调试。如果包装箭头函数使用块体而不是简洁体来封装超过一行的实现逻辑，它会变得更加冗长。应该避免这种情况：

{title="Code Playground",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <div>
    ...
    <span>
      <button
        type="button"
        onClick={() => {
          // do something else

          // note: avoid using complex logic in JSX

          onRemoveItem(item);
        }}
      >
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

可以接受三个处理函数版本中，其中二个是内联和一个常规处理函数。非内联的函数处理会移动详细的实现进入函数组件块体，内联函数会移动详细的实现在JSX中。

### 练习:

* 检查 [上一节的源代码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Inline-Handler-in-JSX).
  * 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Imperative-React...hs/Inline-Handler-in-JSX?expand=1).
* 查看处理函数，回调处理函数和内联处理函数