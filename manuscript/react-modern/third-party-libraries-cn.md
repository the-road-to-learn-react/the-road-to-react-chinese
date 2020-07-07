> ## Third-Party Libraries in React

## React中的第三方库

> We previously introduced the native fetch API to complete requests to the Hacker News API, which the browser provides. Not all browsers support this, however, especially the older ones. Also, once you start testing your application in a [headless browser environment](https://en.wikipedia.org/wiki/Headless_browser), issues can arise with the fetch API. There are a couple of ways to make fetch work in older browsers ([polyfills](https://en.wikipedia.org/wiki/Polyfill_(programming))) and in tests (isomorphic fetch), but these concepts are a bit off-task for the purpose of this learning experience.

此前，我们引入了原生浏览器提供的 fetch API，完成对 Hacker News API 的请求。然而不是所有浏览器都支持这个 API，特别是一些老版本的浏览器。另外，一旦你开始在[无头浏览器环境](https://en.wikipedia.org/wiki/Headless_browser) 中测试你的应用，fetch API 就会出现问题。这里有几种方式可以在旧版本浏览器上使用 fetch [polyfills](https://en.wikipedia.org/wiki/Polyfill_(programming)))和测试（同构式 fetch），但是这些概念对现在来说有点偏离主题。

> One alternative is to substitute the native fetch API with a stable library like [axios](https://github.com/axios/axios), which performs asynchronous requests to remote APIs. In this section, we will discover how to substitute a library--a native API of the browser in this case--with another library from the npm registry. First, install axios on the command line:

一个替代方案是用一个稳定的库如 [axios](https://github.com/axios/axios) 来替代原生 fetch API ，它执行对远程 API 的异步请求。在本节中，我们将学习怎样使用 npm 注册表中的一个库来替换浏览器原生的 API。首先，在命令行安装 axios：

{title="Command Line",lang="text"}
~~~~~~~
npm install axios
~~~~~~~

> Second, import axios in your App component's file:

然后，将 axios 引入到 App 组件文件里：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert

...
~~~~~~~

> You can use `axios` instead of `fetch`, and its usage looks almost identical to the native fetch API. It takes the URL as an argument and returns a promise. You don't have to transform the returned response to JSON anymore, since axios wraps the result into a data object in JavaScript. Just make sure to adapt your code to the returned data structure:

使用 `axios` 代替 `fetch`，它的使用看起来与原生 fetch API 几乎相同。它将 URL 作为一个参数，并返回一个 promise。你不需要将返回的结果转换成 JSON，因为 axios 把结果包装成了 JavaScript 的数据对象。你只是需要确保你的代码适配返回的数据结构：
{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    axios
      .get(url)
# leanpub-end-insert
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-start-insert
          payload: result.data.hits,
# leanpub-end-insert
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, [url]);

  ...
};
~~~~~~~

> In this code, we call axios `axios.get()` for an explicit [HTTP GET request](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET), which is the same HTTP method we used by default with the browser's native fetch API. You can use other HTTP methods such as HTTP POST with `axios.post()`as well.

在代码中，我们调用 axios `axios.get()` 用于一个显式 [HTTP GET 请求](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET)，这也是我们在浏览器的原生 fetch API 中默认使用的 HTTP 方法。你可以使用其他 HTTP 方法，比如使用 `axios.post()` 发起 HTTP POST 请求。

> We can see with these examples that axios is a powerful library for performing requests to remote APIs. I recommend over the native fetch API when requests become complex, working with older browser, or for testing.

通过这些例子，我们可以看到 axios 是一个执行远程 API 请求的强大的库。当请求变得复杂的时候或与老版本浏览器一起工作，或者用于测试时，相比原生 fetch API， 我更推荐使用 axios。

> ### Exercises:

### 练习：


> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Third-Party-Libraries-in-React).
  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Explicit-Data-Fetching-with-React...hs/Third-Party-Libraries-in-React?expand=1).
  * Read more about [popular libraries in React](https://www.robinwieruch.de/react-libraries).
  * Read more about [why frameworks matter](https://www.robinwieruch.de/why-frameworks-matter).
  * Read more about [axios](https://github.com/axios/axios).

> * 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Third-Party-Libraries-in-React)
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Explicit-Data-Fetching-with-React...hs/Third-Party-Libraries-in-React?expand=1)
  * 阅读更多关于 [React 中流行的库 ](https://www.robinwieruch.de/react-libraries)
  * 阅读更多关于 [为什么框架重要](https://www.robinwieruch.de/why-frameworks-matter).
  * 阅读更多关于 [axios](https://github.com/axios/axios).