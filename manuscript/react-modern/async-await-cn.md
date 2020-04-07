## Async/Await in React (Advanced)

## React 中的 Async / Await （高阶）

> You'll work with asynchronous data often in React, so it's good to know alternative syntax for handling promises: async/await. The following refactoring of the `handleFetchStories` function without error handling shows how:

在 React 里经常需要处理异步数据，所以了解 promise 的另外一种语法形式是大有裨益的，也就是：async / await。下面这段对 `handleFetchStories` 的重构展示了如何使用它们，不包含错误处理：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleFetchStories = React.useCallback(async () => {
# leanpub-end-insert
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    const result = await axios.get(url);
# leanpub-end-insert

# leanpub-start-insert
    dispatchStories({
      type: 'STORIES_FETCH_SUCCESS',
      payload: result.data.hits,
    });
# leanpub-end-insert
  }, [url]);

  ...
};
~~~~~~~

> To use async/await, our function requires the `async` keyword. Once you start using the `await` keyword, everything reads like synchronous code. Actions after the `await` keyword are not executed until promise resolves, meaning the code will wait.

想使用 async / await，需要给函数加 `async` 关键字。一旦开始使用 `await` 关键字，代码读起来就像同步的一样了。`await` 关键字后面的动作会等到 promise 正常返回后才会执行，也就是说代码会等待：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(async () => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    try {
# leanpub-end-insert
      const result = await axios.get(url);

      dispatchStories({
        type: 'STORIES_FETCH_SUCCESS',
        payload: result.data.hits,
      });
# leanpub-start-insert
    } catch {
      dispatchStories({ type: 'STORIES_FETCH_FAILURE' });
    }
# leanpub-end-insert
  }, [url]);

  ...
};
~~~~~~~

> To include error handling as before, the `try` and `catch` blocks are there to help. If something goes wrong in the `try` block,  the code will jump into the `catch` block to handle the error. `then`/`catch` blocks and async/await with `try`/`catch` blocks are both valid for handling asynchronous data in JavaScript and React.

如果需要和以前一样加入错误处理，则要用到 `try` / `catch` 语句。如果 `try` 里面出错了的话，代码会跳到 `catch` 里做错误处理。`then` / `catch` 语法和 async / await 里的 `try` / `catch` 语法在 JavaScript 和 React 里处理异步数据是同样有效的。

### 练习：

* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Async-Await-in-React).
  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Third-Party-Libraries-in-React...hs/Async-Await-in-React?expand=1).
* Read more about [data fetching in React](https://www.robinwieruch.de/react-hooks-fetch-data).
* Read more about [async/await in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function).

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Async-Await-in-React)。
	* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Third-Party-Libraries-in-React...hs/Async-Await-in-React?expand=1)。
* 阅读更多关于 [React 中的数据获取](https://www.robinwieruch.de/react-hooks-fetch-data)的文章。
* 阅读更多 [JavaScript 中的 async / await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 的文章。