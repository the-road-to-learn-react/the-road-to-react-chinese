## JSX 回调处理函数

接下来，我们将专注在输入框和标签上，通过分离一个独立的 Search 组件，并在 App 组件中创建一个实例。通过这个过程，Search 组件将成为 List 组件的兄弟组件，反之亦然。我们还将把处理函数和 state 移动到 Search 组件内来保持我们功能的完整性。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <Search />
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};

# leanpub-start-insert
const Search = () => {
  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
    setSearchTerm(event.target.value);
  };

  return (
    <div>
      <label htmlFor="search">Search: </label>
      <input id="search" type="text" onChange={handleChange} />

      <p>
        Searching for <strong>{searchTerm}</strong>.
      </p>
    </div>
  );
};
# leanpub-end-insert
~~~~~~~

我们有一个抽取出的 Search 组件，它可以处理 state 并且显示 state 但没有暴露出它的内容。该组件将 `searchTerm` 显示为文本，但并未与父组件或兄弟组件共享此信息。由于 Search 组件除了显示搜索项外什么也不做，因此它对其他组件毫无用处。

![](images/callback-handler.png)

由于 props 只能往下传递，因此无法将 JavaScript 数据信息沿组件树往上传递。但是，我们可以引入**回调函数**：回调函数被引入 （A），在其他地方使用了回调函数（B），但调用在回调函数的位置 （C）。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  // A
  const handleSearch = event => {
    // C
    console.log(event.target.value);
  };
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <Search onSearch={handleSearch} />
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};

# leanpub-start-insert
const Search = props => {
# leanpub-end-insert
  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
    setSearchTerm(event.target.value);

# leanpub-start-insert
    // B
    props.onSearch(event);
# leanpub-end-insert
  };

  return ( ... );
};
~~~~~~~

在您的代码中使用可以省略掉注释 A，B 和 C，因为它们只是在提醒每个代码块要执行的任务。考虑一下回调函数的概念：我们将一个函数从一个组件（App）传递到另一个组件（Search）；我们在第二个组件（Search）中调用它；但实际上它在第一个组件（App）中被执行。这样，我们就能在组件树中往上传递信息。一个组件中使用的处理函数成为回调处理函数，并将该函数通过 React props 往下传递给其他组件。React props 在组件树中始终将信息往下传递，而回调函数作为 props 传递时可以用来和上层组件通信。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Callback-Handler-in-JSX)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-State...hs/Callback-Handler-in-JSX?expand=1)。
* 根据需要多次访问处理函数和回调处理函数的概念。
