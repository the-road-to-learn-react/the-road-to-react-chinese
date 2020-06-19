> ## Styled Components in React

## React 中的样式组件

> With the previous approaches from CSS-in-CSS, Styled Components is one of several approaches for **CSS-in-JS**. I picked Styled Components because it's the most popular. It comes as a JavaScript dependency, so we must install it on the command line:

有了前面 CSS-in-CSS 的方法，**Styled Components** 是 **CSS-in-JS** 的几种方法之一。我选择 **Styled Components** 是因为它是最流行的。它是作为 JavaScript 的依赖，所以我们必须在命令行上安装它：

{title="Command Line",lang="text"}
~~~~~~~
npm install styled-components
~~~~~~~

> Then import it in your *src/App.js* file:

然后在你的 *src/App.js* 文件中引入它：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';
# leanpub-start-insert
import styled from 'styled-components';
# leanpub-end-insert
~~~~~~~

> As the name suggests, CSS-in-JS happens in your JavaScript file. In your *src/App.js* file, define your first styled components:

顾名思义，CSS-in-JS 发生在你的 JavaScript 文件中。在你的 *src/App.js* 文件中，定义你的第一个样式组件：

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledContainer = styled.div`
  height: 100vw;
  padding: 20px;

  background: #83a4d4;
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
`;

const StyledHeadlinePrimary = styled.h1`
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
`;
~~~~~~~

> When using Styled Components, you are using the JavaScript template literals the same way as JavaScript functions. Everything between the backticks can be seen as an argument and the `styled` object gives you access to all the necessary HTML elements (e.g. div, h1) as functions. Once a function is called with the style, it returns a React component that can be used in your App component:

当使用样式化组件时，你使用 JavaScript 模板字符串的方式与 JavaScript 函数的使用方式相同。模板字符串之间的所有内容都可以看作是一个参数，而 `styled` 对象则让你可以作为函数访问所有必要的 HTML 元素（如：div、h1）。一旦函数被调用了样式，它就会返回一个 React 组件，可以在你的 App 组件中使用：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <StyledContainer>
      <StyledHeadlinePrimary>My Hacker Stories</StyledHeadlinePrimary>
# leanpub-end-insert

      <SearchForm
        searchTerm={searchTerm}
        onSearchInput={handleSearchInput}
        onSearchSubmit={handleSearchSubmit}
      />

      {stories.isError && <p>Something went wrong ...</p>}

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
      )}
# leanpub-start-insert
    </StyledContainer>
# leanpub-end-insert
  );
};
~~~~~~~

> This kind of React component follows the same rules as a common React component. Everything passed between its element tags is passed automatically as React `children` prop. For the Item component, we are not using inline styles this time, but defining a dedicated styled component for it. `StyledColumn` receives its styles dynamically using a React prop:

这种 React 组件与普通的 React 组件遵循相同的规则。它的元素标签之间传递的所有内容都会自动作为 React `children` prop 传递下去。对于 Item 组件，我们这次没有使用内联样式，而是为它定义了一个专门的样式组件。`StyledColumn` 使用 React prop 动态接收其样式：

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <StyledItem>
    <StyledColumn width="40%">
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
# leanpub-start-insert
    </StyledColumn>
    <StyledColumn width="30%">{item.author}</StyledColumn>
    <StyledColumn width="10%">{item.num_comments}</StyledColumn>
    <StyledColumn width="10%">{item.points}</StyledColumn>
    <StyledColumn width="10%">
      <StyledButtonSmall
# leanpub-end-insert
        type="button"
        onClick={() => onRemoveItem(item)}
      >
        Dismiss
# leanpub-start-insert
      </StyledButtonSmall>
    </StyledColumn>
  </StyledItem>
# leanpub-end-insert
);
~~~~~~~

> The flexible `width` prop is accessible in the styled component's template literal as an argument of an inline function. The return value from the function is applied there as a string. Since we can use immediate returns when omitting the arrow function's body, it becomes a concise inline function:

灵活的 `width` prop 可作为内联函数的参数，在样式组件的模板字符串中访问。函数的返回值作为字符串使用，并且由于我们可以省略箭头函数的主体并立即返回结果，所以它成为一个简洁的内联函数：

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledItem = styled.div`
  display: flex;
  align-items: center;
  padding-bottom: 5px;
`;

const StyledColumn = styled.span`
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;

  a {
    color: inherit;
  }

  width: ${props => props.width};
`;
~~~~~~~

> Advanced features like CSS nesting are available in Styled Components by default. Nested elements are accessible and the current element can be selected with the `&` CSS operator:

样式组件中默认有 CSS 嵌套等高级功能，嵌套的元素是可以访问的，并且可以用 `&`CSS 操作符选择当前元素：

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledButton = styled.button`
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;

  &:hover {
    background: #171212;
    color: #ffffff;
  }
`;
~~~~~~~

> You can also create specialized versions of styled components by passing another component to the library's function. The specialized button receives all the base styles from the previously defined StyledButton component:

你也可以通过向库中的函数传递另一个组件来创建一个特殊的样式组件。定义的按钮组件将会从之前定义的 StyledButton 组件中接收所有的基本样式：

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledButtonSmall = styled(StyledButton)`
  padding: 5px;
`;

const StyledButtonLarge = styled(StyledButton)`
  padding: 10px;
`;

const StyledSearchForm = styled.form`
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
`;
~~~~~~~

> When we use a styled component like StyledSearchForm, its underlying elements ( `form`, `button`) are used in the real HTML output. We can continue using the native HTML attributes ( `onSubmit`, `type`, `disabled`) there:

当我们使用像 StyledSearchForm 这样的样式组件时，它的底层元素(`form`, `button`)被用于真正的 HTML 输出。我们可以在那里继续使用原生的 HTML 属性（`onSubmit`、`type`、`disabled`）：

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <StyledSearchForm onSubmit={onSearchSubmit}>
# leanpub-end-insert
    <InputWithLabel
      id="search"
      value={searchTerm}
      isFocused
      onInputChange={onSearchInput}
    >
      <strong>Search:</strong>
    </InputWithLabel>

# leanpub-start-insert
    <StyledButtonLarge type="submit" disabled={!searchTerm}>
# leanpub-end-insert
      Submit
# leanpub-start-insert
    </StyledButtonLarge>
  </StyledSearchForm>
# leanpub-end-insert
);
~~~~~~~

> Finally, the InputWithLabel decorated with its yet undefined styled components:

最后，InputWithLabel 组件用其尚未定义的样式组件装饰一下：

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <StyledLabel htmlFor={id}>{children}</StyledLabel>
# leanpub-end-insert
      &nbsp;
# leanpub-start-insert
      <StyledInput
# leanpub-end-insert
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
      />
    </>
  );
};
~~~~~~~

> And its matching styled components are defined in the same file:

而与之相匹配的样式化组件也被定义在同一个文件中：

{title="src/App.js",lang="javascript"}
~~~~~~~
const StyledLabel = styled.label`
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
`;

const StyledInput = styled.input`
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
`;
~~~~~~~

> CSS-in-JS with styled components shifts the focus of defining styles to actual React components. Styled Components are style defined as React components without the intermediate CSS file. If a styled component isn't used in a JavaScript, your IDE/editor will tell you. Styled Components are bundled next to other JavaScript assets in JavaScript files for a production-ready application. There are no extra CSS files, but only JavaScript when using the CSS-in-JS strategy. Both strategies, CSS-in-JS and CSS-in-CSS, and their approaches (e.g. Styled Components and CSS Modules) are popular among React developers. Use what suits you and your team best.

带有样式化组件的 CSS-in-JS 将定义样式的重点转移到实际的 React 组件上。Styled Components 是作为 React 组件定义的样式，没有中间的 CSS 文件。如果一个样式组件没有在 JavaScript 中使用，你的 IDE 或编辑器会告诉你。样式化组件被打包在 JavaScript 文件中的其他 JavaScript 资源中，用于准备部署在生产环境的应用程序中。在使用 CSS-in-JS 策略时，没有额外的 CSS 文件，只有 JavaScript。CSS-in-JS 和 CSS-in-CSS 这两种策略及其方法（如：Styled Components 和 CSS Modules）在 React 开发人员中很受欢迎。选择使用最适合你和你的团队的方法。

### 练习:

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Styled-Components-in-React).
  * 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Styled-Components-in-React?expand=1).
* 了解更多 [React 中的样式组件](https://www.robinwieruch.de/react-styled-components).
  * 当使用 Styled Components 时，通常没有 *src/index.css* 文件用于全局样式。了解如何在使用 Styled Components 时使用全局样式。