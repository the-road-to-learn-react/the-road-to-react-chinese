> ## Remember Last Searches

## 记住上一次的搜索记录

> **Task:** Remember the last five search terms to hit the API, and provide a button to move quickly between searches. When the buttons are clicked,  stories for the search term are fetched again.

**任务：** 记住最近五次的搜索记录用来访问 API，并提供一个能在搜索词之间快速移动的按钮。当点击按钮时，将再次获取搜索词的内容结果。

> **Optional Hints:**

**提示：**

> * Don't use a new state for this feature. Instead, reuse the `url` state and `setUrl` state updater function to fetch stories from the API. Adapt them to multiple `urls` as state, and to set multiple `urls` with `setUrls`. The last URL from `urls` can be used to fetch the data, and the last five URLs from `urls` can be used to display the buttons.

* 不要为这个功能使用新的 state。相反，重用 `url` state 和 `setUrl` state 更新函数来重新从 API 获取内容。将它们适配为 `urls` 状态，并且使用 `setUrls` 设置 `urls`。`urls` 中的最后一个 URL 可以用来获取数据，并且`urls` 中最后五个 URL 可以用来显示按钮。

> First, we will refactor all `url` to `urls` state and all `setUrl` to `setUrls` state updater functions. Instead of initializing the state with a `url` as a string, make it an array with the initial `url` as its only entry:

首先，我们将所有的 `url` 重构到一个 `urls` state 中，同时将 `setUrl` 重构为 `setUrls` state 更新函数。与其使用字符串 `url` 初始化 state，倒不如把它变成一个数组，把初始的 `url` 作为其唯一的条目：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [urls, setUrls] = React.useState([
# leanpub-end-insert
    `${API_ENDPOINT}${searchTerm}`,
# leanpub-start-insert
  ]);
# leanpub-end-insert

  ...
};
~~~~~~~

> Second, instead of using the current `url` state for data fetching, use the last `url` entry from the `urls` array. If another `url` is added to the list of `urls`, it is used to fetch data instead:

第二，使用 `urls` 数组中最后一个 `url` 条目来获取数据，而不是使用当前 `url` state。如果 `urls` 中新添加了另一个 `url`，则就用它来获取数据：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {

  ...

  const handleFetchStories = React.useCallback(async () => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

    try {
# leanpub-start-insert
      const lastUrl = urls[urls.length - 1];
      const result = await axios.get(lastUrl);
# leanpub-end-insert

      dispatchStories({
        type: 'STORIES_FETCH_SUCCESS',
        payload: result.data.hits,
      });
    } catch {
      dispatchStories({ type: 'STORIES_FETCH_FAILURE' });
    }
# leanpub-start-insert
  }, [urls]);
# leanpub-end-insert

  ...
};
~~~~~~~

> And third, instead of storing `url` string as state with the state updater function, concat the new `url` with the previous `urls` in an array for the new state:

第三，不要用 state 更新函数将 `url` 字符串储存为 state，而是将新的 `url` 与之前的 `urls` 数组合并成新的 state：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleSearchSubmit = event => {
# leanpub-start-insert
    const url = `${API_ENDPOINT}${searchTerm}`;
    setUrls(urls.concat(url));
# leanpub-end-insert

    event.preventDefault();
  };

  ...
};
~~~~~~~

> With each search, another URL is stored in our state of `urls`. Next, render a button for each of the last five URLs. We'll include a new universal handler for these buttons, and each passes a specific `url` with a more specific inline handler:

每一次搜索时，新的 URL 都会储存在我们的 `urls` state 中。接下来，渲染为最后五个 URL 中每个 URL 渲染一个按钮，我们将为这些按钮添加一个新的通用的处理方法，并且每次给一个特定的内联函数传递一个特定的 `url`：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const getLastSearches = urls => urls.slice(-5);
# leanpub-end-insert

...

const App = () => {
  ...

# leanpub-start-insert
  const handleLastSearch = url => {
    // do something
  };
# leanpub-end-insert

# leanpub-start-insert
  const lastSearches = getLastSearches(urls);
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <SearchForm ... />

# leanpub-start-insert
      {lastSearches.map(url => (
        <button
          key={url}
          type="button"
          onClick={() => handleLastSearch(url)}
        >
          {url}
        </button>
      ))}
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

> Next, instead of showing the whole URL of the last search in the button as button text, show only the search term by replacing the API's endpoint with an empty string:

接下来，不要把按钮中的最后一个搜索的 URL 作为按钮的文本展示出来，而是用一个空字符串代替 API 的端点，只显示搜索词：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const extractSearchTerm = url => url.replace(API_ENDPOINT, '');
# leanpub-end-insert

# leanpub-start-insert
const getLastSearches = urls =>
  urls.slice(-5).map(url => extractSearchTerm(url));
# leanpub-end-insert

...

const App = () => {
  ...

  const lastSearches = getLastSearches(urls);

  return (
    <div>
      ...

      {lastSearches.map(searchTerm => (
        <button
# leanpub-start-insert
          key={searchTerm}
# leanpub-end-insert
          type="button"
# leanpub-start-insert
          onClick={() => handleLastSearch(searchTerm)}
# leanpub-end-insert
        >
# leanpub-start-insert
          {searchTerm}
# leanpub-end-insert
        </button>
      ))}

      ...
    </div>
  );
};
~~~~~~~

> The `getLastSearches` function now returns search terms instead of URLs. The actual `searchTerm`is passed to the inline handler instead of the `url`. By mapping over the list of `urls` in `getLastSearches`, we can extract the search term for each `url` within the array's map method. Making it more concise, it can also look like this:

`getLastSearches` 函数目前返回的是搜索词而不是 URL。实际上，传递给内联函数的是 `searchTerm`，而不是 `url`。通过映射到 `getLastSearches` 中的 `url` 列表，我们能在数组的 map 方法中提取每个 `url` 的搜索词。为了更加简洁，它也可以这样写：

{title="src/App.js",lang="javascript"}
~~~~~~~
const getLastSearches = urls =>
# leanpub-start-insert
  urls.slice(-5).map(extractSearchTerm);
# leanpub-end-insert
~~~~~~~

> Now we'll provide functionality for the new handler used by every button, since clicking one of these buttons should trigger another search. Since we use the `urls` state for fetching data, and since we know the last URL is always used for data fetching, concat a new `url` to the list of `urls` to trigger another search request:

现在，因为点击其中一个按钮应该触发一次新的搜索，所以我们将为每个按钮使用的新处理函数提供功能。由于我们使用 `urls` state 来获取数据，并且我们知道最后一个 URL 总是用来获取数据的，所以在 `urls` 列表中添加一个新的 `url` 来触发另一次搜索请求：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleLastSearch = searchTerm => {
    const url = `${API_ENDPOINT}${searchTerm}`;
    setUrls(urls.concat(url));
  };
# leanpub-end-insert

  ...
};
~~~~~~~

> If you compare this new handler's implementation logic to the `handleSearchSubmit`, you may see some common functionality. Extract this common functionality to a new handler and a new extracted utility function:

如果你将这个新的处理函数的实现逻辑和 `handleSearchSubmit` 对比一下，你可能会发现一些共同的功能。将这些共同的功能提取到一个新的处理函数和新的提取的实用工具函数中：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const getUrl = searchTerm => `${API_ENDPOINT}${searchTerm}`;
# leanpub-end-insert

...

const App = () => {
  ...

  const handleSearchSubmit = event => {
# leanpub-start-insert
    handleSearch(searchTerm);
# leanpub-end-insert

    event.preventDefault();
  };

  const handleLastSearch = searchTerm => {
# leanpub-start-insert
    handleSearch(searchTerm);
# leanpub-end-insert
  };

# leanpub-start-insert
  const handleSearch = searchTerm => {
    const url = getUrl(searchTerm);
    setUrls(urls.concat(url));
  };
# leanpub-end-insert

  ...
};
~~~~~~~

> The new utility function can be used somewhere else in the App component. If you extract functionality that can be used by two parties, always check to see if it can be used by a third party.

新的实用工具函数可以在 App 组件的其他任何地方使用。如果你提取的功能可以被双方使用，那么一定要检查一下它是否可以被第三方使用。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  // important: still wraps the returned value in []
# leanpub-start-insert
  const [urls, setUrls] = React.useState([getUrl(searchTerm)]);
# leanpub-end-insert

  ...
};
~~~~~~~

> The functionality should work, but it complains or breaks if the same search term is used more than once, because `searchTerm` is used for each button element as `key` attribute. Make the key more specific by concatenating it with the `index` of the mapped array.

该功能应该可以使用了，但如果同一个搜索词被多次使用，它就会出问题了，因为 `searchTerm` 被作为 `key` 属性用于每个按钮元素。我们可以通过将 `key` 与映射数组的 `index` 串联起来，使得 `key` 更加具体。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

# leanpub-start-insert
      {lastSearches.map((searchTerm, index) => (
# leanpub-end-insert
        <button
# leanpub-start-insert
          key={searchTerm + index}
# leanpub-end-insert
          type="button"
          onClick={() => handleLastSearch(searchTerm)}
        >
          {searchTerm}
        </button>
      ))}

      ...
    </div>
  );
};
~~~~~~~

> It's not the perfect solution, because the `index` isn't a stable key (especially when adding items to the list; however, it doesn't break in this scenario. The feature works now, but you can add further UX improvements by following the tasks below.

这并不是完美的解决方案，因为 `index` 不是一个稳定的 key（尤其是当向列表中添加条目时）；然而，在这种场景下它并没有被破坏。功能现在可以运行了，但是你可以通过下面的任务对 UX 进行进一步的改进。

> **More Tasks:**

**更多任务：**

> * (1) Do not show the current search as a button, only the five preceding searches. Hint: Adapt the `getLastSearches` function.
* （1）仅以按钮形式展示之前的五个搜索，而当前搜索不需要展示为按钮形式。提示：适配 `getLastSearches` 函数。
> * (2) Don't show duplicated searches. Searching twice for "React" shouldn't create two different buttons. Hint: Adapt the `getLastSearches` function.
* （2）不要显示重复的搜索，搜索两次 "React" 不应该产生两个不同的按钮。提示：适配 `getLastSearches` 函数。
> * (3) Set the SearchForm component's input field value with the last search term if one of the buttons is clicked.
* （3）如果其中一个按钮被点击了，则将 `SearchForm` 组件的输入字段的值设置为最后一次搜索的内容。

> The source of the five rendered buttons is the `getLastSearches` function. There, we take the array of `urls` and return the last five entries from it. Now we'll change this utility function to return the last six entries instead of five, removing the last one. Afterward, only the five *previous* searches are displayed as buttons.

5 个渲染的按钮来自于 `getLastSearches` 函数，在这个函数里面，我们取数组 `urls`，并返回其中的最后 5 项。现在我们将修改一下这个函数，让其返回最后 6 项而不是 5 项。之后，只有 5 个*之前的*搜索结果会以按钮的形式展示出来。

{title="src/App.js",lang="javascript"}
~~~~~~~
const getLastSearches = urls =>
  urls
# leanpub-start-insert
    .slice(-6)
    .slice(0, -1)
# leanpub-end-insert
    .map(extractSearchTerm);
~~~~~~~

> If the same search is executed twice or more times in a row,  duplicate buttons appear, which is likely not your desired behavior. It would be acceptable to group identical searches into one button if they followed each other. We will solve this problem in the utility function as well. Before separating the array into the five previous searches, group the identical searches:

如果相同的内容连续搜索了多次，就会出现重复的按钮，这很可能不是你想要的行为。如果将连续且相同的搜索合并到一个按钮中，那就是可以接受的了。我们也将在这个函数中解决这个问题。在将数组分割成前 5 次搜索内容之前，先将相同的搜索内容进行分组：

{title="src/App.js",lang="javascript"}
~~~~~~~
const getLastSearches = urls =>
  urls
# leanpub-start-insert
    .reduce((result, url, index) => {
      const searchTerm = extractSearchTerm(url);

      if (index === 0) {
        return result.concat(searchTerm);
      }

      const previousSearchTerm = result[result.length - 1];

      if (searchTerm === previousSearchTerm) {
        return result;
      } else {
        return result.concat(searchTerm);
      }
    }, [])
# leanpub-end-insert
    .slice(-6)
    .slice(0, -1);
~~~~~~~

> The reduce function starts with an empty array as its `result`. The first iteration concats the `searchTerm` we extracted from the first `url` into the `result`. Every extracted `searchTerm` is compared to the one before it. If the previous search term is different from the current, concat the `searchTerm` to the result. If the search terms are identical, return the result without adding anything.

`reduce` 函数以一个空数组作为其 `result` 开始，第一次循环我们将从第一个 `url` 中提取 `searchTerm` 并合并到 `result` 中。每一次提取的 `searchTerm` 都会与之前的进行比较，如果之前的搜索词与当前的不同，则将这个 `searchTerm` 添加到 `result` 中。如果搜索词相同，则返回 `result` 并且不向其添加任何搜索词。

> Lastly, the SearchForm's input field should be set with the new `searchTerm` if one of the last search buttons is clicked. We can solve this using the state updater function for the specific value used in the SearchForm component.

最后，如果最后一个搜索按钮被点击了，`SearchForm` 的输入字段应该设置为新的 `searchTerm`。我们可以使用 `SearchForm` 组件中特定值的 state 更新函数来解决这个问题。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleLastSearch = searchTerm => {
# leanpub-start-insert
    setSearchTerm(searchTerm);
# leanpub-end-insert

    handleSearch(searchTerm);
  };

  ...
};
~~~~~~~

> Last, extract the feature's new rendered content from this section as a standalone component, to keep the App component lightweight:

最后，将该功能的新渲染内容提取出来，作为一个独立的组件，以保持 App 组件的轻量简洁：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const lastSearches = getLastSearches(urls);

  return (
    <div>
      ...

# leanpub-start-insert
      <LastSearches
        lastSearches={lastSearches}
        onLastSearch={handleLastSearch}
      />
# leanpub-end-insert

      ...
    </div>
  );
};

# leanpub-start-insert
const LastSearches = ({ lastSearches, onLastSearch }) => (
  <>
    {lastSearches.map((searchTerm, index) => (
      <button
        key={searchTerm + index}
        type="button"
        onClick={() => onLastSearch(searchTerm)}
      >
        {searchTerm}
      </button>
    ))}
  </>
);
# leanpub-end-insert
~~~~~~~

> This feature wasn't an easy one. Lots of fundamental React but also JavaScript knowledge was needed to accomplish it. If you had no problems implementing it yourself or to follow the instructions, you are very well set. If you had one or the other issue, don't worry too much about it. Maybe you even figured out another way to solve this task and it may have turned out simpler than the one I showed here.

这个功能并不容易。需要很多 React 的基础知识，同时也需要大量的 JavaScript 知识来完成它。如果你自己实现它没有遇到任何问题，或者按照说明去做，那么你就已经做的很好了。如果你遇到了这样或那样的问题，也不用太过担心，也许你可以想出另一种解决方法，结果甚至可能比我展示的更加简单。

### 练习：

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Remember-Last-Searches).
  * 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Reverse-Sort...hs/Remember-Last-Searches?expand=1).