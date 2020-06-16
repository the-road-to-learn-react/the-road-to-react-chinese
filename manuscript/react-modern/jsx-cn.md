## React JSX

> Recall that I mentioned the returned output of the App component resembles HTML. This output is called JSX, which mixes HTML and JavaScript. Let's see how this works for displaying the variable:

回想一下，我提到 App 组件返回的类似 HTML 的输出。此输出称为 JSX，它将 HTML 和 JavaScript 混合在一起。让我们看看它如何展示变量：
{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

const title = 'React';

function App() {
  return (
    <div>
# leanpub-start-insert
      <h1>Hello {title}</h1>
# leanpub-end-insert
    </div>
  );
}

export default App;
~~~~~~~

> Start your application with `npm start` again, and look for the rendered variable in browser, which should read: "Hello React".

再次使用 `npm start` 启动你的应用程序，并在浏览器中查找渲染的变量，它应该显示为："Hello React"。

> Let's focus on the HTML, which is expressed almost the same in JSX. An input field with a label can be defined as follows:

让我们聚焦到 HTML，它在 JSX 中的表达几乎相同。带有标签的输入字段可以定义如下：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

const title = 'React';

function App() {
  return (
    <div>
      <h1>Hello {title}</h1>

# leanpub-start-insert
      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />
# leanpub-end-insert
    </div>
  );
}

export default App;
~~~~~~~

> We specified three HTML attributes here: `htmlFor`, `id`, and `type`. Where `id` and `type` should be familiar from native HTML, `htmlFor` might be new. The `htmlFor` reflects the `for` attribute in HTML. JSX replaces a handful of internal HTML attributes, but you can find all the [supported HTML attributes](https://reactjs.org/docs/dom-elements.html#all-supported-html-attributes) in React's documentation, which follow the camelCase naming convention. Expect to come across more JSX-specific attributes like `className` and `onClick` instead of `class` and `onclick`, as you learn more about React.

我们在此处指定了三个 HTML 属性：`htmlFor`，`id` 和 `type`。这里的 `id` 和 `type` 在原生 HTML 中很熟悉，`htmlFor` 可能是新的属性。`htmlFor` 在 HTML 中反映了 `for` 属性。 JSX 替换了一些 HTML 内部属性，但是你可以在 React 的文档中找到所有[支持的 HTML 属性](https://reactjs.org/docs/dom-elements.html#all-supported-html-attributes)，JSX 遵循驼峰式命名约定。 随着对 React 的更多了解，将会遇到更多的 JSX 特定属性，例如 `className` 和 `onClick，而不是` `class` 和 `onclick。`

> We will revisit the HTML input field for implementation details later; for now, let's return to JavaScript in JSX. We have defined a string primitive to be displayed in the App component, and the same can be done with a JavaScript object:

稍后，我们将重新探索 HTML 输入字段的实现细节； 现在，让我们回到 JSX 中的 JavaScript。 我们已经定义了要在 App 组件中显示的字符串基础类型，也可以使用 JavaScript 对象完成此操作：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
const welcome = {
  greeting: 'Hey',
  title: 'React',
};
# leanpub-end-insert

function App() {
  return (
    <div>
      <h1>
# leanpub-start-insert
        {welcome.greeting} {welcome.title}
# leanpub-end-insert
      </h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />
    </div>
  );
}

export default App;
~~~~~~~

> Remember, everything in curly braces in JSX can be used for JavaScript expressions (e.g. function execution):

请记住，JSX 花括号中的所有内容都可使用 JavaScript 表达式（例如函数执行）：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
function getTitle(title) {
  return title;
}
# leanpub-end-insert

function App() {
  return (
    <div>
# leanpub-start-insert
      <h1>Hello {getTitle('React')}</h1>
# leanpub-end-insert

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />
    </div>
  );
}

export default App;
~~~~~~~

> JSX was initially invented for React, but it became useful for other modern libraries and frameworks after it gained popularity. It is one of my favorite things about React. Without any extra templating syntax (except for the curly braces), we are now able to use JavaScript in HTML.

JSX 最初是为 React 发明的，但在流行之后，它对其他现代库和框架也适用。这是我喜欢 React 的地方之一。现在不需要任何额外的模板语法（花括号除外），我们现在就可以在 HTML 中使用 JavaScript。

> ### Exercises:

### 练习:

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-JSX).
> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-the-React-Component...hs/React-JSX?expand=1).
> * Read more about [React's JSX](https://reactjs.org/docs/introducing-jsx.html).
> * Define more primitive and complex JavaScript data types and render them in JSX.
> * Try to render a JavaScript array in JSX. If it's too complicated, don't worry, because you will learn more about this in the next section.

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-JSX)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-the-React-Component...hs/React-JSX?expand=1)。
* 阅读更多关于 [React 的 JSX](https://reactjs.org/docs/introducing-jsx.html)。
* 定义更多基础和复杂的 JavaScript 数据类型，并在 JSX 中渲染它们。
* 尝试在 JSX 中渲染 JavaScript 数组。 如果过于复杂，请不要担心，因为你将在下一节中详细了解。
