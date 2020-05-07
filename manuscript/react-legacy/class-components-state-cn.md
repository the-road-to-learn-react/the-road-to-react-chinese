> ## React Class Components: State
## React 类组件：State

> Before React Hooks, class components were superior to function components because they could be stateful. With a class constructor, we can set an initial state for the component. Also, the component's instance (`this`) gives access to the current state (`this.state`) and the component's state updater method (`this.setState`):

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

> If the state has more than one property in its state object, the `setState` method performs only a shallow update. Only the properties passed to `setState` are overwritten, and all other properties in the state object stay intact. Since state management is important for frontend applications, there was no way around class components without hooks for function components.

如果 state 中有一个以上的属性，`setState` 方法只执行浅更新。只有传递给 `setState` 的属性被覆盖，而状态对象中的所有其它属性都保持不变。由于状态管理对于前端应用来说是很重要的，所以函数组件在没有 hook 的情况下，是无法达到类组件的功能的。

> In a React class component, there are two dedicated APIs (`this.state` and `this.setState`) to manage a component's state. In a function component, React's useState and useReducer hooks handle this. Related items are packed into one state hook, while a class component must use a general state API. This was one of the major reasons to introduce React Hooks, and move away from class components.

在 React 类组件中，有两个专门的 API（`this.state`和`this.setState`）来管理组件的 state。在函数组件中，React 的 useState 和 useReducer 钩子来处理。相关项被打包到一个状态 hook 中，而类组件必须使用通用状态 API。这也是引入 React Hooks，摆脱类组件的主要原因之一。

### 练习：

* 编写一个有状态的组件 （比如一个 Input 组件）。