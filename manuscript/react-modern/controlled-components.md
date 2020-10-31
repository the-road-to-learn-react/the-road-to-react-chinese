## 受控组件

> **Controlled components** are not necessary React components, but HTML elements. Here, we'll learn how to turn the Search component and its input field into a controlled component.

**受控组件**并不一定是 React 组件，也可以是 HTML 元素。这一节我们将学习如何把 Search 组件以及内部的输入框转化为一个受控组件。

> Let's go through a scenario that shows  why we should follow the concept of controlled components throughout our React application. After applying the following change -- giving the `searchTerm` an initial state -- can you spot the mistake in your browser?

让我们看一个展示为什么我们需要在 React 应用中遵循受控组件概念的情景。在我们应用了以下改变之后：赋予 `searchTerm` 一个初始 state ，你能发现在你浏览器上出现的错误吗？

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 const stories = [ ... ];

# leanpub-start-insert
 const [searchTerm, setSearchTerm] = React.useState('React');
# leanpub-end-insert

 ...
};
~~~~~~~

> While the list has been filtered according to the initial search, the input field doesn't show the initial `searchTerm`. We want the input field to reflect the actual `searchTerm` used from the initial state; but it's only reflected through the filtered list.

当这个列表基于初始搜索被过滤的时候，输入框不会展示初始的 `searchTerm` 。我们期望输入框反应 `searchTerm` 的实际初始 state ，但它只反映了被过滤的列表。

> We need to convert the Search component with its input field into a controlled component. So far, the input field doesn't know anything about the `searchTerm`. It only uses the change event to inform us of a change. Actually, the input field has a `value` attribute.

因此我们需要把 Search 组件以及内部的输入框转化成一个受控组件。到目前为止，这个输入框还没有获知任何关于 `searchTerm` 的信息。它只使用了 change 事件来通知我们发生的改变。而实际上它还拥有一个 `value` 属性。


{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 const stories = [ ... ];

 const [searchTerm, setSearchTerm] = React.useState('React');

 ...

 return (
   <div>
     <h1>My Hacker Stories</h1>

# leanpub-start-insert
     <Search search={searchTerm} onSearch={handleSearch} />
# leanpub-end-insert

     ...
   </div>
 );
};

const Search = props => (
 <div>
   <label htmlFor="search">Search: </label>
   <input
     id="search"
     type="text"
# leanpub-start-insert
     value={props.search}
# leanpub-end-insert
     onChange={props.onSearch}
   />
 </div>
);
~~~~~~~

> Now the input field starts with the correct initial value, using the `searchTerm` from the React state. Also, when we change the `searchTerm`, we force the input field to use the value from React's state (via props). Before, the input field managed its own internal state natively with just HTML.

现在输入框开始用 React state 中的 `searchTerm` 作为正确的初始值。并且当我们改变 `searchTerm ` 的值时，我们强制输入框使用了 React state 中的值 （通过 props）。而之前输入框仅仅是以原生 HTML 的方式管理着自己的内部状态。

![](images/controlled-component.png)

> We learned about controlled components in this section, and, taking all the previous sections as learning steps into consideration, discovered another concept called **unidirectional data flow**:

我们在这一节学习了受控组件，再结合之前学习过程中的所有章节，可以发现另一个被称为**单向数据流**的概念。

{title="Visualization",lang="javascript"}
~~~~~~~
UI -> Side-Effect -> State -> UI -> ...
~~~~~~~

> A React application and its components start with an initial state, which may be passed down as props to other components. It's rendered for the first time as a UI. Once a side-effect occurs, like user input or data loading from a remote API, the change is captured in React's state. Once state has been changed, all the components affected by the modified state or the implicitly modified props are re-rendered (the component functions runs again).

一个 React 应用和其中的组件一开始都有一个初始 state, 可能通过 props 的方式向下传给其他组件。它会在第一次时被渲染成 UI 。一旦副作用发生，比如用户输入或者从远端 API 加载数据，这个改变会在 React state 中被捕获。而一旦 state 被改变，所有受到改变的 state 或者被暗中改变的 props 影响的组件将重新渲染（组件的函数会被重新调用）。

> In the previous sections, we also learned about React's **component lifecycle**. At first, all components are instantiated from the top to the bottom of the component hierarchy. This includes all hooks (e.g. `useState`) that are instantiated with their initial values (e.g. initial state). From there, the UI awaits side-effects like user interactions. Once state is changed (e.g. current state changed via state updater function from `useState`), all components affected by modified state/props render again.

在之前的章节中，我们也学习了 React 的 **组件生命周期**。首先所有组件将会在组件层级中从上往下地被实例化。这包含了所有被基于初始值（比如初始 state ）实例化的 hooks （比如 `useState`）。那时 UI 等待着诸如用户交互之类的副作用。一旦 state 发生了改变 （比如当前 state 被 `useState` 中的更新 state 的函数改变），所有受到改变的 state 或者 props 影响的组件将会重新渲染。

> Every run through a component's function takes the *recent value* (e.g. current state) from the hooks and *doesn't* reinitialize them again (e.g. initial state). This might seem odd, as one could assume the `useState` hooks function re-initializes again with its initial value, but it doesn't. Hooks initialize only once when the component renders for the first time, after which React tracks them internally with their most recent values.

每一次运行一个组件的函数会从 hooks 中使用*最新值*（比如当前 state ）并且*不会*重新执行初始化（比如初始 state）。这可能会显得有些奇怪，因为有人可能会假定 `useState` hooks 函数会用初始值重新初始化，但其实并没有。Hooks 只会在组件第一次渲染时初始化一次，而之后 React 会在内部追踪其最新值。

### 练习：

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Controlled-Components).
>  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Lifting-State-in-React...hs/React-Controlled-Components?expand=1).
> * Read more about [controlled components in React](https://www.robinwieruch.de/react-controlled-components/).
> * Experiment with `console.log()` in your React components and observe how your changes render, both initially and after the input field changes.

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Controlled-Components)。
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Lifting-State-in-React...hs/React-Controlled-Components?expand=1)。
* 阅读更多关于[ React 受控组件](https://www.robinwieruch.de/react-controlled-components/)的文章。
* 尝试在你的 React 组件中通过 `console.log()` 观察你的改变是怎么渲染的，包括初始时和输入栏改变之后。
