## React 类组件：State

在 React Hooks 之前，类组件比函数组件更有优势，因为他们可以是有状态的。使用类构造函数，我们可以为组件设置一个初始状态。而且，组件的实例（`this`）可以访问当前 state（`this.state`）和组件的 state 更新函数（`this.setState`）：

{title="Code Playground",lang="javascript"}
~~~~~~~
class App extends React.Component {
  constructor(props) {
    super(props);

# leanpub-start-insert
    this.state = {
      searchTerm: 'React',
    };
# leanpub-end-insert
  }

  render() {
# leanpub-start-insert
    const { searchTerm } = this.state;
# leanpub-end-insert

    return (
      <div>
        <h1>My Hacker Stories</h1>

        <SearchForm
          searchTerm={searchTerm}
# leanpub-start-insert
          onSearchInput={() => this.setState({
            searchTerm: event.target.value
          })}
# leanpub-end-insert
        />
      </div>
    );
  }
}
~~~~~~~

如果 state 中有一个或多个属性，`setState` 方法只执行浅更新。只有传递给 `setState` 的属性被覆盖，而状态对象中的所有其它属性都保持不变。由于状态管理对于前端应用来说是很重要的，所以函数组件在没有 hook 的情况下，是无法达到类组件的功能的。

在 React 类组件中，有两个专门的 API（`this.state`和`this.setState`）来管理组件的 state。在函数组件中，React 的 useState 和 useReducer hooks 来处理。相关项被打包到一个状态 hook 中，而类组件必须使用通用状态 API。这也是引入 React Hooks，摆脱类组件的主要原因之一。

### 练习

* 编写一个有状态的组件 （比如一个 Input 组件）。
