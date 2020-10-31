## React 函数定义 （高阶）

以下的重构建议是可选的，用来辅助说明 JavaScript/React 不同的模式。就算不用这些模式，你一样可以做出完整的 React 应用，所以如果下面这些内容看起来太复杂了，也不需要灰心。

*src/App.js* 里所有的组件都是函数组件。JavaScript 有很多种声明函数的方式。到目前为止，我们都是用 function 来声明，但箭头函数看起来会更简洁一些：

{title="Code Playground",lang="javascript"}
~~~~~~~
// function declaration
function () { ... }

// arrow function declaration
const () => { ... }
~~~~~~~

在声明箭头函数时，如果只有一个参数，可以把括号去掉。但有多个参数的时候，括号是必须的：

{title="Code Playground",lang="javascript"}
~~~~~~~
// allowed
const item => { ... }

// allowed
const (item) => { ... }

// not allowed
const item, index => { ... }

// allowed
const (item, index) => { ... }
~~~~~~~

用箭头函数声明 React 函数组件会让它们看起来更加简洁：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const App = () => {
# leanpub-end-insert
  return (
    <div>
      ...
    </div>
  );
# leanpub-start-insert
};
# leanpub-end-insert

# leanpub-start-insert
const List = () => {
# leanpub-end-insert
  return list.map(function(item) {
    return (
      <div key={item.objectID}>
        ...
      </div>
    );
  });
# leanpub-start-insert
};
# leanpub-end-insert
~~~~~~~

对于其他的函数也一样，比如我们用到的 JavaScript 数组的 map 函数：

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = () => {
# leanpub-start-insert
  return list.map(item => {
# leanpub-end-insert
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
};
~~~~~~~

如果一个箭头函数*什么也没做*，只是返回了*某些结果*，—— 换句话说，如果一个箭头函数没有执行任何任务，只返回信息——，你还可以去掉**块体**（花括号）。在**简写体**中，会附一个**隐式的返回声明**，所以 return 也是可以去掉的：

{title="Code Playground",lang="javascript"}
~~~~~~~
// with block body
count => {
  // perform any task in between

  return count + 1;
}

// with concise body
count =>
  count + 1;
~~~~~~~

这也可以应用在 App 和 List 组件上，因为它们除了返回 JSX 之外什么都没做。同样也可以用在 map 里的箭头函数上：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const App = () => (
# leanpub-end-insert
  <div>
    ...
  </div>
# leanpub-start-insert
);
# leanpub-end-insert

# leanpub-start-insert
const List = () =>
  list.map(item => (
# leanpub-end-insert
    <div key={item.objectID}>
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
      <span>{item.num_comments}</span>
      <span>{item.points}</span>
    </div>
# leanpub-start-insert
  ));
# leanpub-end-insert
~~~~~~~

去掉了 function 声明、花括号和 return 语句后，我们的 JSX 现在看起来更简洁了。但始终要记得，这只是可选的步骤，使用一般的函数声明、而不是箭头函数，带花括号的箭头函数体、而非省略它，这些都是可接受的。有时函数体是必要的，当我们需要在函数签名和返回语句之间引入业务逻辑时：

{title="Code Playground",lang="javascript"}
~~~~~~~
const App = () => {
  // perform any task in between

  return (
    <div>
      ...
    </div>
  );
};
~~~~~~~

确保你理解了这个重构的概念，因为后面我们将在有和没有函数体的箭头函数组件之间灵活切换，取决于组件的需求来决定使用哪种形式。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Definition)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-another-React-Component...hs/React-Component-Definition?expand=1)。
* 阅读更多关于 [JavaScript 箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)的文章。
* 熟练掌握有块体和返回的箭头函数、以及没有返回语句的简写体。
