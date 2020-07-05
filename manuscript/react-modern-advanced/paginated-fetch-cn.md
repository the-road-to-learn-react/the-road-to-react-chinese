> ## Paginated Fetch

## 分页查询

> Searching for popular stories via Hacker News API is only one step towards a fully-functional search engine, and there are many ways to fine-tune the search. Take a closer look at the data structure and observe how [the Hacker News API](https://hn.algolia.com/api) returns more than a list of `hits`.

通过 Hacker News API 搜索热门故事只是迈向全功能搜索引擎的一步，还有很多方法可以对搜索进行微调。仔细看看数据结构，观察[Hacker News API](https://hn.algolia.com/api)如何返回的不仅仅是一个`hits`列表。

> Specifically, it returns a paginated list. The page property, which is `0` in the first response, can be used to fetch more paginated lists as results. You only need to pass the next page with the same search term to the API.

具体来说，它返回的是一个分页列表。页面属性，在第一个响应中是`0`，可以用来获取更多的分页列表作为结果。你只需要将下一个有相同搜索词的页面传递给API。

![](images/paginated-list.png)

> The following shows how to implement a paginated fetch with the Hacker News data structure. If you are used to **pagination** from other applications, you may have a row of buttons from 1-10 in your mind -- where the currently selected page is highlighted 1-[3]-10 and where clicking one of the buttons leads to fetching and displaying this subset of data.

下面展示了如何利用 Hacker News 数据结构实现分页获取。如果你已经习惯了其他应用程序的**分页**，你的脑海中可能会有一排从1-10的按钮--当前选中的页面被高亮显示为1-[3]-10，点击其中的一个按钮就会获取并显示这个数据子集。

![](images/pagination.png)

> In contrast, we will implement the feature as **infinite pagination**. Instead of rendering a single paginated list on a button click, we will render *all paginated lists as one list* with *one* button to fetch the next page. Every additional *paginated list* is concatenated at the end of the *one list*.

相反，我们将把该功能实现为**无限分页**。我们不会在点击按钮时呈现一个单一的分页列表，而是将*所有的分页列表作为一个列表*，用*一个*按钮来获取下一页。每一个额外的*分页列表*都会在*一个列表*的末尾进行连接。

![](images/infinite-pagination.png)

> **Task:** Rather than fetching only the first page of a list, extend the functionality for fetching succeeding pages. Implement this as an infinite pagination on button click.

**任务：**不是只获取列表的第一页，而是扩展获取后续页面的功能，将其作为点击按钮时的无限分页来实现。

**提示：**

> * Extend the `API_ENDPOINT` with the parameters needed for the paginated fetch.
> * Store the `page` from the `result` as state after fetching the data.
> * Fetch the first page (`0`) of data with every search.
> * Fetch the succeeding page ( `page + 1`) for every additional request triggered with a new HTML button.

* 用分页获取所需的参数扩展`API_ENDPOINT`。
* 从`result`中获取数据后，将`page`存储为 state。
* 每次搜索都会获取第一页(`0`)的数据。
* 每当用一个新的 HTML 按钮触发一个额外的请求时，就获取后续的页面(`page + 1`)。

> First, extend the API constant so it can deal with paginated data later. We will turn this one constant:

首先，扩展API常量，以便以后可以处理分页数据。我们将把这一个常量：

{title="src/App.js",lang="javascript"}
~~~~~~~
const API_ENDPOINT = 'https://hn.algolia.com/api/v1/search?query=';

const getUrl = searchTerm => `${API_ENDPOINT}${searchTerm}`;
~~~~~~~

> Into a composable API constant with its parameters:

进入一个可合成的API常量，其参数：

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

> Fortunately, we don't need to adjust the API endpoint, because we extracted a common `getUrl` function for it. However, there is one spot where we must address this logic for the future:

幸运的是，我们不需要调整API端点，因为我们为它提取了一个通用的`getUrl`函数。但是，有一个地方我们必须在未来解决这个逻辑：

{title="src/App.js",lang="javascript"}
~~~~~~~
const extractSearchTerm = url => url.replace(API_ENDPOINT, '');
~~~~~~~

> In the next steps, it won't be sufficient to replace the base of our API endpoint, which is no longer in our code. With more parameters for the API endpoint, the URL becomes more complex. It will change from X to Y:

在接下来的步骤中，仅仅替换掉我们API端点的基础是不够的，我们的代码中已经没有了API端点。随着API端点的参数越来越多，URL变得更加复杂。它将从X变为Y：

{title="src/App.js",lang="javascript"}
~~~~~~~
// X
https://hn.algolia.com/api/v1/search?query=react

// Y
https://hn.algolia.com/api/v1/search?query=react&page=0
~~~~~~~

> It's better to extract the search term by extracting everything between `?` and `&`. Also consider that the `query` parameter is directly after the `?` and all other parameters like `page` follow it.

最好是提取搜索词，将`?`和`&`之间的所有内容提取出来。另外，考虑到`query`参数是直接放在`??`之后的，而所有其他参数如`page`都在它之后。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const extractSearchTerm = url =>
  url
    .substring(url.lastIndexOf('?') + 1, url.lastIndexOf('&'));
# leanpub-end-insert
~~~~~~~

> The key (`query=`) also needs to be replaced, leaving only the value (`searchTerm`):

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

> Essentially, we'll trim the string until we leave only the search term:

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

> The returned result from the Hacker News API delivers us the `page` data:

从Hacker News API返回的结果为我们提供了`page`数据：

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

> We need to store this data to make paginated fetches later:

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

> Extend the API endpoint with the new `page` parameter. This change was covered by our premature optimizations earlier, when we extracted the search term from the URL.

用新的`page`参数扩展API端点。这个变化被我们之前过早的优化所覆盖，当时我们从URL中提取了搜索词。

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

> Next, we must adjust all `getUrl` invocations by passing the `page` argument. Since the initial search and last search always fetch the first page (`0`), we pass this page as an argument to the function for retrieving the appropriate URL:

接下来，我们必须通过传递`page`参数来调整所有`getUrl`的调用。由于初始搜索和最后一次搜索总是获取第一页(`0`)，我们将这一页作为参数传递给函数，以便检索适当的URL：

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

> To fetch the next page when a button is clicked, we'll need to increment the `page` argument in this new handler:

为了在按钮被点击时获取下一页，我们需要在这个新的处理程序中增加`page`参数：

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

> We've implemented data fetching with the dynamic `page` argument. The initial and last searches always use the first page, and every fetch with the new "More" button uses an incremented page. There is one crucial bug when trying the feature, though: the new fetches don't extend the previous list, but completely replace it.

我们已经用动态`page`参数实现了数据获取。初次和最后一次搜索总是使用第一页，而每次使用新的"更多"按钮获取数据都会使用递增的页面。不过在尝试这个功能的时候，有一个关键的bug：新的取数并没有扩展之前的列表，而是完全取代了它。

![](images/concat.png)

> We solve this in the reducer by avoiding the replacement of current `data` with new `data`, concatenating the paginated lists:

我们在 reducer 中解决这个问题，避免用新的`data`替换当前的`data`，对分页列表进行连接：

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

> The displayed list grows after fetching more data with the new button. However, there is still a flicker straining the UX. When fetching paginated data, the list disappears for a moment because the loading indicator appears and reappears after the request resolves.

用新按钮获取更多数据后，显示的列表会增长。但是仍有闪烁的现象，使用户体验没那么好。在获取分页数据时，由于加载指示器出现，列表会消失片刻，并在请求解决后重新出现。

![](images/flicker.png)

> The desired behavior is to render the list--which is an empty list in the beginning--and replace the "More" button with the loading indicator only for pending requests. This is a common UI refactoring for conditional rendering when the task evolves from a single list to paginated lists.

所需的行为是渲染列表。在开始时是一个空列表，并将"更多"按钮替换为仅针对待处理请求的加载指示器。当任务从单个列表发展到分页列表时，这是一个常见的UI重构，用于有条件的渲染。

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

> It's possible to fetch ongoing data for popular stories now. When working with third-party APIs, it's always a good idea to explore its boundaries. Every remote API returns different data structures, so its features may vary, and can be used in applications that consume the API.

现在可以持续获取热门故事的数据。当使用第三方API时，探索它的边界总是一个好主意。每个远程API都会返回不同的数据结构，所以它的功能可能会有所不同，并且可以在消费API的应用程序中使用。

### 练习：

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Paginated-Fetch).
  * 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Remember-Last-Searches...hs/Paginated-Fetch?expand=1).
* 重新访问 [Hacker News API 文档](https://hn.algolia.com/api)：是否有办法通过向API端添加更多参数来获取更多的页面列表项目？
* 重温本节开头讲到的分页和无限分页。你将如何实现一个普通的分页组件，按钮从1-[3]-10，每个按钮只取并显示一页列表。
* 与其说有一个"更多"按钮，不如说用无限滚动技术实现无限分页？无限滚动不是明确地点击一个按钮来获取下一页，而是在浏览器的视口到达显示列表的底部后，就可以获取下一页。
