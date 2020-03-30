> ## Meet another React Component

## 认识另一种形式的React组件

> So far we've only been using the App component to create our applications. We used the App component in the last section to express everything needed to render our list in JSX, and it should scale with your needs and eventually handle more complex tasks. To help with this, we'll split some of its responsibilities into a standalone List component:

到目前为止，我们只用了App组件创建了我们的应用程序。在上一节中，我们用App组件来表示在JSX中渲染列表的一切，它应该随着你的需求不断扩展，并且最终能够处理一些更复杂的任务。为了解决这个问题，我们将App组件的一些职责分解为一个独立的List组件：

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

> Optional: If this component looks odd, because the outermost part of the returned JSX starts with JavaScript. We could use it with a wrapping HTML element as well, but we'll continue with the previous version.

可选项：如果你觉得这个组件看起来有点怪，那是因为返回的JSX最外层是以JavaScript开始的。我们也可以在它外面包一层HTML，但在这儿我们还是使用之前的版本。

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

> Now the new List component can be used in the App component:

现在就可以在App组件中使用List组件了：

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

> You've just created your first React component! With this example, we can see how components that encapsulate meaningful tasks can work for larger React applications.

你刚刚创建了第一个React组件！通过这个例子，我们可以看到那些封装的有意义任务的组件是如何在更大的React应用中工作的。

> Larger React applications have **component hierarchies** (also called **component trees**). There is usually one uppermost **entry point component** (e.g. App) that spans a tree of components below it. The App is the **parent component** of the List, so the List is a **child component** of the App. In a component tree, the App is the **root component**, and the components that don't render any other components are called **leaf components** (e.g. List). The App can have multiple children, as can the List. If the App has another child component, the additional child component is called a **sibling component** of the List.

大一点的React应用具备**组件层次结构**(也称为**组件树**)。一般来说最顶层的**入口组件**(比如：APP)会在它里面嵌套组件树。App组件是List组件的**父组件**，相应的，List组件也是App组件的**子组件**。在组件树中，App是**根组件**，这个组件不会渲染其他**叶子组件**(比如： List)。App可以有多个子组件，List组件也是。如果App组件有其他子组件，那这个子组件称为List组件的**兄弟组件**。


> ### Exercises:
### 练习:

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-another-React-Component).
> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Lists-in-React...hs/Meet-another-React-Component?expand=1).
> * Draw your components -- the App component and List component -- as a component tree on a sheet of paper. Extend this component tree with other possible components (e.g. extracted Search component for the input field and label in the App component). Try to figure out which other parts can be extracted as standalone components.
> * If a Search component is used in the App component, would the Search component be a sibling, parent, or child component for the List component?
> * Ask yourself what problems could arise if we keep treating the `list` variable as global variable. We will cover how to handle these problems in the upcoming sections.

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-another-React-Component)。
  * 确认[上一节之后的变更](ttps://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Lists-in-React...hs/Meet-another-React-Component?expand=1)。
* 在纸上绘制一下App组件和List组件树。用其他可能的组件（比如：在App组件中提取input和label的Search组件）来扩展这个组件树。尝试找出其他组件哪些可以作为独立的组件被提取。
* 如果在App组件中使用Search组件，那对于List组件来说，Search组件是它的兄弟组件还是父组件或子组件？
* 思考一下，如果我们使用全局变量来处理`list`会出现什么问题？我们即将在接下来的章节中介绍如何处理这个问题。

