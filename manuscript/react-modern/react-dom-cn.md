## React DOM

> Now that we've learned about component definitions and their instantiation, we can move to the App component's instantiation. It has been in our application from the start, in the *src/index.js* file:

现在我们已经了解了组件定义及其实例化，接下来我们来看看 App 组件的实例化。它从一开始就在我们的应用程序的 *src/index.js* 文件中：

{title="src/index.js",lang="javascript"}
~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
~~~~~~~

> Next to React, there is another imported library called `react-dom`, in which a `ReactDOM.render()` function uses an HTML node to replace it with JSX. The process integrates React into HTML. `ReactDOM.render()` expects two arguments; the first is to render the JSX. It creates an instance of your App component, though it can also pass simple JSX without any component instantiation.

在导入 React 库的代码下面，有另一个被导入的库，称为 `react-dom`，其中的 `ReactDOM.render()` 方法将使用 JSX 替换 HTML 中的一个 DOM 节点，该过程是将 React 集成到了 HTML 中。
 `ReactDOM.render()` 这个方法需要传入两个参数；首先是要渲染的 JSX，你可以直接传入一个简单的 JSX，而无需任何组件的实例化，也可以直接传入一个组件的实例。
 
{title="Code Playground",lang="javascript"}
~~~~~~~
ReactDOM.render(
  <h1>Hello React World</h1>,
  document.getElementById('root')
);
~~~~~~~

> The second argument specifies where the React application enters your HTML. It expects an element with an `id='root'`, found in the *public/index.html* file. This is a basic HTML file.

第二个参数指定了 React 应用在何处输入你的 HTML。 它需要在 *public/index.html* 文件中找到一个 `id='root'` 的元素。 这是一个基本的 HTML 文件。

> ### Exercises:

### 练习：

> * Open the *public/index.html* to see where the React application enters your HTML.
> * Consider how we can include a React application in an external web application that uses HTML.
> * Read more about [rendering elements in React](https://reactjs.org/docs/rendering-elements.html).

* 打开 *public/index.html*，查看 React 应用程序在何处放置 HTML。
* 设想一下我们如何在使用 HTML 的外部 Web 应用程序中包含 React 应用程序。
* 阅读更多关于 [React 中元素渲染](https://reactjs.org/docs/rendering-elements.html)的信息。
