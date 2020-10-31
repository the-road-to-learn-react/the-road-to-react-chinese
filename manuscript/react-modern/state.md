## React State

React Props 用于沿着组件树向下传递信息；**React State** 用于实现应用交互。我们可以通过与应用的交互来改变它的表现。

首先，我们可以从 React 中获得一个叫作 useState 的工具函数去管理 state，useState 函数被称作 hook。React 中有不止一个 hook，它们与 React 中的 state 管理和其它事务有关，你可以通过接下来的章节学习它们。现在，我们只关注 React 中的 useState hook。

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

React的 `useState` hook 以一个*初始 state* 作为参数，我们可以使用空字符串。同时它会返回包含两个值的数组，第一个值（`searchTerm`）表示*当前 state*；第二个值（`setSearchTerm`）是一个*更新这个 state 的函数*。我有时会把这个函数称作 *state 更新函数*。

如果你不熟悉这种一个数组返回两个值的语法，可以参考阅读 [JavaScript 数组解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring)的内容。它可以用于更简洁地读取数组。简而言之，这是数组的解构，并且其好处可以直观看到：


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

在 React 中，`useState` hook 是返回一个数组的函数。再以下面的 JavaScript 示例作比较：

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

在我们初始化 state，且可以获取当前 state 和 state 更新函数之后，我们就可以使用它们显示当前的 state 并通过 APP 组件内部的事件处理函数对其进行更新了。

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

当用户在输入框中输入内容时，输入框内容的改变会被处理函数以当前内部值捕获。事件处理函数的逻辑使用 state 更新函数去设置新的 state。在给组件设置了新的 state 之后，组件会被重新渲染，这意味着该组件函数会被再次执行。新的 state 变为了当前的 state，并可以在组件的 JSX 中显示。

### 练习

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-State)。
* 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Props...hs/React-State?expand=1)。
* 阅读更多关于 [JavaScript数组解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring)。
* 阅读更多关于 React 的 useState hook ([0](https://www.robinwieruch.de/react-usestate-hook), [1](https://reactjs.org/docs/hooks-state.html)), 使用它让你的 React 组件具有交互性。
