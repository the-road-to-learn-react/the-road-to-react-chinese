## React 重新获取数据

至此，App 组件使用预定义的查询条件获取了一次 stories 列表。这之后，用户可以在客户端搜索 stories。现在我们将把这个功能从客户端移到服务端，用实际的 `searchTerm` 作为动态查询条件来请求 API。

首先，移除 `searchedStories`，因为我们将通过 API 搜索获取 stories 。只传递常规的 stories 给 List 组件：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
# leanpub-start-insert
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
# leanpub-end-insert
      )}
    </div>
  );
};
~~~~~~~

第二步，使用组件 state 中实际的 `searchTerm`，而不是像之前那样使用硬编码作为搜索关键词。如果 `searchTerm` 是空字符串，则什么都不做。


{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
# leanpub-start-insert
    if (searchTerm === '') return;
# leanpub-end-insert

    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    fetch(`${API_ENDPOINT}${searchTerm}`)
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
  }, []);

  ...
};
~~~~~~~

现在按照搜索关键词初始化搜索，因此我们需要实现数据的重新获取。如果 `searchTerm` 改变，再次执行获取数据的副作用函数。如果 `searchTerm` 没有展示（如 null，空字符串，undefined)，则什么都不做（作为一种更普遍的状况）：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
# leanpub-start-insert
    if (!searchTerm) return;
# leanpub-end-insert

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
  }, [searchTerm]);
# leanpub-end-insert

  ...
};
~~~~~~~

我们将该功能从客户端搜索改为了服务端搜索。使用 `searchTerm` 获取一组被服务端筛选的列表，而不是在客户端筛选预先定义的列表。服务端的搜索不仅发生在初始数据获取时，同时也在 `searchTerm` 改变时获取数据。该功能现在完全在服务端完成。

每次在输入框中输入内容就重新获取一次数据并不是最佳方案，这一点我们将在稍后修复。因为这种实现会给 API 造成压力，如果你发起太多次的请求，则可能会发生一些错误。

### 练习

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Data-Re-Fetching-in-React).
* 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Data-Fetching-with-React...hs/Data-Re-Fetching-in-React?expand=1)。
