> ## Explicit Data Fetching with React

## 使用 React 进行显式数据获取

> Re-fetching all data each time someone types in the input field isn't optimal. Since we're using a third-party API to fetch the data, its internals are out of our reach. Eventually, we will incur [rate limiting](https://en.wikipedia.org/wiki/Rate_limiting), which returns an error instead of data.

每次在输入框中输入内容就重新获取数据不是最佳的选择。因为我们使用的是第三方 API 去获取数据，频繁的调用可能引发 [限速](https://en.wikipedia.org/wiki/Rate_limiting)，接口将返回错误。

> To solve this problem, change the implementation details from implicit to explicit data (re-)fetching. In other words, the application will refetch data only if someone clicks a confirmation button. First, add a button element for the confirmation to the JSX:

要解决这个问题，我们把获取数据的实现方式从隐式改为显式。换句话说，仅在点击确认按钮时才重新获取数据。首先，我们添加一个确认按钮：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <InputWithLabel
        id="search"
        value={searchTerm}
        isFocused
# leanpub-start-insert
        onInputChange={handleSearchInput}
# leanpub-end-insert
      >
        <strong>Search:</strong>
      </InputWithLabel>

# leanpub-start-insert
      <button
        type="button"
        disabled={!searchTerm}
        onClick={handleSearchSubmit}
      >
        Submit
      </button>
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

> Second, the handler, input, and button handler receive implementation logic to update the component's state. The input field handler still updates the `searchTerm`; the button handler sets the `url` derived from the *current* `searchTerm` and the static API URL as a new state:

然后，监听输入框、按钮的函数会处理相应逻辑并更新组件状态。输入框的处理函数仍然会用来更新 `searchTerm`，按钮的处理函数会组装 `API_ENDPOINT` 和 *最新的* `searchTerm` 并更新到 `url` 状态中：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const [searchTerm, setSearchTerm] = useSemiPersistentState(
    'search',
    'React'
  );

# leanpub-start-insert
  const [url, setUrl] = React.useState(
    `${API_ENDPOINT}${searchTerm}`
  );
# leanpub-end-insert

  ...

# leanpub-start-insert
  const handleSearchInput = event => {
# leanpub-end-insert
    setSearchTerm(event.target.value);
  };

# leanpub-start-insert
  const handleSearchSubmit = () => {
    setUrl(`${API_ENDPOINT}${searchTerm}`);
  };
# leanpub-end-insert

  ...
};
~~~~~~~

> Third, instead of running the data fetching side-effect on every `searchTerm` change -- which would happen each time the input field's value changes -- the `url` is used. The `url` is set explicitly by the user when the search is confirmed via our new button:

之后，将每次因输入框值改变 `searchTerm`(`url`) 而导致的数据请求，改为每次用户点击确认按钮来触发：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    fetch(url)
# leanpub-end-insert
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
  }, [url]);
# leanpub-end-insert

  React.useEffect(() => {
    handleFetchStories();
  }, [handleFetchStories]);

  ...
};
~~~~~~~

> Before the `searchTerm` was used for two cases: updating the input field's state and activating the side-effect for fetching data. Too many responsibilities one may would have said. Now it's only used for the former. A second state called `url` got introduced for triggering the side-effect for fetching data which only happens when a user clicks the confirmation button.

之前的 `searchTerm` 有两个用途：更新（存储）输入框的值和触发获取数据，现在它仅用于更新（存储）输入框的值。现在当用户点击确认按钮时，`url` 状态会被更新并引起相应的副作用以获取新的数据。

> ### 练习：

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Explicit-Data-Fetching-with-React).
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Memoized-Handler-in-React...hs/Explicit-Data-Fetching-with-React?expand=1).
* 为什么 `url` 的状态用 `useState` 而不是 `useSemiPersistentState` 来管理？
* 为什么在 `handleFetchStories` 函数中不再检查空的 `searchTerm`？