## React 获取数据

我们目前正在获取数据，但它仍然是来自我们自己用 Promise 设置的假数据。到现在为止关于异步 React 和高级 State 管理的课程都在为我们从真正的第三方 API 中获取数据做准备。我们将使用可靠且丰富的 [Hacker News API](https://hn.algolia.com/api) 来请求流行的科技新闻。

我们将直接从 API 中获取数据， 而不是使用 `initialStories` 数组和 `getAsyncStories` 函数(你可以删除这些)。

{title="src/App.js",lang="javascript"}
~~~~~~~
// A
# leanpub-start-insert
const API_ENDPOINT = 'https://hn.algolia.com/api/v1/search?query=';
# leanpub-end-insert

const App = () => {
  ...

  React.useEffect(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    fetch(`${API_ENDPOINT}react`) // B
      .then(response => response.json()) // C
# leanpub-end-insert
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-start-insert
          payload: result.hits, // D
# leanpub-end-insert
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, []);

  ...
};
~~~~~~~

首先，`API_ENDPOINT` (A) 用于获取热门科技新闻的某个查询(搜索主题)。在本示例中，我们获取 React 相关的新闻。其次，用浏览器原生的 [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 来执行这个请求 (B), 对于 fetch API，需要将返回数据翻译成 JSON (C)。最后，返回的结果遵循不同的数据结构 (D)， 我们将其作为有效数据发送给我们的组件 state。

在上面的代码示例中，我们使用了 [JavaScript 的模版字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) 进行字符串插值。在 JavaScript 中没有这个功能的时候，我们会在字符串上使用 + 运算符来代替。

{title="Code Playground",lang="javascript"}
~~~~~~~
const greeting = 'Hello';

// + operator
const welcome = greeting + ' React';
console.log(welcome);
// Hello React

// template literals
const anotherWelcome = `${greeting} React`;
console.log(anotherWelcome);
// Hello React
~~~~~~~

检查你的浏览器，查看从 Hacker News API 中获取的初始查询的相关内容。由于我们对样本内容使用了相同的数据结构，所以我们不需要改变什么，用搜索功能获取数据后仍然可以对它们进行过滤。我们将在下一节中改变这个行为。对于 App 组件来说，这里没有太多的数据获取需要实现，虽然这都是学习如何在 React 中把异步数据作为 state 管理的一部分。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Data-Fetching-with-React)
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Impossible-States...hs/Data-Fetching-with-React?expand=1)
* 通读 [Hacker News](https://news.ycombinator.com/) 和它的 [API](https://hn.algolia.com/api)
* 阅读更多关于连接到远程 API 的 [浏览器原生 fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
* 阅读更多关于 [JavaScript 模板字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)。
  