# React's Legacy
# React 的遗留问题

> React has changed a lot since 2013. The iterations of its library, how React applications are written, and especially its components have all changed drastically. However, many React applications were built over the last few years, so not everything was created with the current status quo in mind. This section of the book covers React's legacy.

自2013年以来，React 发生了很多变化。它的库的迭代，React 应用的编写方式，尤其是它的组件都发生了翻天覆地的变化。然而，许多 React 应用都是在过去几年中构建的，所以并不是所有的东西都是按照目前的情况来创建的。本书的这一节主要介绍了 React 的遗留问题。

> I won't cover all that's considered legacy in React, because some features have been revamped more than once. You may see the previous iteration of the feature in older React applications, but will probably be different than the current.

我不会涵盖所有被认为是 React 中的遗留功能，因为有些功能已经不止一次被改造过。你可能会在老的 React 应用中看到这个功能的上一个迭代，但可能会和现在的不同。

> Throughout this section we will compare a [modern React application](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-modern-final) to its [legacy version](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-legacy). We'll discover that most differences between modern and legacy React are due to class components versus function components.

在本节中，我们将对一个[现代 React 应用](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-modern-final)和它的[遗留版本](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-legacy)进行比较。我们会发现，现代 React 和传统 React 之间的大部分差异都是由于类组件和函数组件造成的。

> ## React Class Components
## React 类组件

> React components have undergone many changes, from **createClass components** over **class components**, to **function components**. Going through a React application today, it's likely that we'll see class components next to the modern function components.

React 组件经历类很多变化，从 **craeteClass components**到**class components**，再到**function components**。今天再翻开 React 应用看看，我们很可能会在现代到函数组件看到类组件。

{title="Code Playground",lang="javascript"}
~~~~~~~
class InputWithLabel extends React.Component {
  render() {
    const {
      id,
      value,
      type = 'text',
      onInputChange,
      children,
    } = this.props;

    return (
      <>
        <label htmlFor={id}>{children}</label>
        &nbsp;
        <input
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

> A typical class component is a JavaScript class with a mandatory **render method** that returns the JSX. The class extends from a `React.Component` to inherit ([class inheritance](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming))) all React's component features (e.g. state management for state, lifecycle methods for side-effects). React props are accessed via the class instance (`this`).

一个典型的类组件是一个带有强制**render 方法**的 JavaScript 类，该类返回 JSX。该类从`React.Component`中扩展出来的，继承（[类的继承](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)）所有的 React 的组件特性（状态的管理，副作用的生命周期方法）。React props 通过类实例（`this`）来访问。

> For a while class components were the popular choice for writing React applications. Eventually, function components were added, and both co-existed with their distinct purposes side by side:

有一段时间，类组件是编写 React 应用的首选。最终，当函数组件被加入后，二者因其不同的目的共存着。

{title="Code Playground",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
  children,
}) => (
  <>
    <label htmlFor={id}>{children}</label>
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

> If no side-effects and no state were used in legacy apps, we'd use a function component instead of a class component. Before 2018--before React Hooks were introduced--React's function components couldn't handle side-effects (`useEffect` hooks) or state (`useState`/`useReducer` hooks). As a result, these components were known as **functional stateless components**,  there only to input props and output JSX. To use state or side-effects, it was necessary to refactor from a function component to a class component. When neither state nor side-effects were used, we used class components or the more lightweight function component.

如果在传统的 App 中不使用副作用和 state，我们会使用函数组件而不是类组件。在 2018 年 React Hooks 引入之前，React 的函数组件无法处理副作用（`useEffect` hook）或状态（`useState`/`useReducer` hook）。因此，这些组件被称为**无状态函数组件**，只能在组件中输入 props 和输出 JSX。为了使用 state 或副作用，必须从函数组件重构到类组件。当状态和副作用都没有使用时，我们就使用类组件或更轻量级都函数组件。

> With the addition of React Hooks, function components worked the same as class components, with state and side-effects. And since there was no longer any practical difference between them, the community chose function components since they are more lightweight.

加入 React Hooks 后，函数组件的工作原理和类组件一样，有 state 和副作用。而且由于它们之间已经没有实际的区别，并且它们更轻量级，所以社区选择来函数组件。

### 练习：

* 了解更多 [JavaScript 中的类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)。
* 了解更多 [如何将类组件重构成函数组件](https://www.robinwieruch.de/react-hooks-migration)。
* 了解更多关于 [类组件语法](https://github.com/the-road-to-learn-react/react-alternative-class-component-syntax) 这种语法并不流行，但更有效。
* 了解更多 [深入类组件](https://reactjs.org/docs/react-component.html)。
* 了解更多 [React 中的其它遗留问题和现代组件类型](https://www.robinwieruch.de/react-component-types)。
