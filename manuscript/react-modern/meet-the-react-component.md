## 认识React组件

我们的第一个 React 组件在 *src/App.js* 文件中，它和下面的这个例子很相似。内容可能稍有不同，因为 create-react-app 有时候会更新默认的组件结构。

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          Href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
~~~~~~~

除非有另外的说明，否则该文件将会是贯穿这个教程的核心。我们先将组件简化为更轻便的版本，这样就可以在入门时不用关注那么多 create-react-app 生成的模板代码了。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
import React from 'react';

function App() {
  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
# leanpub-end-insert
~~~~~~~

首先，这个 App 组件是一个 JavaScript 函数。一般称它为 **函数组件**，因为还有其他形式的 React 组件（稍后介绍 **组件类型**）。其次，截止目前 App 函数组件不接收任何参数（稍后介绍  **props**）。最后，App 组件返回的像 HTML 的代码称之为 JSX（稍后介绍 **JSX**）。

这个函数组件的实现细节和其他任何 JavaScript 函数一样。在整个 React 旅程中，你会在实践中体会到这一点。

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

function App() {
# leanpub-start-insert
  // 在这儿做点什么
# leanpub-end-insert

  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
~~~~~~~

跟所有的 JavaScript 函数一样，在函数体内定义的变量会在每次运行函数的时候被重新定义。

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

function App() {
# leanpub-start-insert
  const title = 'React';
# leanpub-end-insert

  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
~~~~~~~

如果这个变量不需要使用 App 组件中的任何内容（比如：参数来源于函数签名），我们也可以在 App 组件外部定义这个变量：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
const title = 'React';
# leanpub-end-insert

function App() {
  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}

export default App;
~~~~~~~

我们将在下一节中使用这个变量！

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-the-React-Component)。
* 如果你不确定什么时候在 JavaScript（或 React）中使用`const`，`let`，或者`var`来声明变量，可以[阅读更多关于它们之间的区别](https://www.robinwieruch.de/const-let-var)
  * 阅读更多关于 [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
  * 阅读更多关于 [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

思考一下在 App 组件返回的 HTML 中展示`title`的方式有哪些。我们将在下一节中使用这个变量。
