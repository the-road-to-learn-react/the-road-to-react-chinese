## Imperative React

## 指令式 React

>  React is inherently declarative, starting with JSX and ending with hooks. In JSX, we tell React *what* to render and not *how* to render it. In a React side-effect Hook (useEffect), we express when to achieve *what* instead of *how* to achieve it. Sometimes, however, we'll want to access the rendered elements of JSX imperatively, in cases such as these:

React 天生是声明式的，从 JSX 开始一直到 hook。我们告诉 React 去渲染*什么*，而不是*如何*去渲染。在使用 React 管理副作用的 hook 时（useEffect），我们表述的是什么时候去实现*什么*事情，而不是*如何*去实现。然而，有些时候我们希望通过指令式的方式去访问渲染好的 JSX 元素，比如下列这些场景：

>* read/write access to elements via the DOM API:
>	* measure (read) an element's width or height
>	* setting (write) an input field's focus state
>* implementation of more complex animations:
>	* setting transitions
>	* orchestrating transitions
>* integration of third-party libraries:
>	* [D3](https://d3js.org/) is a popular imperative chart library

* 通过 DOM API 对元素做读写操作：
  * 测量（读）一个元素的宽或高
  * 设置（写）一个输入框的聚焦状态
* 实现更加复杂的动画效果：
  * 设置过场动画
  * 串联多个动画
* 集成三方库：
  * [D3](https://d3js.org/)：一个常见的指令式图表库

> Because imperative programming in React is often verbose and counterintuitive, we'll walk only through a small example for setting the focus of an input field imperatively. For the declarative way, simply set the input field's autofocus attribute:

因为 React 中的指令式编程大多数时候是啰嗦且反直觉的，我们这里就只举一个小例子，如何用指令式的方式把焦点设在输入框上。如果用声明式，只需要简单地设置输入框的自动聚焦（autofocus）属性：

{title="src/App.js",lang="javascript"}

~~~~~~~
const InputWithLabel = ({ ... }) => (
  <>
    <label htmlFor={id}>{children}</label>
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
# leanpub-start-insert
      autoFocus
# leanpub-end-insert
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

> This works, but only if one of the reusable components is rendered once. For instance, if the App component renders two InputWithLabel components, only the last rendered component receives the auto-focus on its render. However, since we have a reusable React component here, we can pass a dedicated prop and let the developer decide whether its input field should have an autofocus or not:

这样就可以工作了，不过仅限于这些可复用的组件中的一个，且渲染一次的时候。举例来说，如果 App 组件渲染了两个 InputWithLabel 组件，只有最后一个被渲染的组件在它渲染的时候会得到自动聚焦。不过既然这是一个可复用的 React 组件，我们可以传一个特定的属性，让开发者决定他的输入框是否应该被自动聚焦：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <InputWithLabel
        id="search"
        value={searchTerm}
# leanpub-start-insert
        isFocused
# leanpub-end-insert
        onInputChange={handleSearch}
      >
        <strong>Search:</strong>
      </InputWithLabel>

      ...
    </div>
  );
};
~~~~~~~

> Using just `isFocused` as an attribute is equivalent to `isFocused={true}`. Within the component, use the new prop for the input field's `autoFocus` attribute:

`isFocused` 和 `isFocused={true}` 是等价的。在组件内部，使用这个新的 prop 作为 `autoFocus` 属性的值：

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
# leanpub-start-insert
  isFocused,
# leanpub-end-insert
  children,
}) => (
  <>
    <label htmlFor={id}>{children}</label>
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
# leanpub-start-insert
      autoFocus={isFocused}
# leanpub-end-insert
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

> The feature works, yet it's still a declarative implementation. We are telling React *what* to do and not *how* to do it. Even though it's possible to do it with the declarative approach, let's refactor this scenario to an imperative approach. We want to execute the `focus()` method programmatically via the input field's DOM API once it has been rendered:

功能已经有了，但这仍然是声明式的实现方式。我们在告诉 React 去做*什么*，而不是*如何*去做。尽管可以用声明式实现，我们还是尝试把这里重构成指令式的。我们希望通过程序调用输入框 DOM API 的 `focus()` 方法，在它被渲染之后：

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
  isFocused,
  children,
}) => {
# leanpub-start-insert
  // A
  const inputRef = React.useRef();
# leanpub-end-insert

# leanpub-start-insert
  // C
  React.useEffect(() => {
    if (isFocused && inputRef.current) {
      // D
      inputRef.current.focus();
    }
  }, [isFocused]);
# leanpub-end-insert

  return (
    <>
      <label htmlFor={id}>{children}</label>
      &nbsp;
# leanpub-start-insert
       {/* B */}
# leanpub-end-insert
      <input
# leanpub-start-insert
        ref={inputRef}
# leanpub-end-insert
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
      />
    </>
  );
};
~~~~~~~

> All the essential steps are marked with comments that are explained step by step:

> * (A) First, create a `ref` with **React's useRef hook**. This `ref` object is a persistent value which stays intact over the lifetime of a React component. It comes with a property called `current`, which, in contrast to the `ref` object, can be changed.
> * (B) Second, the `ref` is passed to the input field's JSX-reserved `ref` attribute and the element instance is assigned to the changeable `current` property.
> * (C) Third, opt into React's lifecycle with React's useEffect Hook, performing the focus on the input field when the component renders (or its dependencies change).
> * (D) And fourth, since the `ref` is passed to the input field's `ref` attribute, its `current` property gives access to the element. Execute its focus programmatically as a side-effect, but only if `isFocused` is set and the `current` property is existent.

所有必要的操作都用注释标记出来了，这里会一步一步讲解：

* (A) 首先，使用 **React useRef hook** 创建一个 `ref` （引用）。这个 `ref` 对象是一个持久的值，在整个 React 组件生命周期里不会受到影响。它有一个 `current` （当前）属性，和 `ref` 对象本身正好相反，它是可以被更改的。
* (B) 其次，`ref` 通过 JSX 保留的 `ref` 标签传给输入框，这个元素实例就会被赋给可变的 `current` 属性。
* (C) 再次，使用 React useEffect Hook 进入 React 的生命周期，当组件渲染（或是它的依赖发生改变）时，使焦点落在输入框上。
* (D) 最后，因为 `ref` 被传给了输入框的 `ref` 属性，它的 `current` 属性让我们可以访问到当前元素。通过程序使其聚焦作为一种副作用，但仅当设置了 `isFocused`  并且 `current` 属性存在时。

> This was an example of how to move from declarative to imperative programming in React. It's not always possible to go the declarative way, so the imperative approach is necessary. This lesson is for the sake of learning about the DOM API in React.

这只是个如何在 React 中从声明式转换为指令式编程的例子。因为并不是所有时候都能使用声明的方式，所以指令式还是有必要的。本课是为了学习在 React 中如何使用 DOM API。

### 练习：

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Imperative-React).
>   * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Component-Composition...hs/Imperative-React?expand=1).
> * Read more about [React's useRef Hook](https://reactjs.org/docs/hooks-reference.html#useref).

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Imperative-React)。
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Component-Composition...hs/Imperative-React?expand=1)。
* 阅读更多关于 [React useRef Hook](https://reactjs.org/docs/hooks-reference.html#useref) 的文章。
