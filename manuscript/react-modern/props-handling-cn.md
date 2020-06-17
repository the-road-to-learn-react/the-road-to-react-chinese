## Props Handling (Advanced)

## Props 处理（高级）

> Props are passed from parent to child down the component tree. Since we use props to transport information from component to component frequently, and sometimes via other components which are in between, it is useful to know a few tricks to make passing props more convenient.

Props 经组件树由父级向下传递给子级。由于我们经常使用 props 在组件间传递信息，有时也会通过其他中间组件来进行传递，所以知道一些小技巧可以让传递 props 更方便。

> *Note: The following refactorings are recommended for you to learn different JavaScript/React patterns, though you can still build complete React applications without them. Consider this advanced React techniques that will make your source code more concise.*

*注意：下面推荐的重构是为了让你学习不同的 JavaScript/React 模式，尽管在没有这些模式的情况下你仍然可以构建完整的 React 应用程序。考虑一下这些先进的 React 技术，会让你的源代码更加简洁。*

> React props are a JavaScript object, else we couldn't access `props.list` or `props.onSearch` in React components. Since `props` is an object which just passes information from one component to another component, we can apply a couple JavaScript tricks to it. For example, accessing an object's properties with modern [JavaScript object destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment):

React props 是一个 JavaScript 对象，否则我们就无法访问 React 组件中的 `props.list` 或 `props.onSearch` 。因为 `props` 是一个对象，它只是将信息从一个组件传递给另一个组件，所以我们可以对它应用一些 JavaScript 技巧。例如，用现代的 [JavaScript 对象解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)来访问对象的属性：

{title="Code Playground",lang="javascript"}

~~~~~~~
const user = {
 firstName: 'Robin',
 lastName: 'Wieruch',
};

// without object destructuring
const firstName = user.firstName;
const lastName = user.lastName;

console.log(firstName + ' ' + lastName);
// "Robin Wieruch"

// with object destructuring
const { firstName, lastName } = user;

console.log(firstName + ' ' + lastName);
// "Robin Wieruch"
~~~~~~~

> If we need to access multiple properties of an object, using one line of code instead of multiple lines is often simpler and more elegant. That's why object destructuring is already widely used in JavaScript. Let's transfer this knowledge to the React props in our Search component. First, we have to refactor the Search component's arrow function from concise body into block body:

如果我们需要访问一个对象的多个属性，用一行代码来代替多行代码往往更简单、更优雅。这就是为什么在 JavaScript 中已经广泛使用对象解构的原因。让我们把这些知识运用到我们的 Search 组件中的 React props 上。首先，我们要把 Search 组件的箭头函数从简写体重构成块状体：

{title="src/App.js",lang="javascript"}

~~~~~~~
# leanpub-start-insert
const Search = props => {
 return (
# leanpub-end-insert
   <div>
     <label htmlFor="search">Search: </label>
     <input
       id="search"
       type="text"
       value={props.search}
       onChange={props.onSearch}
     />
   </div>
# leanpub-start-insert
 );
};
# leanpub-end-insert
~~~~~~~

> And second, we can apply the destructuring of the `props` object in the component's function body:

接着，我们可以在组件的函数体中对 `props` 对象进行解构：

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

> That's a basic destructuring of the `props` object in a React component, so that the object's properties can be used conveniently in the component. However, we also had to refactor the Search component's arrow function from concise body into block body to access the properties of `props` with the object destructuring in the function's body. This would happen quite often if we followed this pattern, and it wouldn't make things easier for us, because we would constantly have to refactor our components. We can take all this one step further by destructuring the `props` object right away in the function signature of our component, omitting the function's block body of the component again:

这就是对 React 组件中的 `props` 对象的基本解构，这样就可以在组件中方便的使用 `props` 的属性。但是，我们也不得不将 Search 组件的箭头函数从简写体重构为块体，在函数体中用对象解构来访问 `props` 的属性。如果我们按照这样的模式来做的话，这样的情况会经常发生，而且对我们来说并不容易，因为我们必须不断地重构我们的组件。我们可以将这一切更进一步，直接在组件的函数签名中对 `props` 对象进行解构，再次省略组件的块体：

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
     value={search}
     onChange={onSearch}
   />
 </div>
 # leanpub-start-insert
);
# leanpub-end-insert
~~~~~~~

> React's `props` are rarely used in components by themselves; rather, all the information that is contained in the `props` object is used. By destructuring the `props` object right away in the function signature, we can conveniently access all information without dealing with its `props` container. This should be the basic lesson learned from this section, however, we can take this one step further with the following advanced lessons.

React `props` 本身很少在组件中使用，相反，所有包含在 `props` 对象中的信息都会被使用。通过在函数签名中马上解构 `props` 对象，我们可以方便地访问所有信息，而不需要处理 `props` 容器。这是本节的基本知识，不过，我们可以通过下面的高级课程更进一步。

> Let's check out another scenario to dive deeper into advanced props handling in React: In order to prepare for this scenario, we will extract a new Item component from the List component with the previous lesson learned about object destructuring for React's `props` object:

让我们以另一个场景来深入学习 React 中 props 处理的高级方式：为了准备这个场景，我们将用之前学过的关于 React 的 props 对象解构的知识，从 List 组件中提取一个新的 Item 组件：

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

> Now, the incoming `item` in the Item component has something in common with the previously discussed `props`: they are both JavaScript objects. Also, even though the `item` object has already been destructured from the `props` in the Item component's function signature, it isn't directly used in the Item component. The `item` object only passes its information (object properties) to the elements.

现在，Item 组件中的入参 `item` 与前面讨论的 `props` 有一个共同点：它们都是 JavaScript 对象。另外，即使 `item` 对象已经从 Item 组件的函数签名中的 `props` 中解构了，但它并没有直接在 Item 组件中使用。`item` 对象只是将其信息（对象属性）传递给了元素。

> The shown solution is fine as you will see in the ongoing discussion. However, I want to show you two more variations of it, because there are many things to learn about JavaScript objects here. Let's get started with *nested destructuring* and how it works:

你会在接下来持续的讨论中看到，上面代码中所示的解决方案是没有问题的。不过，我想再给大家展示另外两种变体，因为这里有很多关于 JavaScript 对象的知识需要学习。让我们从*嵌套解构*开始，看看它是如何工作的:

{title="Code Playground",lang="javascript"}

~~~~~~~
const user = {
  firstName: 'Robin',
  pet: {
    name: 'Trixi',
  },
};

// without object destructuring
const firstName = user.firstName;
const name = user.pet.name;

console.log(firstName + ' has a pet called ' + name);
// "Robin has a pet called Trixi"

// with nested object destructuring
const {
  firstName,
  pet: {
    name,
  },
} = user;

console.log(firstName + ' has a pet called ' + name);
// "Robin has a pet called Trixi"
~~~~~~~

> Nested destructuring helps us to access properties from objects which are deeply nested (e.g. the pet's name of a user). Now, in our Item components, because the `item` object is never directly used in the Item component's JSX elements, we can perform a *nested destructuring* in the component's function signature too:

嵌套解构可以帮助我们从深度嵌套的对象中访问其属性（比如说 user 中 pet 的 name 属性）。现在，在我们的 Item 组件中，由于 item 对象从来没有在 Item 组件的 JSX 元素中被直接使用过，所以我们也可以在组件的函数签名中进行嵌套解构：

{title="src/App.js",lang="javascript"}

~~~~~~~
// Variation 1: Nested Destructuring

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

> The nested destructuring helps us to gather all the needed information of the `item` object in the function signature for its immediate usage in the component's elements. However, nested destructuring introduces lots of clutter through indentations in the function signature. While it's here not the most readable option, it can be useful in other scenarios though.

嵌套解构可以帮助我们在函数签名中收集所有需要的 `item` 对象的信息，以便在组件元素中直接使用。然而，嵌套解构在函数签名中因为缩进而引入了很多杂乱的信息。虽然它在这里不是最可读的方式，但在其他场景中还是很有用的。

> Let's take another approach with JavaScript's spread and rest operators. In order to prepare for it, we will refactor our List and Item components to the following implementation. Rather than passing the item as object from List to Item component, we are passing every property of the `item` object:

让我们利用 JavaScript 的展开运算符和剩余运算符进行另一种实现。为了做好准备，我们将把 List 和 Item 组件重构为以下的实现。我们不再将 List 组件中的 `item` 作为对象传递给 Item 组件，而是将 `item` 对象的每一个属性都传递给它：

{title="src/App.js",lang="javascript"}

~~~~~~~
// Variation 2: Spread and Rest Operators
// 1. Iteration

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

> Now, even though the Item component's function signature is more concise, the clutter ended up in the List component instead, because every property is passed to the Item component individually. We can improve this approach using [JavaScript's spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax):

现在，尽管 Item 组件的函数签名更简洁了，但这些杂乱无章的东西最后反而落在了 List 组件中，因为每个属性都是单独传递给 Item 组件的。我们可以使用 [JavaScript 的展开运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)来改进这种方法：

{title="Code Playground",lang="javascript"}

~~~~~~~
const profile = {
 firstName: 'Robin',
 lastName: 'Wieruch',
};

const address = {
 country: 'Germany',
 city: 'Berlin',
 code: '10439',
};

const user = {
  ...profile,
  gender: 'male',
  ...address,
};

console.log(user);
// {
//   firstName: "Robin",
//   lastName: "Wieruch",
//   gender: "male"
//   country: "Germany,
//   city: "Berlin",
//   code: "10439"
// }
~~~~~~~

> JavaScript's spread operator allows us to literally spread all key/value pairs of an object to another object. This can also be done in React's JSX. Instead of passing each property one at a time via props from List to Item component as before, we can use JavaScript's spread operator to pass all the object's key/value pairs as attribute/value pairs to a JSX element:

JavaScript 的展开运算符允许我们将一个对象的所有键/值对分散到另一个对象上。这也可以在 React 的 JSX 中实现。我们可以使用 JavaScript 的展开运算符来将对象的所有键/值对作为属性/值对传递给 JSX 元素，而不是像以前那样通过 props 从 List 组件到 Item 组件一次一个地传递每个属性：

{title="src/App.js",lang="javascript"}

~~~~~~~
// Variation 2: Spread and Rest Operators
// 2. Iteration

const List = ({ list }) =>
# leanpub-start-insert
 list.map(item => <Item key={item.objectID} {...item} />);
# leanpub-end-insert

const Item = ({ title, url, author, num_comments, points }) => (
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

> This refactoring made the process of passing the information from List to Item component more concise. Finally, we'll use [JavaScript's rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) as the icing on the cake. The JavaScript rest operator happens always as the last part of an object destructuring:

这个重构使得从 List 组件到 Item 组件传递信息的过程更加简洁。最后，我们用 [JavaScript 的剩余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)锦上添花。JavaScript 的剩余操作符总是出现在一个对象解构的最后：

{title="Code Playground",lang="javascript"}

~~~~~~~
const user = {
 id: '1',
 firstName: 'Robin',
 lastName: 'Wieruch',
 country: 'Germany',
 city: 'Berlin',
};

const { id, country, city, ...userWithoutAddress } = user;

console.log(userWithoutAddress);
// {
//   firstName: "Robin",
//   lastName: "Wieruch"
// }

console.log(id);
// "1"

console.log(city);
// "Berlin"
~~~~~~~

> Even though both have the same syntax (three dots), the rest operator shouldn't be mistaken with the spread operator. Whereas the rest operator happens on the right side of an assignment, the spread operator happens on the left side. The rest operator is always used to separate an object from some of its properties.

尽管两者的语法相同(都是三个点)，但剩余运算符不应该与展开运算符相混淆。剩余运算符发生在解构的右侧，而展开运算符发生在左侧。剩余运算符总是用来将一个对象和它的某些属性分开。

> Now it can be used in our List component to separate the `objectID` from the item, because the `objectID` is only used as `key` and isn't used in the Item component. Only the remaining (rest) item gets spread as attribute/value pairs into the Item component (as before):

现在可以在 List 组件中使用它来将 `objectID` 从 item 中分离出来，因为 `objectID` 只用作 `key`，不用于 Item 组件。 只有剩下的项作为属性/值对分散到 Item 组件中(如前所述) :

{title="src/App.js",lang="javascript"}

~~~~~~~
// Variation 2: Spread and Rest Operators (final)

const List = ({ list }) =>
# leanpub-start-insert
  list.map(({ objectID, ...item }) => <Item key={objectID} {...item} />);
# leanpub-end-insert

const Item = ({ title, url, author, num_comments, points }) => (
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

> In this final variation, the rest operator is used to destructure the `objectID` from the rest of the `item` object. Afterward, the `item` is spread with its key/values pairs into the Item component. While this final variation is very concise, it comes with  advanced JavaScript features that may be unknown to some.

在这个最后的变体中，剩余运算符被用来从 `item` 对象的其余部分中解构 `objectID`。之后，`item` 与它的键/值对被分散到 Item 组件中。虽然这个最后的变体非常简洁，但它包含了一些可能有些人不知道的高级 JavaScript 功能。

> In this section, we have learned about JavaScript object destructuring which can be used commonly for the `props` object, but also for other objects like the `item` object. We have also seen how nested destructuring can be used (Variation 1), but also how it didn't add any benefits in our case, because it just made the component bigger. In the future you will find more likely use cases for nested destructuring which are beneficial. Last but not least, you have learned about JavaScript's spread and rest operators, which shouldn't be confused with each other, to perform operations on JavaScript objects and to pass the `props` object from one component to another component in the most concise way. In the end, I want to point out the initial version again which we will keep over the next chapters:

在这一节中，我们学习了 JavaScript 的对象解构，这种方法通常用于 `props` 对象，也可以用于其他对象，比如 `item` 对象。我们也学习了如何使用嵌套解构（变体1），但在我们的例子中，它并没有增加任何好处，因为它只是让组件变大了。在未来，你会发现更多类似的嵌套解构的实用用例。最后但并非不重要的一点，你已经了解了 JavaScript 的展开运算符和剩余运算符，这两个运算符不应该相互混淆，它们可以在 JavaScript 对象上执行，并以最简洁的方式将 `props` 对象从一个组件传递给另一个组件。最后，我想再次指出，在接下来的章节中我们将保留这个最初的版本：

{title="src/App.js",lang="javascript"}

~~~~~~~
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

> It may not be the most concise, but it is the easiest to reason about. Variation 1 with its nested destructuring didn't add much benefit and variation 2 may add too many advanced JavaScript features (spread operator, rest operator) which are not familiar to everyone. After all, all these variations have their pros and cons. When refactoring a component, always aim for readability, especially when working in a team of people, and make sure make sure they're using a common React code style.

它可能不是最简洁的，但却是最容易理解的。变体1的嵌套解构并没有增加太多好处，而变体2可能会增加太多高级的JavaScript 功能（展开运算符、剩余运算符），而这些功能并不是每个人都熟悉。毕竟，所有这些变体都有利有弊。在重构一个组件时，一定要以可读性为目标，尤其是在一个团队中工作时，要确保他们使用的是通用的 React 代码风格。

### Exercises:

### 练习

>* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Props-Handling).
>* Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Controlled-Components...hs/Props-Handling?expand=1).
>* Read more about [JavaScript's destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).
>* Think about the difference between  JavaScript array destructuring -- which we used for React's `useState` hook -- and object destructuring.
>* Read more about [JavaScript's spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax).
>* Read more about [JavaScript's rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters).
>* Get a good sense about JavaScript (e.g. spread operator, rest parameters, destructuring) and what's related to React (e.g. props) from the last lessons.
>* Continue to use your favorite way to handle React's props. If you're still undecided, consider the variation used in the previous section.


* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Props-Handling)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Controlled-Components...hs/Props-Handling?expand=1)。
* 阅读更多关于 [JavaScript 的解构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)的信息。
* 思考 JavaScript 的数组解构 -- 被用在 React 的 `useState` hook 中 -- 和对象解构之间的区别。
* 阅读更多关于 [JavaScript 的展开运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)的信息。
* 阅读更多关于 [JavaScript 的剩余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)的信息。
* 从上一节课中了解一下 JavaScript （展开运算符，剩余参数，解构）以及与 React 相关的东西（例如 props）。
* 继续用你喜欢的方式去处理 React 的 props。如果你依然不确定使用哪种方式，考虑一下在上一节中的版本。
