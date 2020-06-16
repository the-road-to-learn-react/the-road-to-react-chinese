## React State

>React Props are used to pass information down the component tree; **React state** is used to make applications interactive. We'll be able to change the application's appearance by interacting with it.

React Props 用于沿着组件树向下传递信息；React State 用于实现应用交互。我们可以通过与应用的交互来改变它的表现。

>First, there is a utility function called useState that we take from React for managing state. The useState function is called a hook. There is more than one React hook -- related to state management but also other things in React -- and you will learn about them throughout the next sections. For now, let's focus on React's useState hook:

首先，我们可以从 React 中获得一个叫作 useState 的工具函数去管理状态，useState 函数被称作 hook。React 中有不止一个 hook -- 它们与 React 中的状态管理和其它事务有关 -- 你可以通过接下来的章节学习它们。现在，我们只关注 React 中的 useState hook。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  const [searchTerm, setSearchTerm] = React.useState('');
# leanpub-end-insert

  ...
};
~~~~~~~

>React's `useState` hook takes an *initial state* as an argument. We'll use an empty string, and the function will return an array with two values. The first value (`searchTerm`) represents the *current state*; the second value is a *function to update this state* (`setSearchTerm`). I will sometimes refer to this function as *state updater function*.

React的 useState hook 以一个初始状态值作为参数，我们可以使用空字符串。同时它会返回包含两个值的数组，第一个值(searchTerm)表示当前状态；第二个值(setSearchTerm)是一个更新这个状态的函数。我有时会把这个函数称作状态更新函数。

>If you are not familiar with the syntax of the two values from the returned array, consider reading about [JavaScript array destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring). It is used to read from an array more concisely. This is array destructuring and its benefits visualized in a nutshell:

如果你不熟悉这种一个数组返回两个值的语法，可以参考阅读[JavaScript数组解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring)的内容。它可以用于更简洁地读取数组。简而言之，这是数组的解构，并且其好处可以直观看到：


{title="Code Playground",lang="javascript"}
~~~~~~~
// basic array definition
const list = ['a', 'b'];

// no array destructuring
const itemOne = list[0];
const itemTwo = list[1];

// array destructuring
const [firstItem, secondItem] = list;
~~~~~~~

>In the case of React, the React `useState` hook is a function which returns an array. Take again the following JavaScript example as comparison:

在 React 中，useState hook 是返回一个数组的函数。再以下面的 JavaScript 示例作比较：

{title="Code Playground",lang="javascript"}
~~~~~~~
function getAlphabet() {
  return ['a', 'b'];
}

// no array destructuring
const itemOne = getAlphabet()[0];
const itemTwo = getAlphabet()[1];

// array destructuring
const [firstItem, secondItem] = getAlphabet();
~~~~~~~

>Array destructuring is just a shorthand version of accessing each item one by one. If you express it without the array destructuring in React, it becomes less readable:

数组解构只是逐个获取数组值的一种简写方式，如果你在 React 中不以解构的方式表示数组，那么它的可读性就会降低：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  // less readable version without array destructuring
  const searchTermState = React.useState('');
  const searchTerm = searchTermState[0];
  const setSearchTerm = searchTermState[1];

  ...
};
~~~~~~~

>The React team chose array destructuring because of its concise syntax and ability to name destructured variables. The following code snippet is an example of array destructuring:

React 团队之所以选择数组解构，是因为它语法简洁并且有命名解构后的变量的能力。下面的代码片段是一个销毁数组的例子：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  const [searchTerm, setSearchTerm] = React.useState('');
# leanpub-end-insert

  ...
};
~~~~~~~

>After we initialize the state and have access to the current state and the state updater function, use them to display the current state and update it within the App component's event handler:

在我们初始化状态，且可以获取当前状态和状态更新函数之后，我们就可以使用它们显示当前的状态并通过 APP 组件内部的事件处理函数对状态进行更新了。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
# leanpub-start-insert
    setSearchTerm(event.target.value);
# leanpub-end-insert
  };

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" onChange={handleChange} />

# leanpub-start-insert
      <p>
        Searching for <strong>{searchTerm}</strong>.
      </p>
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};
~~~~~~~

>When the user types into the input field, the input field's change event is captured by the handler with its current internal value. The handler's logic uses the state updater function to set the new state. After the new state is set in a component, the component renders again, meaning the component function runs again. The new state becomes the current state and can be displayed in the component's JSX.

当用户在输入框中输入内容时，输入框内容的改变会被当前状态值的处理函数捕获。事件处理函数的逻辑使用更新函数去设置新的状态值。在给组件设置了新的状态值之后，组件会被重新渲染，这意味着该组件函数会被再次执行。新的状态变为了当前的状态，并可以在组件的 JSX 中显示。


### 练习:

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-State).
  * 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Props...hs/React-State?expand=1).
* 阅读更多关于 [JavaScript数组解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring).
* 阅读更多关于 React 的 useState hook ([0](https://www.robinwieruch.de/react-usestate-hook), [1](https://reactjs.org/docs/hooks-state.html)), 使用它让你的 React 组件具有交互性。
