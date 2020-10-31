## 认识另一种形式的React组件

到目前为止，我们只用了 App 组件创建我们的应用程序。在上一节中，我们用 App 组件来表示在 JSX 中渲染列表的一切，它应该随着你的需求不断扩展，并且最终能够处理一些更复杂的任务。为了解决这个问题，我们将 App 组件的一些职责分解为一个独立的 List 组件：

{title="src/App.js",lang="javascript"}
~~~~~~~
const list = [ ... ];

function App() { ... }

# leanpub-start-insert
function List() {
  return list.map(function(item) {
    return (
      <div key={item.objectID}>
        <span>
          <a href={item.url}>{item.title}</a>
        </span>
        <span>{item.author}</span>
        <span>{item.num_comments}</span>
        <span>{item.points}</span>
      </div>
    );
  });
}
# leanpub-end-insert
~~~~~~~

可选项：如果你觉得这个组件看起来有点怪，那是因为返回的 JSX 最外层是以 JavaScript 开始的。我们也可以在它外面包一层 HTML，但在这儿我们还是使用之前的版本。

{title="src/App.js",lang="javascript"}
~~~~~~~
function List() {
# leanpub-start-insert
  return (
    <div>
      {list.map(function(item) {
# leanpub-end-insert
        return (... );
# leanpub-start-insert
      })}
    </div>
  );
# leanpub-end-insert
}
~~~~~~~

现在就可以在 App 组件中使用 List 组件了：

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

      <hr />

# leanpub-start-insert
      <List />
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

你刚刚创建了第一个 React 组件！通过这个例子，我们可以看到那些封装的有意义任务的组件是如何在更大的 React 应用中工作的。

![](images/component-tree.png)

大一点的 React 应用具备**组件层次结构**（也称为**组件树**）。一般来说最顶层的**入口组件**（比如：APP）会在它里面嵌套组件树。App 组件是 List 组件的**父组件**，相应的，List 组件也是 App 组件的**子组件**。在组件树中，App 是**根组件**，这个组件不会渲染其他**叶子组件**（比如： List）。App 可以有多个子组件，List 组件也是。如果 App 组件有其他子组件，那这个子组件称为 List 组件的**兄弟组件**。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-another-React-Component)。
* 确认[上一节之后的变更](ttps://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Lists-in-React...hs/Meet-another-React-Component?expand=1)。
* 在纸上绘制一下 App 组件和 List 组件树。用其他可能的组件（比如：在 App 组件中提取 input 和 label 的 Search 组件）来扩展这个组件树。尝试找出其他组件哪些可以作为独立的组件被提取。
* 如果在 App 组件中使用 Search 组件，那对于 List 组件来说，Search 组件是它的兄弟组件还是父组件或子组件？
* 思考一下，如果我们使用全局变量来处理`list`会出现什么问题？我们即将在接下来的章节中介绍如何处理这个问题。
