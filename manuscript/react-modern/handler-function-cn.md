## Handler Function in JSX

## JSX 中的处理函数

> The App component still has the input field and label, which we haven't used. In HTML outside of JSX, input fields have an [onchange handler](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onchange). We're going to discover how to use onchange handlers with a React component's JSX. First, refactor the App component form a concise to block body so we can add implementation details.

我们还没使用到 App 组件里的输入框及其标签。在 JSX 之外的 HTML 中，input 有一个 [onchange 处理函数](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onchange)。我们将要学习如何在 React 组件中的 JSX 上使用 onchange 处理函数。让我们先把 App 组件从简洁体重构回块体，以便添加更多实现细节。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const App = () => {
  // do something in between

  return (
# leanpub-end-insert
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

      <hr />

      <List />
    </div>
# leanpub-start-insert
  );
};
# leanpub-end-insert
~~~~~~~

> Next, define a function -- which can be normal or arrow -- for the change event of the input field. In React, this function is called an **(event) handler**. Now the function can be passed to the `onChange` attribute (JSX named attribute) of the input field.

随后定义一个函数，普通函数或者箭头函数都可以，用来处理 input 的 change 事件。在 React 中，这种函数叫做 **（事件）处理函数**。现在可以把这个函数传给 input 的 `onChange` 属性了（JSX 命名的属性）。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
# leanpub-start-insert
  const handleChange = event => {
    console.log(event);
  };
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
# leanpub-start-insert
      <input id="search" type="text" onChange={handleChange} />
# leanpub-end-insert

      <hr />

      <List />
    </div>
  );
};
~~~~~~~

> After opening your application in a web browser, open the browser's developer tools to see logging occur after you type into the input field. This is called a **synthetic event** defined by a JavaScript object. Through this object, we can access the emitted value of the input field:

在浏览器中打开应用，再打开浏览器的开发者工具，就可以看到在输入框打字之后的日志了。这被称为**合成事件**，由一个 JavaScript 对象构成。通过这个对象，我们可以拿到输入框发出的值：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const handleChange = event => {
# leanpub-start-insert
    console.log(event.target.value);
# leanpub-end-insert
  };

  return ( ... );
};
~~~~~~~

> The synthetic event is essentially a wrapper around the [browser's native event](https://developer.mozilla.org/en-US/docs/Web/Events), with more functions that are useful to prevent native browser behavior (e.g. refreshing a page after the user clicks a form's submit button). Sometimes you will use the event, sometimes you won't need it.

合成事件本质上就是把[浏览器的原生事件](https://developer.mozilla.org/en-US/docs/Web/Events)包了一层，附加了一些有用的函数可以阻止浏览器默认行为（比如，用户点击表单提交按钮之后浏览器会刷新页面）。有时你会用到这个事件，有时不需要。


> This is how we give HTML elements in JSX handler functions to respond to user interaction. Always pass functions to these handlers, not the return value of the function, except when the return value is a function:

这就是如何通过给 HTML 元素附加 JSX 处理函数来响应用户交互的方式。只把函数传给这些处理函数，而不是函数的返回值，除非返回值也是一个函数：

{title="Code Playground",lang="javascript"}
~~~~~~~
// don't do this
<input
  id="search"
  type="text"
# leanpub-start-insert
  onChange={handleChange()}
# leanpub-end-insert
/>

// do this instead
<input
  id="search"
  type="text"
# leanpub-start-insert
  onChange={handleChange}
# leanpub-end-insert
/>
~~~~~~~

> HTML and JavaScript work well together in JSX. JavaScript in HTML can display objects, can pass JavaScript primitives to HTML attributes (e.g. `href` to `<a>`), and can pass functions to an element's attributes for handling events.

HTML 和 JavaScript 在 JSX 里合作地很愉快。HTML 中的 JavaScript 可以展示对象，可以传递 JavaScript 的基本类型给 HTML 属性（例如把 `href` 传给 `<a>`），也可以通过给元素属性传递函数来处理事件。

> I prefer using arrow functions because of their concision as event handlers, however, in a larger React component I see myself using the function statements too, because it gives them more visibility in contrast to other variable declarations within a component's body.

我个人更倾向于用箭头函数来定义事件处理函数，这样更简洁。但是在稍大的 React 组件中，我发现自己也会使用函数声明，因为这样能更好地和组件中其他的变量声明区分开来。

### 练习：

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Handler-Function-in-JSX).
>   * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Component-Definition...hs/Handler-Function-in-JSX?expand=1).
> * Read more about [React's events](https://reactjs.org/docs/events.html).

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Handler-Function-in-JSX)。
	* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Component-Definition...hs/Handler-Function-in-JSX?expand=1)。
* 阅读更多关于 [React的事件](https://reactjs.org/docs/events.html)的文章。
