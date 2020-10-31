## 命令式的 React

> In a React function component, React's useRef Hook is used mostly for imperative programming. Throughout React's history, the *ref* and its usage had a few versions:

在 React 函数组件中，React 的 useRef Hook 主要用于命令式编程。在 React 的历史上，*ref* 及其用法有几个版本：

* String Refs (已弃用)
* Callback Refs
* createRef Refs (类组件专属)
* useRef Hook Refs (函数式组件专属)

> Recently, the React team introduced **React's createRef** as the  latest equivalent to a function component's useRef hook:

最近，React 团队引入了**React 的 createRef**，作为最新的相当于函数组件的 useRef 钩子：

{title="Code Playground",lang="javascript"}
~~~~~~~
class InputWithLabel extends React.Component {
  constructor(props) {
    super(props);

# leanpub-start-insert
    this.inputRef = React.createRef();
# leanpub-end-insert
  }

  componentDidMount() {
    if (this.props.isFocused) {
# leanpub-start-insert
      this.inputRef.current.focus();
# leanpub-end-insert
    }
  }

  render() {
    ...

    return (
      <>
        ...
        <input
# leanpub-start-insert
          ref={this.inputRef}
# leanpub-end-insert
          id={id}
          type={type}
          value={value}
          onChange={onInputChange}
        />
      </>
    );
  }
}
~~~~~~~

> With the helper function, the ref is created in the class' constructor, applied in the JSX for the `ref` attributed, and here used in a lifecycle method. The ref can also be used elsewhere, like focusing the input field on a button click.

通过工具函数，在类的构造函数中创建 `ref`，在 JSX 中应用 `ref`，这里用在生命周期方法中使用。`ref` 也可以用在其他地方，比如将输入字段聚焦在按钮点击上。

> Where createRef is used in React's class components, React's useRef Hook is used in React function components. As React shifts towards function components, today its common practice to use the useRef hook exclusively to manage refs and apply imperative programming principles.

在 React 的类组件中使用 createRef，而在 React 的函数组件中则使用 React 的 useRef Hook。随着 React 向函数组件的转变，如今其通常的做法是专门使用 useRef 钩子来管理 refs，并应用命令式编程原则。

### 练习：

* 了解更多 [React 中不同的 Ref 技术](https://reactjs.org/docs/refs-and-the-dom.html)。