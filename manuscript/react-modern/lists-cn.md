> ## Lists in React

## React 中的列表

> So far we've rendered a few primitive variables in JSX; next we'll render a list of items. We'll experiment with sample data at first, then we'll apply that to fetch data from a remote API. First, let's define the array as a variable. As before, we can define a variable outside or inside the component. The following defines it outside:

到目前为止，我们已经在 JSX 中渲染了一些原始变量。接下来，我们将渲染项目列表。我们先用示例数据进行实验，然后将其应用于从远程 API 获取数据。首先，让我们将数组定义为变量。和以前一样，我们可以在组件外部或内部定义变量。以下是在外部定义变量：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
const list = [
  {
    title: 'React',
    url: 'https://reactjs.org/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  },
  {
    title: 'Redux',
    url: 'https://redux.js.org/',
    author: 'Dan Abramov, Andrew Clark',
    num_comments: 2,
    points: 5,
    objectID: 1,
  },
];
# leanpub-end-insert

function App() { ... }

export default App;
~~~~~~~

> I used a `...` here as a placeholder, to keep my code snippet small (without App component implementation details) and focused on the essential parts (the `list` variable outside of the App component). I will use the `...` throughout the rest of this learning experience as placeholder for code blocks that I have established previous exercises. If you get lost, you can always verify your code using the CodeSandbox links I provide at the end of most sections.

我在这里使用一个 `...` 作为占位符，以使代码段简洁（没有 App 组件实现细节），并专注于必要部分（App 组件外部的 `list` 变量）。在整个学习过程中，我将使用 `...` 作为我之前用于练习的代码块的占位符。 如果你感到困惑，可以随时使用大多数章节结尾处提供的 CodeSandbox 链接来验证代码。

> Each item in the list has a title, a url, an author, an identifier (`objectID`), points -- which indicate the popularity of an item -- and a count of comments. Next, we'll render the list within our JSX dynamically:

列表中的每个项目都有一个标题，一个 URL，一个作者，一个标识符（`objectID`），分数（表示该项目的受欢迎程度）以及评论数。 接下来，我们将在 JSX 中动态渲染列表：

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
# leanpub-start-insert
      <h1>My Hacker Stories</h1>
# leanpub-end-insert

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

# leanpub-start-insert
      <hr />
# leanpub-end-insert

# leanpub-start-insert
      {/* render the list here */}
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

> You can use the [built-in JavaScript map method for arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) to iterate over each item of the list and return a new version of each:

你可以使用[数组内置的 JavaScript map 方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)遍历每个项目并返回他们的新版本：

{title="Code Playground",lang="javascript"}
~~~~~~~
const numbers = [1, 4, 9, 16];

const newNumbers = numbers.map(function(number) {
  return number * 2;
});

console.log(newNumbers);
// [2, 8, 18, 32]
~~~~~~~

> We won't map from one JavaScript data type to another in our case. Instead, we return a JSX fragment that renders each item of the list:

在这种情况下，我们不会从一种 JavaScript 数据类型映射到另一种。 相反，我们返回渲染列表中每个项目的 JSX 片段：

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {list.map(function(item) {
        return <div>{item.title}</div>;
      })}
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

> Actually, one of my first React "Aha" moments was using barebones JavaScript to map a list of JavaScript objects to HTML elements without any other HTML templating syntax. It's just JavaScript in HTML.

实际上，我最初对 React 感到惊叹的瞬间之一是使用简洁的 JavaScript 来将 JavaScript 对象列表映射为 HTML 元素，而没有任何其他 HTML 模板语法。 它只是 HTML 中的 JavaScript。

![](images/jsx-mapping.png)

> React will display each item now, but you can still improve your code so React handles advanced dynamic lists more gracefully. By assigning a key attribute to each list item's element, React can identify modified items if the list changes (e.g. re-ordering). Fortunately, our list items come with an identifier:

React 现在会显示每个项目，但是你仍然可以改进代码，以便 React 更优雅地处理高级动态列表。 通过为每个列表项的元素分配 key 属性，React 可以在列表发生更改（例如重新排序）时识别已修改的项目。 幸运的是，我们的列表项带有一个标识符：

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      ...

      <hr />

      {list.map(function(item) {
# leanpub-start-insert
        return (
          <div key={item.objectID}>
# leanpub-end-insert
            {item.title}
# leanpub-start-insert
          </div>
        );
# leanpub-end-insert
      })}
    </div>
  );
}
~~~~~~~

> We avoid using the index of the item in the array to make sure the key attribute is a stable identifier. If the list changes its order, for example, React will not be able to identify the items properly:

我们要避免使用数组中项目的索引来保证 key 属性是稳定的标识符。 例如，如果列表更改顺序，React 将无法正确识别项目：

{title="Code Playground",lang="javascript"}
~~~~~~~
// don't do this
{list.map(function(item, index) {
  return (
    <div key={index}>
      ...
    </div>
  );
})}
~~~~~~~

> So far, only the title is displayed for each item. Let's experiment with displaying more of the item's properties:

到目前为止，每个项目仅显示标题。 让我们尝试显示更多项目属性：

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {list.map(function(item) {
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
      })}
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

> The map function is inlined concisely in your JSX. Within the map function, we have access to each item and its properties. The `url` property of each item is used as dynamic `href` attribute for the anchor tag. Not only can JavaScript in JSX be used to display items, but also to assign HTML attributes dynamically.

map 函数简洁地内联在你的 JSX 中。 在 map 函数中，我们可以访问每个项目及其属性。 每个项目的 `url` 属性都可用作锚标签 `<a>` 的动态 `href` 属性。 JSX 中的 JavaScript 不仅可以用于显示项目，还可以动态分配 HTML 属性。

> ### Exercises:

### 练习:

> - Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Lists-in-React).

- 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Lists-in-React)。

> - Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-JSX...hs/Lists-in-React?expand=1).

- 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-JSX...hs/Lists-in-React?expand=1)。

> - Read more about why React's key attribute is needed ([0](https://dev.to/jtonzing/the-significance-of-react-keys---a-visual-explanation--56l7), [1](https://www.robinwieruch.de/react-list-key), [2](https://reactjs.org/docs/lists-and-keys.html)). Don't worry if you don't understand the implementation yet, just focus on what problem it causes for dynamic lists.

- 阅读更多关于为何需要 React 的 key 属性的信息（[0](https://dev.to/jtonzing/the-significance-of-react-keys---a-visual-explanation--56l7)，[1](https://www.robinwieruch.de/react-list-key)，[2](https://reactjs.org/docs/lists-and-keys.html)）。 如果你还不了解实现，请不要担心，只需关注它对动态列表造成的问题。

> - Recap the [standard built-in array methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/) -- especially _map_, _filter_, and _reduce_ -- which are available in native JavaScript.

- 回顾[标准内置数组方法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/) -- 尤其是原生 JavaScript 中可用的 _map_ ，_filter_ 和 _reduce_ 。

> - What happens if your return `null` instead of the JSX?

- 如果你返回 `null` 而不是 JSX，会发生什么？

> - Extend the list with some more items to make the example more realistic.

- 用更多项目扩展列表，让示例更真实。

> - Practice using different JavaScript expressions in JSX.

- 练习在 JSX 中使用不同的 JavaScript 表达式。

