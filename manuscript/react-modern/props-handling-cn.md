> ## Props Handling (Advanced)

## Props 处理(高级)

> Props are passed from parent to child down the component tree. Since we use props to transport information from component A to component B frequently, and sometimes via component C, it is useful to know a few tricks to make this more convenient.

Props 经组件树由父级向下传递给子级。因为我们经常用 props 将信息从组件 A 传递给组件 B，有时也会通过组件C传递，所以知道一些小技巧可以让我们做这些操作时更加方便。

> *Note: The following refactorings are recommended for you to learn different JavaScript/React patterns, though you can still build complete React applications without them. Consider this advanced React techniques that will make your source code more concise.*

*注意：接下来推荐的重构只是让你学习不同的 JavaScript/React 模式，尽管不使用这些模式你依然可以构建完整的 React 应用。考虑一下这些高级的 React 技术，会使你的代码更加简洁。*

> React props are a JavaScript object, else we couldn't access `props.list` or `props.onSearch` in React components. Since `props` is an object, we can apply a couple JavaScript tricks to it. For instance, accessing its properties:

React props 是一个JavaScript对象，否则我们在 React 的组件中就没办法访问 `props.list`或是`props.onSearch`。由于 props 是一个对象，所以我们可以对它使用一些 JavaScript 的技巧。例如，访问它的属性：

{title="Code Playground",lang="javascript"}

~~~~~~~
const user = {
 firstName: 'Robin',
 lastName: 'Wieruch',
};

// without object destructuring
// 不使用对象的结构赋值
const firstName = user.firstName;
const lastName = user.lastName;

console.log(firstName + ' ' + lastName);
// "Robin Wieruch"

// with object destructuring
// 使用对象的解构赋值
const { firstName, lastName } = user;

console.log(firstName + ' ' + lastName);
// "Robin Wieruch"
~~~~~~~

> This JavaScript feature is called [object destructuring in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment). If we need to access multiple properties of an object, using one line of code instead of multiple lines is simpler and more elegant. Let's transfer this knowledge to React props in our Search component:

这个 JavaScript 功能叫做[对象的解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。如果我们需要访问一个对象中的多个属性，使用这样的一行代码代替多行代码要更加简洁和优雅。让我们把这些知识运用到 Search 组件中的 React props 上：

{title="src/App.js",lang="javascript"}

~~~~~~~
const Search = props => {
# leanpub-start-insert
 const { search, onSearch } = props;
# leanpub-end-insert

 return (
   <div>
     <label htmlFor="search">Search: </label>
     <input
       id="search"
       type="text"
# leanpub-start-insert
       value={search}
       onChange={onSearch}
# leanpub-end-insert
     />
   </div>
 );
};
~~~~~~~

> That's a basic destructuring of the `props` object in a React component, so that its properties can be used in the component without the `props` object. We refactored the Search component's arrow function from concise body into block body to access the properties of `props`. This would happen quite often if we followed this pattern, and it wouldn't make things easier for us. We can take it a step further by destructuring the `props` right away in the function signature, omitting the block body again:

这就是对 React 组件里的 props 对象的一个基本解构赋值，使其属性可以在组件中不使用 props 对象。为了访问 props 的属性，我们将 Search 组件的箭头函数的函数体从简写体重构成了块体。如果我们采用这个模式的话，会经常做这样的重构，而且不会让代码变得更加简单。我们可以更进一步，直接在函数签名中结构 `props`，再次省略块体：

{title="src/App.js",lang="javascript"}

~~~~~~~
# leanpub-start-insert
const Search = ({ search, onSearch }) => (
# leanpub-end-insert
 <div>
   <label htmlFor="search">Search: </label>
   <input
     id="search"
     type="text"
# leanpub-start-insert
     value={search}
     onChange={onSearch}
# leanpub-end-insert
   />
 </div>
);
~~~~~~~

> React's `props` are rarely used in components by themselves; rather, all the information that comes with the `props` object used as a container that's actually used in the component. By destructuring the `props` object right away in the function signature, we can conveniently access all information without dealing with its container.

React 的 `props` 几乎不会在自己的组件内使用，相反，包含所有信息的 `props` 对象实际上在组件内被当做一个容器来使用。通过在函数签名中解构 `props` 对象，我们可以方便地访问所有信息，而不用处理这个容器。

> Let's check out another scenario where its less about the `props` object, and more about an object that comes from it. The same lesson is applicable for the `props` object. First, we will extract a new Item component from the List component:

让我们来看看另一种情况，在这种情况里，我们不太关注 `props` 对象，更关注来自于 `props` 的一个对象。当然，同样的经验也可以用在 `props` 对象上。首先，我们将从 List 组件中提取一个新的 Item 组件：

{title="src/App.js",lang="javascript"}

~~~~~~~
# leanpub-start-insert
const List = ({ list }) =>
 list.map(item => <Item key={item.objectID} item={item} />);
# leanpub-end-insert

# leanpub-start-insert
const Item = ({ item }) => (
 <div>
   <span>
     <a href={item.url}>{item.title}</a>
   </span>
   <span>{item.author}</span>
   <span>{item.num_comments}</span>
   <span>{item.points}</span>
 </div>
);
# leanpub-end-insert
~~~~~~~

> We applied previous lesson by destructuring the `props` object in the function signature of each component. The incoming `item` of the Item component could be seen the same as the `props`, because it's never directly used in the Item component. There are three potential ways to handle this problem. The first is to perform a *nested destructuring* in the component's function signature:

根据之前学到的，在每一个组件中我们在函数签名中解构 `props` 对象。Item 组件的入参 `item` 可以被看做和 `props` 相同，因为它在 Item 组件中没有被直接用到过。有三种已知的方法来处理这样的情况。第一种方法是在组件的函数签名中执行一次*嵌套解构*：

{title="src/App.js",lang="javascript"}

~~~~~~~
// version 1 (final)
const Item = ({
# leanpub-start-insert
 item: {
   title,
   url,
   author,
   num_comments,
   points,
 },
# leanpub-end-insert
}) => (
 <div>
   <span>
# leanpub-start-insert
     <a href={url}>{title}</a>
# leanpub-end-insert
   </span>
# leanpub-start-insert
   <span>{author}</span>
   <span>{num_comments}</span>
   <span>{points}</span>
# leanpub-end-insert
 </div>
);
~~~~~~~

> Nested destructuring introduces lots of clutter through indentations in the function signature. While it's not the most readable option, it can be useful in specific scenarios. On to the second way:

由于在函数签名中有缩进，嵌套解构的方式引入了大量的杂乱。虽然这种方式不是最易读的，但是在一些特殊情况下它是很有用的。接下来介绍第二种方式：

{title="src/App.js",lang="javascript"}

~~~~~~~
// version 2 (I)
const List = ({ list }) =>
 list.map(item => (
   <Item
     key={item.objectID}
# leanpub-start-insert
     title={item.title}
     url={item.url}
     author={item.author}
     num_comments={item.num_comments}
     points={item.points}
# leanpub-end-insert
   />
 ));

# leanpub-start-insert
const Item = ({ title, url, author, num_comments, points }) => (
# leanpub-end-insert
 <div>
   <span>
     <a href={url}>{title}</a>
   </span>
   <span>{author}</span>
   <span>{num_comments}</span>
   <span>{points}</span>
 </div>
);
~~~~~~~

> Even though the Item component's function signature is more concise, the clutter ended up in the List component instead, because every property is passed to the Item component individually. We can improve this technique using [JavaScript's spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax):

虽然 Item 组件的函数签名更加简洁了，但是 List 组件却很杂乱，因为每一个传入 Item 组件的属性都是单独传入的。我们可以通过使用 [JavaScript 的展开运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax)来改进这个问题：

{title="Code Playground",lang="javascript"}

~~~~~~~
const person = {
 firstName: 'Robin',
 lastName: 'Wieruch',
};

const details = {
 year: 1988,
 country: 'Germany',
};

const personWithDetails = { ...person, ...details };

console.log(personWithDetails);
// {
//   firstName: "Robin",
//   lastName: "Wieruch",
//   year: 1988,
//   country: "Germany"
// }
~~~~~~~

> Instead of passing each property one at a time via props from List to Item component, we could use JavaScript's spread operator to pass all the object's key/value pairs as attribute/value pairs to the JSX element:

我们可以用 JavaScript 的展开运算符，将对象的所有键/值对当做属性/值对一起传入 JSX 元素，而不是通过 props 从 List 到 Item 组件中逐一传递每一个属性：

{title="src/App.js",lang="javascript"}

~~~~~~~
// version 2 (II)
const List = ({ list }) =>
# leanpub-start-insert
 list.map(item => <Item key={item.objectID} {...item} />);
# leanpub-end-insert

const Item = ({ title, url, author, num_comments, points }) => (
 ...
);
~~~~~~~

> Then, we'll use [JavaScript's rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters):

然后，我们将使用 [JavaScript 的剩余参数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Rest_parameters)：

{title="Code Playground",lang="javascript"}

~~~~~~~
const person = {
 firstName: 'Robin',
 lastName: 'Wieruch',
 year: 1988,
};

const { year, ...personWithoutYear } = person;

console.log(personWithoutYear);
// {
//   firstName: "Robin",
//   lastName: "Wieruch"
// }
~~~~~~~

> The JavaScript rest operator is the last part of an object destructuring. It shouldn't be mistaken with the spread operator even though it has the same syntax. Usually, the rest operator happens on the right side of an assignment, with the spread operator on the left.

JavaScript 剩余参数运算符是对象解构的最后一部分。虽然它和扩展运算符语法相同，但不要将它们混淆。通常来说，剩余参数运算符会被用在一次赋值的最右边，而扩展运算符在左边。

> Now it can be used in our List component to separate the `objectID` from the item, because the `objectID` is only used as `key` and isn't used (so far) in the Item component. Only the remaining (rest) item gets spread as attribute/value pairs into the Item component (as before):

因为 `objectID` 仅仅被用作 `key` 而且（目前）没有用在 Item 组件中，所以现在可以在 List 组件中使用剩余参数运算符，将 `objectID` 从 item 参数中分离出来。只有剩下的（其余的） item （和以前一样）作为属性/值对被分散到Item组件中。

{title="src/App.js",lang="javascript"}

~~~~~~~
// version 2 (III/final)
const List = ({ list }) =>
# leanpub-start-insert
 list.map(({ objectID, ...item }) => (
   <Item key={objectID} {...item} />
# leanpub-end-insert
 ));
~~~~~~~

> While this version is very concise, it comes with  advanced JavaScript features that may be unknown to some. The third way of handling this situation is to keep it the same as before:

到目前为止的这个版本已经非常简洁，这些都来自于一些人还不知道的 JavaScript 的高级功能。第三种处理这种情况的方式是将它与之前保持一致：

{title="src/App.js",lang="javascript"}

~~~~~~~
// version 3 (final)
const List = ({ list }) =>
 list.map(item => <Item key={item.objectID} item={item} />);

const Item = ({ item }) => (
 <div>
   <span>
     <a href={item.url}>{item.title}</a>
   </span>
   <span>{item.author}</span>
   <span>{item.num_comments}</span>
   <span>{item.points}</span>
 </div>
);
~~~~~~~

> In my opinion, this is the best technique to use when working with other people on a React project. It's not as concise as version 2, but it's more readable, gives the Item component a smaller API surface, and doesn't add too many advanced JavaScript features (spread operator, rest operator).

在我看来，这是在 React 项目上与他人合作的最佳技术。虽然它并没有第二个版本那么简洁，但是要更加可读，它让 Item 组件的 API 面更小了，而且没有加上过多的高级 JavaScript 功能（展开运算符， 剩余运算符）。

> All these versions have their pros and cons. When refactoring a component, always aim for readability, especially when working in a team of people, and make sure make sure they're using a common React code style.

所有的这些版本都有他们各自的优缺点。当重构一个组件的时候，通常为了让其更加可读，特别是当与一个团队的人一起工作时，同时务必确认所有人都在使用相同的 React 代码风格。

### Exercises:

### 练习：

>* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Props-Handling).
> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Controlled-Components...hs/Props-Handling?expand=1).
>* Read more about [JavaScript's destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).
>* Think about the difference between  JavaScript array destructuring -- which we used for React's `useState` hook -- and object destructuring.
>* Read more about [JavaScript's spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax).
>* Read more about [JavaScript's rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters).
>* Get a good sense about JavaScript (e.g. spread operator, rest parameters, destructuring) and what's related to React (e.g. props) from the last lessons.
>* Continue to use your favorite way to handle React's props. If you're still undecided, consider the version used in the previous section.
>


* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Props-Handling)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Controlled-Components...hs/Props-Handling?expand=1)。
* 阅读更多关于 [JavaScript 的解构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。
* 思考 JavaScript 的数组解构 -- 用在 React 的 `useState` hook 中 -- 和对象解构之间的区别。
* 阅读更多关于 [JavaScript 的展开运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)。
* 阅读更多关于 [JavaScript 的剩余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)。
* 从上一节课中了解一下 JavaScript （展开运算符，剩余参数，解构）以及与 React 相关的东西（例如 props）。
* 继续用你喜欢的方式去处理 React 的 props。如果你依然不确定使用哪种方式，考虑一下在上一节中的版本。







