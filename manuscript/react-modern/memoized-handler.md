## React 中 Memoized 函数（高级）

前一节我们讲了处理函数、回调函数以及内联函数。现在我们将介绍**memoized 处理函数**，它可以被用于处理函数和回调函数的最顶层，接下来我们会把所有的数据请求的逻辑移动至副作用外部的一个单独的函数中（A）；然后将其用 `useCallback` Hook 包裹（B）；并且在 `useEffect` Hook 中调用它。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  // A
# leanpub-start-insert
  const handleFetchStories = React.useCallback(() => {
# leanpub-end-insert
    if (!searchTerm) return;

    dispatchStories({ type: 'STORIES_FETCH_INIT' });

    fetch(`${API_ENDPOINT}${searchTerm}`)
      .then(response => response.json())
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
          payload: result.hits,
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
# leanpub-start-insert
  }, [searchTerm]); // E
# leanpub-end-insert

  React.useEffect(() => {
# leanpub-start-insert
    handleFetchStories(); // C
  }, [handleFetchStories]); // D
# leanpub-end-insert

  ...
};
~~~~~~~

应用的行为与之前一致，只是重构了一下实现逻辑。与在副作用中使用匿名函数进行请求数据相比，我们将其重构成了一个对应用来说更可用的函数。

让我们一起来探讨一下为什么在这儿需要 React 的 `useCallback` Hook。每当其依赖数组（E）改变时这个 Hook 就会创建一个 memoized 函数。结果，因为 `useEffect` Hook 依赖于新的函数（D）所以它会再次运行（C）：

{title="Visualization",lang="javascript"}
~~~~~~~
1. change: searchTerm
2. implicit change: handleFetchStories
3. run: side-effect
~~~~~~~

如果我们没有使用 `useCallback` Hook 创建 memoized 函数的话，每当有一个 App 组件渲染时就会创建一个新的 `handleFetchStories` 方法。`handleFetchStories` 方法每次都会被创建，并且会在 `useEffect` Hook 中执行获取数据，随后获取到的数据会被存在组件的 state 中。因为组件的 state 改变了，所以组件就会重新渲染并创建一个新的 `handleFetchStories` 方法。副作用函数会被触发再次获取数据，这样我们就会陷入一个无限循环中：

{title="Visualization",lang="javascript"}
~~~~~~~
1. define: handleFetchStories
2. run: side-effect
3. update: state
4. re-render: component
5. re-define: handleFetchStories
6. run: side-effect
...
~~~~~~~

只有当搜索内容改变时 `useCallback` Hook 才会更改函数。那是当我们想触发重新获取数据时，因为输入字段中有新的输入，并且我们希望看到列表中展示新的数据。

通过将获取数据的函数移动到 `useEffect` Hook 外，让它可以在应用的其他部分复用。我们暂时不会使用它，但这是理解 `useCallback` Hook 的一种方式。现在当 `searchTerm` 改变时， `useEffect` Hook 将会运行，因为每次 `searchTerm` 更改时都会重新定义 `handleFetchStories`。因为 `useEffect` Hook 依赖于 `handleFetchStories`，所以获取数据的副作用会再次运行。

### 练习

* 检查 [上一节源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Memoized-Handler-in-React)。
* 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Data-Re-Fetching-in-React...hs/Memoized-Handler-in-React?expand=1)。
* 阅读更多关于 [React 的 useCallback Hook](https://reactjs.org/docs/hooks-reference.html#usecallback)。
