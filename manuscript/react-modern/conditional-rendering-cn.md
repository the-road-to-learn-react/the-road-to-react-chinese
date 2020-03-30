> ## React Conditional Rendering

## React 条件渲染

> Handling asynchronous data in React leaves us with conditional states: with data and without data. This case is already covered, though, because our initial state is an empty list rather than `null`. If it was null, we'd have to handle this issue in our JSX. However, since it's `[]`, we `filter()` over an empty array for the search feature, which leaves us with an empty array. This leads to rendering nothing in the List component's `map()` function.

在 React 中处理异步数据使我们处于条件状态：有数据，无数据。这种情况已经覆盖，由于我们的初始 state 是一个空列表而不是 `null`，如果是 `null`，我们则必须在 JSX 中处理这个问题。但是，由于它是 `[]`, 我们对用于搜索的空数组进行了 `filter()` 处理，依然返回一个空数组。这导致在 List 组件的 `map()` 函数中不渲染任何内容。

> In a real world application, there are more than two conditional states for asynchronous data, though. Consider showing users a loading indicator when data loading is delayed:

但在实际的应用程序中，异步数据通常有两个以上的条件状态。考虑延迟数据加载时向用户显示加载指示器：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState([]);
# leanpub-start-insert
  const [isLoading, setIsLoading] = React.useState(false);
# leanpub-end-insert

  React.useEffect(() => {
# leanpub-start-insert
    setIsLoading(true);
# leanpub-end-insert

    getAsyncStories().then(result => {
      setStories(result.data.stories);
# leanpub-start-insert
      setIsLoading(false);
# leanpub-end-insert
    });
  }, []);

  ...
};
~~~~~~~

> With [JavaScript's ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator), we can inline this conditional state as a **conditional rendering** in JSX:

使用[JavaScript 三元运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator), 我们可以将该条件状态在 JSX 中内联为**条件渲染**

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {isLoading ? (
        <p>Loading ...</p>
      ) : (
# leanpub-end-insert
        <List
          list={searchedStories}
          onRemoveItem={handleRemoveStory}
        />
# leanpub-start-insert
      )}
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

> Asynchronous data comes with error handling, too. It doesn't happen in our simulated environment, but there could be errors if we start fetching data from another third-party API. Introduce another state for error handling and solve it in the promise's `catch()` block when resolving the promise:

异步数据有错误处理，在我们的模拟环境中不会发生这种情况，但是如果我们开始从另一个第三方 API 获取数据，则可能会出错。引入另一个 state 来处理错误状态，并在 Promise 完成时在 Promise 的 `catch()` 中处理它。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState([]);
  const [isLoading, setIsLoading] = React.useState(false);
# leanpub-start-insert
  const [isError, setIsError] = React.useState(false);
# leanpub-end-insert

  React.useEffect(() => {
    setIsLoading(true);

    getAsyncStories()
      .then(result => {
        setStories(result.data.stories);
        setIsLoading(false);
      })
# leanpub-start-insert
      .catch(() => setIsError(true));
# leanpub-end-insert
  }, []);

  ...
};
~~~~~~~

> Next, give the user feedback in case something went wrong with another conditional rendering. This time, it's either rendering something or nothing. So instead of having a ternary operator where one side returns `null`, use the logical `&&` operator as shorthand:

接下来，为用户提供反馈，以防其他条件渲染出现问题。这次，它要么渲染一些内容，要么什么都不渲染。用 `&&` 简写的形式来避免在三元运算符的另一测返回 `null`。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {isError && <p>Something went wrong ...</p>}
# leanpub-end-insert

      {isLoading ? (
        <p>Loading ...</p>
      ) : (
        ...
      )}
    </div>
  );
};
~~~~~~~

> In JavaScript, a `true && 'Hello World'` always evaluates to 'Hello World'. A `false && 'Hello World'` always evaluates to false. In React, we can use this behaviour to our advantage. If the condition is true, the expression after the logical `&&` operator will be the output. If the condition is false, React ignores it and skips the expression.

在 JavaScript 中， `true && 'Hello World'` 总是返回 'Hello world'。`false && 'Hello World'` 总是返回 false。在 React 中，我们可以利用这种行为来获得优势。如果条件为 true， 则逻辑 `&&` 运算符之后的表达式将作为输出。如果条件是 false，React 将忽略它并跳过表达式。

> Conditional rendering is not just for asynchronous data though. The simplest example of conditional rendering is a boolean flag state that's toggled with a button. If the boolean flag is true, render something, if it is false, don't render anything.

条件渲染不仅适用于异步数据，最简单的例子是使用按钮切换一个布尔标志的 state，如果表示是 true，则渲染某些内容，如果是 false，则不渲染任何内容。

> This feature can be quite powerful, because it gives you the ability to conditionally render JSX. It's yet another tool in React to make your UI more dynamic. And as we've discovered, it's often necessary for more complex control flows like asynchronous data.

这个功能非常强大，因为它使您能够有条件地呈现 JSX。它是 React 中另一个使您的 UI 更加动态的工具。正如我们所发现的，对于异步数据等更复杂的控制流而言，通常是必需的。

> ### Exercises:

### 练习：

>* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Conditional-Rendering).
>  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Asynchronous-Data...hs/React-Conditional-Rendering?expand=1).
> * Read more about [conditional rendering in React](https://www.robinwieruch.de/conditional-rendering-react/).

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Conditional-Rendering)。
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Asynchronous-Data...hs/React-Conditional-Rendering?expand=1)。
* 阅读更多[React 条件渲染](https://www.robinwieruch.de/conditional-rendering-react/)。