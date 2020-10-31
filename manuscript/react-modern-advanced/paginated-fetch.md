## 分页查询

通过 Hacker News API 搜索热门故事只是迈向全功能搜索引擎的一步，还有很多方法可以对搜索进行微调。仔细看看数据结构，观察 [Hacker News API](https://hn.algolia.com/api) 如何返回的不仅仅是一个 `hits` 列表。

具体来说，它返回的是一个分页列表。page 属性，在第一个响应中是 `0`，可以用来获取更多的分页列表作为结果。你只需要将下一个有相同搜索词的 page 传递给 API。

![](images/paginated-list.png)

下面展示了如何利用 Hacker News 数据结构实现分页获取。如果你已经习惯了其他应用程序的**分页**，你的脑海中可能会有一排从 1-10 的按钮--当前选中的页面被高亮显示为 1-[3]-10，点击其中的一个按钮就会获取并显示这个数据子集。

![](images/pagination.png)

相反，我们将把该功能实现为**无限分页**。我们不会在点击按钮时呈现一个单一的分页列表，而是将*所有的分页列表作为一个列表*，用*一个*按钮来获取下一页。每一个额外的*分页列表*都会在*一个列表*的末尾进行连接。

![](images/infinite-pagination.png)

**任务：**不是只获取列表的第一页，而是扩展获取后续页面的功能，将其作为点击按钮时的无限分页来实现。

**提示：**

* 用分页获取所需的参数扩展 `API_ENDPOINT`。
* 从 `result` 中获取数据后，将 `page` 存储为 state。
* 每次搜索都会获取第一页(`0`)的数据。
* 每当用一个新的 HTML 按钮触发一个额外的请求时，就获取后续的页面(`page + 1`)。

首先，扩展 API 常量，以便以后可以处理分页数据。我们将把这一个常量：

{title="src/App.js",lang="javascript"}
~~~~~~~
const API_ENDPOINT = 'https://hn.algolia.com/api/v1/search?query=';

const getUrl = searchTerm => `${API_ENDPOINT}${searchTerm}`;
~~~~~~~

改为一个可合成的 API 常量，其参数如下：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const API_BASE = 'https://hn.algolia.com/api/v1';
const API_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
# leanpub-end-insert

// careful: notice the ? in between
# leanpub-start-insert
const getUrl = searchTerm =>
  `${API_BASE}${API_SEARCH}?${PARAM_SEARCH}${searchTerm}`;
# leanpub-end-insert
~~~~~~~

幸运的是，我们不需要调整 API 端点，因为我们为它提取了一个通用的 `getUrl` 函数。但是，有一个地方我们必须在未来解决这个逻辑：

{title="src/App.js",lang="javascript"}
~~~~~~~
const extractSearchTerm = url => url.replace(API_ENDPOINT, '');
~~~~~~~

在接下来的步骤中，仅仅替换掉我们 API 端点的基础是不够的，我们的代码中已经没有了 API 端点。随着 API 端点的参数越来越多，URL 变得更加复杂。它将从 X 变为 Y：

{title="src/App.js",lang="javascript"}
~~~~~~~
// X
https://hn.algolia.com/api/v1/search?query=react

// Y
https://hn.algolia.com/api/v1/search?query=react&page=0
~~~~~~~

最好是提取搜索词，将 `?` 和 `&` 之间的所有内容提取出来。另外，考虑到 `query` 参数是直接放在 `?` 之后的，而所有其他参数如 `page` 都在它之后。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const extractSearchTerm = url =>
  url
    .substring(url.lastIndexOf('?') + 1, url.lastIndexOf('&'));
# leanpub-end-insert
~~~~~~~

键(`query=`)也需要替换，只留下值(`searchTerm`)：

{title="src/App.js",lang="javascript"}
~~~~~~~
const extractSearchTerm = url =>
  url
    .substring(url.lastIndexOf('?') + 1, url.lastIndexOf('&'));
# leanpub-start-insert
    .replace(PARAM_SEARCH, '');
# leanpub-end-insert
~~~~~~~

基本上，我们会修剪字符串，直到只留下搜索词：

{title="src/App.js",lang="javascript"}
~~~~~~~
// url
https://hn.algolia.com/api/v1/search?query=react&page=0

// url after  substring
query=react

// url after replace
react
~~~~~~~

从 Hacker News API 返回的结果为我们提供了 `page` 数据：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(async () => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

    try {
      const lastUrl = urls[urls.length - 1];
      const result = await axios.get(lastUrl);

      dispatchStories({
        type: 'STORIES_FETCH_SUCCESS',
# leanpub-start-insert
        payload: {
          list: result.data.hits,
          page: result.data.page,
        },
# leanpub-end-insert
      });
    } catch {
      dispatchStories({ type: 'STORIES_FETCH_FAILURE' });
    }
  }, [urls]);

  ...
};
~~~~~~~

我们需要存储这些数据，以便以后进行分页获取：

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
  switch (action.type) {
    case 'STORIES_FETCH_INIT':
      ...
    case 'STORIES_FETCH_SUCCESS':
      return {
        ...state,
        isLoading: false,
        isError: false,
# leanpub-start-insert
        data: action.payload.list,
        page: action.payload.page,
# leanpub-end-insert
      };
    case 'STORIES_FETCH_FAILURE':
      ...
    case 'REMOVE_STORY':
      ...
    default:
      throw new Error();
  }
};

const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
# leanpub-start-insert
    { data: [], page: 0, isLoading: false, isError: false }
# leanpub-end-insert
  );

  ...
};
~~~~~~~

用新的 `page` 参数扩展 API 端点。这个变化被我们之前过早的优化所覆盖，当时我们从 URL 中提取了搜索词。

{title="src/App.js",lang="javascript"}
~~~~~~~
const API_BASE = 'https://hn.algolia.com/api/v1';
const API_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
# leanpub-start-insert
const PARAM_PAGE = 'page=';
# leanpub-end-insert

// careful: notice the ? and & in between
# leanpub-start-insert
const getUrl = (searchTerm, page) =>
  `${API_BASE}${API_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}`;
# leanpub-end-insert
~~~~~~~

接下来，我们必须通过传递 `page` 参数来调整所有 `getUrl` 的调用。由于初始搜索和最后一次搜索总是获取第一页(`0`)，我们将这一页作为参数传递给函数，以便检索适当的 URL：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [urls, setUrls] = React.useState([getUrl(searchTerm, 0)]);
# leanpub-end-insert

  ...

  const handleSearchSubmit = event => {
# leanpub-start-insert
    handleSearch(searchTerm, 0);
# leanpub-end-insert

    event.preventDefault();
  };

  const handleLastSearch = searchTerm => {
    setSearchTerm(searchTerm);

# leanpub-start-insert
    handleSearch(searchTerm, 0);
# leanpub-end-insert
  };

# leanpub-start-insert
  const handleSearch = (searchTerm, page) => {
    const url = getUrl(searchTerm, page);
# leanpub-end-insert
    setUrls(urls.concat(url));
  };

  ...
};
~~~~~~~

为了在按钮被点击时获取下一页，我们需要在这个新的处理程序中增加 `page` 参数：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleMore = () => {
    const lastUrl = urls[urls.length - 1];
    const searchTerm = extractSearchTerm(lastUrl);
    handleSearch(searchTerm, stories.page + 1);
  };
# leanpub-end-insert

  ...

  return (
    <div>
      ...

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
      )}

# leanpub-start-insert
      <button type="button" onClick={handleMore}>
        More
      </button>
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

我们已经用动态 `page` 参数实现了数据获取。初次和最后一次搜索总是使用第一页，而每次使用新的"更多"按钮获取数据都会使用递增的页面。不过在尝试这个功能的时候，有一个关键的 bug：新的取数并没有扩展之前的列表，而是完全取代了它。

![](images/concat.png)

我们在 reducer 中解决这个问题，避免用新的 `data` 替换当前的 `data`，对分页列表进行连接：

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
  switch (action.type) {
    case 'STORIES_FETCH_INIT':
      ...
    case 'STORIES_FETCH_SUCCESS':
      return {
        ...state,
        isLoading: false,
        isError: false,
# leanpub-start-insert
        data:
          action.payload.page === 0
            ? action.payload.list
            : state.data.concat(action.payload.list),
# leanpub-end-insert
        page: action.payload.page,
      };
    case 'STORIES_FETCH_FAILURE':
      ...
    case 'REMOVE_STORY':
      ...
    default:
      throw new Error();
  }
};
~~~~~~~

用新按钮获取更多数据后，显示的列表会增长。但是仍有闪烁的现象，使用户体验没那么好。在获取分页数据时，由于加载指示器出现，列表会消失片刻，并在请求解决后重新出现。

![](images/flicker.png)

所需的行为是渲染列表。在开始时是一个空列表，并将"更多"按钮替换为仅针对待处理请求的加载指示器。当任务从单个列表发展到分页列表时，这是一个常见的 UI 重构，用于有条件的渲染。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

# leanpub-start-insert
      <List list={stories.data} onRemoveItem={handleRemoveStory} />
# leanpub-end-insert

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
# leanpub-start-insert
        <button type="button" onClick={handleMore}>
          More
        </button>
# leanpub-end-insert
      )}
    </div>
  );
};
~~~~~~~

现在可以持续获取热门故事的数据了。当使用第三方 API 时，探索它的边界总是一个好主意。每个远程 API 都会返回不同的数据结构，所以它的功能可能会有所不同，并且可以在消费 API 的应用程序中使用。

### 练习

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Paginated-Fetch).
* 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Remember-Last-Searches...hs/Paginated-Fetch?expand=1).
* 重新访问 [Hacker News API 文档](https://hn.algolia.com/api)：是否有办法通过向API端添加更多参数来获取更多的页面列表项目？
* 重温本节开头讲到的分页和无限分页。你将如何实现一个普通的分页组件，按钮从 1-[3]-10，每个按钮只取并显示一页列表。
* 与其说有一个"更多"按钮，不如说用无限滚动技术实现无限分页？无限滚动不是明确地点击一个按钮来获取下一页，而是在浏览器的视口到达显示列表的底部后，就可以获取下一页。
