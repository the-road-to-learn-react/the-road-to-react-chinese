## React DOM

现在我们已经了解了组件定义及其实例化，接下来我们来看看App组件的实例化。它从一开始就在我们的应用程序的 *src/index.js* 文件中：
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

在导入 React 库的代码下面，有另一个被导入的库，称为`react-dom`，其中的`ReactDOM.render()`方法将使用JSX替换HTML中的一个DOM节点，该过程是将React集成到了HTML中。
 `ReactDOM.render()` 这个方法需要传入两个参数；首先是要渲染的 JSX，你可以直接传入一个简单的 JSX，而无需任何组件的实例化，也可以直接传入一个组件的实例。
{title="Code Playground",lang="javascript"}
~~~~~~~
ReactDOM.render(
  <h1>Hello React World</h1>,
  document.getElementById('root')
);
~~~~~~~
第二个参数指定了 React 应用在何处输入你的 HTML。 它需要在 *public/index.html* 文件中找到一个 `id='root'` 的元素。 这是一个基本的HTML文件。

### 练习：
* 打开 *public/index.html*，查看 React 应用程序在何处放置 HTML。
* 设想一下我们如何在使用 HTML 的外部 Web 应用程序中包含 React 应用程序。
* 阅读更多有关在 [React 中元素渲染](https://reactjs.org/docs/rendering-elements.html)的信息。
