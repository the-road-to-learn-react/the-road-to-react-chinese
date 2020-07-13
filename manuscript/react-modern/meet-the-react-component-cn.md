> ## Meet the React Component

## 认识React组件

> Our first React component is in the *src/App.js* file, which should look similar to the example below. The file might differ slightly, because create-react-app will sometimes update the default component's structure.

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

> This file will be our focus throughout this tutorial, unless otherwise specified. Let's start by reducing the component to a more lightweight version for getting you started without too much boilerplate code from create-react-app.

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

> First, this React component, called App component, is just a JavaScript function. It's commonly called **function component**, because there are other variations of React components  (see **component types** later). Second, the App component doesn't receive any parameters in its function signature yet (see **props** later). And third, the App component returns code that resembles HTML which is called JSX (see **JSX** later).

首先，这个 App 组件是一个 JavaScript 函数。一般称它为 **函数组件 **，因为还有其他形式的 React 组件（稍后介绍 **组件类型 **）。其次，截止目前 App 函数组件不接收任何参数（稍后介绍  **props **）。最后，App 组件返回的像 HTML 的代码称之为 JSX（稍后介绍 **JSX **）。

> The function component possess implementation details like any other JavaScript function. You will see this in practice in action throughout your React journey:

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

> Variables defined in the function's body will be re-defined each time this function runs, like all JavaScript functions:

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

> Since we don't need anything from within the App component used for this variable -- e.g. parameters coming from the function signature -- we can define the variable outside of the App component as well:

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

> Let's use this variable in the next section.

我们将在下一节中使用这个变量！

> ### Exercises:

### 练习:

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-the-React-Component).

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-the-React-Component)。

> * If you are unsure when to use `const`, `let` or `var` in JavaScript (or React) for variable declarations, make sure to [read more about their differences](https://www.robinwieruch.de/const-let-var).
> * Read more about [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const).
> * Read more about [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let).

* 如果你不确定什么时候在 JavaScript（或 React）中使用`const`，`let`，或者`var`来声明变量，可以[阅读更多关于它们之间的区别](https://www.robinwieruch.de/const-let-var)
  * 阅读更多关于 [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
  * 阅读更多关于 [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

> * Think about ways to display the `title` variable in your App component's returned HTML. In the next section, we'll put this variable to use.

思考一下在 App 组件返回的 HTML 中展示`title`的方式有哪些。我们将在下一节中使用这个变量。
