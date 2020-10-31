## 实例化 React 组件

接下来，我将简单介绍一下 JavaScript 类，以帮助阐明 React 组件。从技术上讲，它们没有什么关系，需要注意这一点，但这对于您理解组件概念是一个合适的类比。

类在面向对象编程语言中非常常用。JavaScript 的编程模式非常灵活，允许函数式编程和面向对象编程共存。要概括 JavaScript 类以进行面向对象的编程，思考以下的 *Developer* 类：

{title="Code Playground",lang="javascript"}
~~~~~~~
class Developer {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  getName() {
    return this.firstName + ' ' + this.lastName;
  }
}
~~~~~~~

每个类都有一个构造函数 constructor，接收一些参数并将其分配给类的实例。类还可以定义与主体相关的函数（例如 `getName`），称为 **方法** or **类方法**.

定义 Developer 类只是其中一部分；另外还需要进行实例化。类定义是对功能蓝图的描绘，当使用 `new` 语句创建实例时会被用到。

{title="Code Playground",lang="javascript"}
~~~~~~~
// 类定义
class Developer { ... }

// 类实例化
const robin = new Developer('Robin', 'Wieruch');

console.log(robin.getName());
// "Robin Wieruch"

// 实例化另一个
const dennis = new Developer('Dennis', 'Wieruch');

console.log(dennis.getName());
// "Dennis Wieruch"
~~~~~~~

如果有一个 JavaScript 类的定义存在，则可以创建它的 *多个* 实例。它类似 React component，它只有 *一个* 组件定义，但可以有 *多个* 组件实例:

{title="src/App.js",lang="javascript"}
~~~~~~~
// 定义 App 组件
function App() {
  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

      <hr />

      {/* 创建一个 List 组件实例 */}
      <List />
      {/* 创建另一个 List 组件实例 */}
      <List />
    </div>
  );
}

// 定义 List 组件
function List() { ... }
~~~~~~~

一旦我们定义了 **组件**，我们就可以在 JSX 中任何地方像 HTML **元素** 一样使用它。元素会产生一个 **组件** 实例，换句话说，组件被实例化了。您可以根据需要创建任意数量的组件实例。它与 JavaScript 的类定义和用法没有太大区别。

### 练习

* 熟悉术语 *组件声明*, *实例*, 和 *元素*。
* 通过创建一个 List 组件的多个组件实例来进行实验。
* 想想看，如何给每个 List 组件赋予自己的 `list` 属性。
