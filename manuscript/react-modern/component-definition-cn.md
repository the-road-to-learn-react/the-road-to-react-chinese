## React 函数定义 （高阶）

## React Component Definition (Advanced)

> The following refactorings are optional recommendations to explain the different JavaScript/React patterns. You can build complete React applications without these advanced patterns, so don't be discouraged if they seem too complicated.

以下的重构建议是可选的，用来辅助说明 JavaScript/React 不同的模式。就算不用这些模式，你一样可以做出完整的 React 应用，所以如果下面这些内容看起来太复杂了，也不需要灰心。

> All components in the *src/App.js* file are function component. JavaScript has multiple ways to declare functions. So far, we have used the function statement, though arrow functions can be used more concisely:

*src/App.js* 里所有的组件都是函数组件。JavaScript 有很多种声明函数的方式。到目前为止，我们都是用 function 来声明，但箭头函数看起来会更简洁一些：

{title="Code Playground",lang="javascript"}

~~~~~~~
// function declaration
function () { ... }

// arrow function declaration
const () => { ... }
~~~~~~~

> You can remove the parentheses in an arrow function expression if it has only one argument, but multiple arguments require parentheses:

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

> Defining React function components with arrow functions makes them more concise:

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

> This holds also true for other functions, like the one we used in our JavaScript array's map method:

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

> If an arrow function doesn't do *anything* in between, but only returns *something*, -- in other words, if an arrow function doesn't perform any task, but only returns information --, you can remove the **block body** (curly braces) of the function. In a **concise body**, an **implicit return statement** is attached, so you can remove the return statement:

如果一个箭头函数什么也没做，只是返回了些**什么结果**，——换句话说，如果一个箭头函数没有执行任何任务，只返回信息——，你还可以去掉**函数体**（花括号）。在**简式函数体**中，会附一个**隐式的返回声明**，所以 return 也是可以去掉的：

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

> This can be done for the App and List component as well, because they only return JSX and don't perform any task in between. Again it also applies for the arrow function that's used in the map function:

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

> Our JSX is more concise now, as it omits the function statement, the curly braces, and the return statement. However, remember this is an optional step, and that it's acceptable to use normal functions instead of arrow functions and block bodies with curly braces for arrow functions over implicit returns. Sometimes block bodies will be necessary to introduce more business logic between function signature and return statement:

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

> Be sure to understand this refactoring concept, because we'll move quickly from arrow function components with and without block bodies as we go. Which one we use will depend on the requirements of the component.

确保你理解了这个重构的概念，因为后面我们将在有和没有函数体的箭头函数组件之间灵活切换，取决于组件的需求来决定使用哪种形式。

### 练习：

* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Definition).
  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-another-React-Component...hs/React-Component-Definition?expand=1).
* Read more about [JavaScript arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions).
* Familiarize yourself with arrow functions with block body and return, and concise body without return.

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Definition)。
	* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-another-React-Component...hs/React-Component-Definition?expand=1)。
* 阅读更多关于 [JavaScript 箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)的文章。
* 熟练掌握有函数体和返回的箭头函数、以及没有返回语句的简洁写法。