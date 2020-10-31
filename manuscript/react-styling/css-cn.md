> # Styling in React
#  React中的样式

> There are many ways to style a React application, and there are lengthy debates about the best **styling strategy** and **styling approach**. We'll go over a few of these approaches without giving them too much weight. There will be some pro and con arguments, but it's mostly a matter of what fits best for developers and teams.

React 应用中写样式的方法有很多，关于最好的**样式策略**和**样式方法**也争论了很久。我们将介绍几种方法，但不会给个权重来说明这些方法哪个更好。目前有正反两方面的论点，但主要是看什么最适合开发者和团队。

> We will begin React styling with common CSS in React, but then explore two alternatives for more advanced **CSS-in-CSS** (**CSS Modules**) and **CSS-in-JS** (**Styled Components**) strategies. CSS Modules and Styled Components are only two approaches out of many in both groups of strategies. We'll also cover how to include scalable vector graphics (SVGs), such as a logo or icons, in our React application.

我们将从 React 中常见的 CSS 样式开始，但随后我们将探讨两种更高级的 **CSS-in-CSS**（**CSS模块**）和 **CSS-in-JS**（**样式组件**）策略。CSS 模块和样式组件只是这两组策略中的两种方法。我们也将介绍如何在 React 应用程序中包含可扩展的矢量图形（SVG），如Logo或图标等。

![](images/css-style-strategies.png)

> If you don't want to build common UI components (e.g. button, dialog, dropdown) from scratch, you can always pick a [popular UI library suited for React](https://www.robinwieruch.de/react-libraries), which provides these components by default. However, it is better for learning React if you try building these components before using a pre-built solution. As a result, we won't use any of the UI component libraries.

如果你不想从头开始构建常见的 UI 组件（如按钮、对话框、下拉菜单），你可以选择一个[适合 React 流行的 UI 库](https://www.robinwieruch.de/react-libraries)，它默认提供了这些组件。不过，如果你在使用预构建的解决方案之前，先尝试着构建这些组件，对学习 React 更好。因此，我们将不会使用任何一个 UI 组件库。

![](images/ui-library.png)

> The following styling approaches and SVGs are pre-configured in `create-react-app`. If you're in control of the build tools (e.g. Webpack), they might need to be configured to import CSS or SVG files. Since we are using create-react-app, we can use these files as assets right away.

以下是在 `create-react-app` 中预先配置的样式化方法和 SVG。如果你使用了构建工具（例如 Webpack），可能需要配置为导入 CSS 或 SVG 文件。因为我们使用的是 create-react-app，所以我们可以立即使用这些文件作为资源。

> ## CSS in React

React 中的 CSS

> Common CSS in React is similar to the standard CSS you may have already learned. Each web application gives HTML elements a `class` (in React it's `className`) attribute that is styled in a CSS file later.

React 中的通用 CSS 与您可能已经学习的标准 CSS 相似。 每个 Web 应用程序都为 HTML 元素提供了一个 class 属性（在 React 中为 className），该属性稍后将在 CSS 文件中设置样式。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <div className="container">
      <h1 className="headline-primary">My Hacker Stories</h1>
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
    </div>
  );
};
~~~~~~~

> The `<hr />` was removed because the CSS handles the border in the next steps. We'll import the CSS file, which is done with the help of the create-react-app configuration:

因为 CSS 在下一步处理了边框，所以删除了 `<hr />`。 我们将导入 CSS 文件，这是在 create-react-app 配置的帮助下完成的：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';

# leanpub-start-insert
import './App.css';
# leanpub-end-insert
~~~~~~~

> This CSS file will define the two (and more) CSS classes we used in the App component. In your *src/App.css* file, define them like the following:

此 CSS 文件将定义我们在 App 组件中使用的两个（以及更多）CSS 类。 在* src / App.css *文件中，像下面这样定义它们：

{title="src/App.css",lang="css"}
~~~~~~~
.container {
  height: 100vw;
  padding: 20px;

  background: #83a4d4; /* fallback for old browsers */
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
}

.headline-primary {
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
}
~~~~~~~

> You should see the first stylings taking effect in your application when you start it again. Next, we will head over to the Item component. Some of its elements receive the `className` attribute too, however, we are also using a new styling technique here:

当再次启动应用程序时，应该会看到第一个样式已经在应用程序中生效。接下来，我们看一下 Item 组件。它的一些元素也会接收 "className" 属性，但是，我们在这里也使用了一种新的样式化技术。

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <div className="item">
    <span style={{ width: '40%' }}>
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
    </span>
# leanpub-start-insert
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
# leanpub-end-insert
      <button
        type="button"
        onClick={() => onRemoveItem(item)}
# leanpub-start-insert
        className="button button_small"
# leanpub-end-insert
      >
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

> As you can see, we can also use the native `style` attribute for HTML elements. In JSX, style can be passed as an inline JavaScript object to these attributes. This way we can define dynamic style properties in JavaScript files rather than mostly static CSS files. This approach is called **inline style**, which is useful for quick prototyping and dynamic style definitions. Inline style should be used sparingly, however, as a separate style definition keeps the JSX more concise.

如你所见，我们也可以使用 HTML 元素的原生 `style` 属性。在 JSX 中，style 可以作为一个内联的 JavaScript 对象传递这些属性。这样我们就可以在 JavaScript 文件中定义动态样式属性，而不是大部分的静态 CSS 文件。这种方法被称为**内联样式**，它对于快速原型设计和动态样式定义非常有用。然而，内联样式应该慎用，因为单独的样式定义可以让 JSX 更简洁。

> In your *src/App.css* file, define the new CSS classes. Basic CSS features are used. Advanced CSS features (e.g. nesting) from CSS extensions (e.g. Sass) are not included in this example, as they are [optional configurations](https://create-react-app.dev/docs/adding-a-sass-stylesheet/).

在你的 *src/App.css* 文件中，定义新的 CSS 类。使用了基本的 CSS 特性。此示例中不包括来自 CSS 扩展（如 Sass）的高级 CSS 功能（如嵌套），因为它们是[可选配置](https://create-react-app.dev/docs/adding-a-sass-stylesheet/)。

{title="src/App.css",lang="css"}
~~~~~~~
.item {
  display: flex;
  align-items: center;
  padding-bottom: 5px;
}

.item > span {
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.item > span > a {
  color: inherit;
}
~~~~~~~

 > The button style from the previous component is still missing, so we'll define a base button style and two more specific specific button styles (small and large). One of the button specifications has been used, the other will be used in the next steps.

 前一个组件中的按钮样式仍然缺失，所以我们将定义一个基本的按钮样式和两个更特定的按钮样式（小和大）。其中一个按钮样式已经被使用了，另一个将在下一步中使用。

{title="src/App.css",lang="css"}
~~~~~~~
.button {
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;
}

.button:hover {
  background: #171212;
  color: #ffffff;
}

.button_small {
  padding: 5px;
}

.button_large {
  padding: 10px;
}
~~~~~~~

> Apart from styling approaches in React, naming conventions ([CSS guidelines](https://developer.mozilla.org/en-US/docs/MDN/Contribute/Guidelines/Code_guidelines/CSS)) are a whole other topic. The last CSS snippet followed BEM rules by defining modifications of a class with an underscore (`_`). Choose whatever naming convention suits you and your team. Without further ado, we will style the next React component:

除了 React 中的样式方法外，命名约定([[CSS 指南](https://developer.mozilla.org/en-US/docs/MDN/Contribute/Guidelines/Code_guidelines/CSS))是另一个主题。最后的 CSS 片段遵循了 BEM 规则，用下划线(`_`)定义了一个类针对修改。选择任何适合你和你的团队的命名惯例。事不宜迟，我们将对下一个 React 组件设置样式。

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <form onSubmit={onSearchSubmit} className="search-form">
# leanpub-end-insert
    <InputWithLabel ... >
      <strong>Search:</strong>
    </InputWithLabel>

    <button
      type="submit"
      disabled={!searchTerm}
# leanpub-start-insert
      className="button button_large"
# leanpub-end-insert
    >
      Submit
    </button>
  </form>
);
~~~~~~~

 > We can also pass the `className` attribute as a prop to React components. For example, we can use this option to pass the SearchForm component a flexible style with a `className` prop from a range of predefined classes from a CSS file. Lastly, style the InputWithLabel component:

 我们还可以将 `className` 属性作为 prop 传递给 React 组件。例如，我们可以使用此选项为 SearchForm 组件传递灵活的样式,该样式来自 CSS 文件的一系列预定义类的 `className` 属性。最后，设置 InputWithLabel 组件的样式:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <label htmlFor={id} className="label">
# leanpub-end-insert
        {children}
      </label>
      &nbsp;
      <input
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
# leanpub-start-insert
        className="input"
# leanpub-end-insert
      />
    </>
  );
};
~~~~~~~

> In your *src/App.css* file, add the remaining classes:

在 *src/App.css* 文件中，添加其余的类:

{title="src/App.css",lang="css"}
~~~~~~~
.search-form {
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
}

.label {
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
}

.input {
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
}
~~~~~~~

> For simplicity, we styled elements like label and input individually in the *src/App.css* file. However, in a real application it may be better to define these elements once in the *src/index.css* file globally. As React components are split into multiple files, sharing style becomes a necessity.

为了简单起见，我们在 *src/App.css* 文件中分别定义了标签和输入等元素的样式。然而，在实际应用中，最好在 *src/index.css* 文件中全局定义一次这些元素。由于 React 组件被拆分成多个文件，共享样式就成了一种必然。

> This is the basic CSS most of us have already learned, written with an inline style that is more dynamic. Without CSS extensions like Sass (Syntactically Awesome Style Sheets) inline styles can become burdensome, though, because features like CSS nesting are not available in native CSS.

这是我们大多数人已经学习的基本 CSS，以更加动态的内联样式编写。 但是，如果没有像 Sass 这样的 CSS 扩展（语法上很棒的样式表），则内联样式会变得很麻烦，因为 CSS 嵌套之类的功能在原生 CSS 中不可用。

> ### Exercises:

### 练习：

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/CSS-in-React).

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/CSS-in-React)

> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/CSS-in-React?expand=1).

确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/CSS-in-React?expand=1)

> * Read more about [CSS stylesheets in create-react-app](https://create-react-app.dev/docs/adding-a-stylesheet).

阅读更多关于[creative-react-app 中的 CSS 样式表](https://create-react-app.dev/docs/adding-a-stylesheet)

> * Read more about [Sass in create-react-app](https://create-react-app.dev/docs/adding-a-sass-stylesheet) for taking advantage of more advanced CSS features like nesting.

阅读更多关于[creative-react-app 中的 Sass](https://create-react-app.dev/docs/adding-a-sass-stylesheet),以利用更高级的 CSS 功能，如嵌套。


> * Try to pass `className` prop from App to SearchForm component, either with the value `button_small` or `button_large` and use this as `className` for the button element.

尝试将 “className” 属性从 App 传递到 SearchForm 组件，其值为 “button_small” 或 “button_large”，并将其用作按钮元素的 “className”。