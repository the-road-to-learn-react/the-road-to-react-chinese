## React Component Composition

## 组件组合

> Now we'll discover how to use a React element in the same fashion as an HTML element, with an opening and closing tag:

现在我们将要学习如何通过一个开合标签，像 HTML 元素一样使用 React 元素：

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
        onInputChange={handleSearch}
# leanpub-start-insert
      >
        Search
      </InputWithLabel>
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

> Instead of using the `label` prop from before, we inserted the text "Search:" between the component's element's tags. In the InputWithLabel component, you have access to this information via **React's children** prop. Instead of using the`label` prop, use the children`prop to render everything that has been passed down from above where you want it:

不像之前使用 `label` prop 那样，而是把 “Search” 字样放在了组件元素标签之间。在组件 InputWithLabel 里，可以通过 **React 的 children** prop 来获取这个信息。不使用 `label` prop，而是用 `children` prop 在需要的地方渲染所有从上层传下来的东西：

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
# leanpub-start-insert
  children,
# leanpub-end-insert
}) => (
  <>
# leanpub-start-insert
    <label htmlFor={id}>{children}</label>
# leanpub-end-insert
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

> Now the React component's elements behave similar to native HTML. Everything that's passed between a component's elements can be accessed as children`in the component and be rendered somewhere. Sometimes when using a React component, you want to have more freedom from the outside what to render in the inside of a component:

现在 React 组件元素表现的和原生 HTML 很类似了。所有在组件元素之间的部分，在组件中都可以通过 `children` 获取到，然后渲染出来。有时当使用一个 React 组件时，你希望在外部有更多自由去决定组件内部应该渲染什么：

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
        onInputChange={handleSearch}
      >
# leanpub-start-insert
        <strong>Search:</strong>
# leanpub-end-insert
      </InputWithLabel>

      ...
    </div>
  );
};
~~~~~~~

> With this React feature, we can compose React components into each other. We've used it with a JavaScript string and with a string wrapped in an HTML `<strong>` element, but it doesn't end here. You can pass components via React children as well.

有了 React 这个特性，我们就可以交织使用 React 组件。我们可以用它来传递一个 JavaScript 字符串，也可以把字符串用 HTML 元素 `<strong>` 包起来，还不止如此，组件也可以被当作 React children 传递。

### 练习：

* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Composition).
  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Reusable-React-Component...hs/React-Component-Composition?expand=1).
* Read more about React Component Composition ([0](https://www.robinwieruch.de/react-component-composition), [1](https://reactjs.org/docs/composition-vs-inheritance.html)).
* Create a simple text component that renders a string and passes it as `children` to the InputWithLabel component.



* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Composition)。
	* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Reusable-React-Component...hs/React-Component-Composition?expand=1)。
* 阅读更多关于 React 组件组合的文章 （[0](https://www.robinwieruch.de/react-component-composition)，[1](https://reactjs.org/docs/composition-vs-inheritance.html)）。
* 创建一个简单的渲染字符串的文字组件，然后将它作为 `children` 传给 InputWithLabel 组件。
