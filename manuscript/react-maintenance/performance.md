# React 维护

一旦 React 应用开始变得越来越庞大，维护将会成为重中之重。我们将介绍性能优化，类型安全，测试以及项目结构等方面来应对这种情况。每一个方面都能在不降低质量的情况下增强您的应用，以能承担更多的功能。

性能优化通过确保有效利用可用资源来防止应用程序变慢；诸如 TypeScript 之类的类型化编程语言会在反馈循环中更早的发现错误；相比于类型化编程，测试给了我们更多明确的反馈，并且提供了一种了解哪些操作可能对应用造成危害的方式；最后，项目结构支持将素材组织到文件和文件夹中，这在团队成员异地合作的情况下十分有用。

## React 性能（高级）

本节只是为了学习 React 的性能改进而已。因为 React 开箱即用的特点，我们在大多数 React 应用中并不需要优化。尽管有很多检测 JavaScript 和 React 性能的更复杂的工具，但是我们会坚持使用简单的 `console.log()` 和我们的浏览器开发者工具来记录日志。

### 不要在第一次渲染时运行

之前，我们介绍了 React 中用于产生副作用的 useEffect Hook。它在组件第一次渲染或挂载时运行，然后每次重新渲染或更新也会运行。通过将空的依赖数组作为第二个参数传递个它，我们就能告诉 Hook 仅在第一次渲染时运行。比如，使用 React 的 useState Hook 检查自定义 Hook 的状态管理，并使用 React 的 useEffect Hook 将其半永久状态与 local storage 一起使用：

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (key, initialState) => {
  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
# leanpub-start-insert
    console.log('A');
# leanpub-end-insert
    localStorage.setItem(key, value);
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

仔细研究开发者工具，我们使用此自定义 Hook 能发现第一次组件渲染时的日志。为组件的初始渲染运行副作用没有任何意义，因为除了初始值之外，没有其他东西可以存储在 local storage 中。所以这是一个多余的函数调用，应该仅在每次组件更新或重新渲染时运行。

正如前面所提到的，没有在每次重新渲染时运行的 Hook，也没有办法以 React 管用的方式告诉 `useEffect` Hook 仅在每次重新渲染时调用其函数。然而，通过使用 React 的 useRef Hook 使其在重新渲染时保持其 `ref.current` 属性不变，我们可以保持组件生命周期的 *组成状态* （无需在状态更新时重新渲染组件）：

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (key, initialState) => {
# leanpub-start-insert
  const isMounted = React.useRef(false);
# leanpub-end-insert

  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
# leanpub-start-insert
    if (!isMounted.current) {
      isMounted.current = true;
    } else {
# leanpub-end-insert
      console.log('A');
      localStorage.setItem(key, value);
# leanpub-start-insert
    }
# leanpub-end-insert
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

我们正在将 `ref` 及其可变的 `current` 属性用于状态管理，该操作并不会触发重新渲染。一旦 Hook 在组件第一次渲染时被调用，那么就会用一个名为 `isMounted` 的 `false` 布尔变量初始化 ref 的 `current` 值。结果是，并没有调用 `useEffet` 中的副作用函数，而副作用函数只有当布尔变量 `isMounted` 变为 `true` 后才会被调用。每当 Hook 再次运行（或重新渲染组件）时，都会在副作用中判断布尔值变量，只有当变量为 `true` 时，才会执行副作用函数。在组件的整个生命周期中，`isMounted` 布尔值将一直为 `true`，这样做是为了避免第一次使用我们的自定义 Hook 时调用副作用函数。

以上只是关于防止组件第一次渲染时调用一个简单的函数的问题，但是想象一下在副作用函数中有一个开销很大的计算，或者自定义 Hook 在应用中被频繁的使用，那么使用这种方法就可以更实用地避免不必要的函数调用。

*提示：这种方式不仅仅适用于性能优化，还可以为了仅在组件重新渲染时产生副作用而使用。我使用过几次这种方式，并且我认为您也能发现更多这种方式适用的场景。*

### 不要进行不必要重新渲染

在前面，我们探讨了 React 的重新渲染机制，接下来我们将为了应用和 List 组件重复此练习。为这两个组件添加一条日志记录。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  console.log('B:App');
# leanpub-end-insert

  return ( ... );
};

const List = ({ list, onRemoveItem }) =>
# leanpub-start-insert
  console.log('B:List') ||
# leanpub-end-insert
  list.map(item => (
    <Item
      key={item.objectID}
      item={item}
      onRemoveItem={onRemoveItem}
    />
  ));
~~~~~~~

因为 List 组件没有函数体，并且开发人员很懒，不想为简单的日志记录重构该组件，所以 List 组件使用了 `||` 运算符。这是一个很 tricky 的方式向没有函数体的功能组件添加日志记录。因为在操作符左侧的 `console.log()` 始终为 false，所以操作符右侧的总会运行。

{title="Code Playground",lang="javascript"}
~~~~~~~
function getTheTruth() {
  if (console.log('B:List')) {
    return true;
  } else {
    return false;
  }
}

console.log(getTheTruth());
// B:List
// false
~~~~~~~

让我们关注在浏览器开发者工具中真正的日志，你应该看到相似的输出，首先 App 组件渲染，然后就是它的子组件（比如：List 组件）。

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
B:App
B:App
B:List
~~~~~~~

由于副作用在第一次渲染时触发了获取数据的操作，因此只有 App 组件会渲染，因为 List 组件被加载状态替换了。当数据获取完成之后，两个组件都会再次渲染。

{title="Visualization",lang="text"}
~~~~~~~
// initial render
B:App
B:List

// data fetching with loading
B:App

// re-rendering with data
B:App
B:List
~~~~~~~

到目前为止，这种行为是可以接收的，因为所有内容都按时渲染了。现在我们将通过在 SearchForm 组件中输入内容，使该实验更进一步。当您每在元素中输入一个字符后，您应该会看到相应的改变：

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

但是 List 组件不应该重新渲染，搜索功能不是通过按钮执行的，因此传递给 List 组件的 `list` 应该保持不变。这就是让很多人感到惊讶的 React 的默认行为。

如果一个父组件重新渲染，他的子组件也会随之重新渲染。React 默认就会这么做，因为阻止子组件的重新渲染可能会导致很多问题，并且 React 的重新渲染机制执行的很快。

可是有时候我们想防止组件重新渲染。比如，如果表格中展示的巨大的数据集合并不受更新的影响，那么就不应该重新渲染。如果组件有所更改，则执行相等性的检查会更有效。因此我们可以使用 React 的 memo API 对 props 进行相等性检查：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = React.memo(
# leanpub-end-insert
  ({ list, onRemoveItem }) =>
    console.log('B:List') ||
    list.map(item => (
      <Item
        key={item.objectID}
        item={item}
        onRemoveItem={onRemoveItem}
      />
    ))
# leanpub-start-insert
);
# leanpub-end-insert
~~~~~~~

然而，当向 SearchForm 中输入值的时候输出仍然没有改变：

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

虽然传给 List 组件的 `list` 是相同的，但是 `onRemoveItem` 回调方法却不是，如果 App 组件重新渲染了，它依然会创建一个新的回调。此前，我们使用 React 的 useCallback Hook 阻止这一行为，仅在组件重新渲染并且依赖项中有改变时才创建一个函数。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleRemoveStory = React.useCallback(item => {
# leanpub-end-insert
    dispatchStories({
      type: 'REMOVE_STORY',
      payload: item,
    });
# leanpub-start-insert
  }, []);
# leanpub-end-insert

  ...

  console.log('B:App');

  return (... );
};
~~~~~~~

因为回调函数在函数签名中获取了作为参数传递的 `item`，所以它没有任何依赖关系，并且只在 App 组件初始渲染的时候被声明了一次。传递给 List 组件的 props 都不应该变化，尝试着将 `memo` 和 `useCallback` 一起使用来搜索输入在 SearchForm 中的内容。

![](images/memo.png)

虽然传递给组件的所有 props 都保持不变，但是如果它们的父组件强制重新渲染，那么它们也会再次渲染。这是 React 的默认行为，因为重新渲染的机制足够快，所以它在大多数情况下都是这么运作。但是，如果重新渲染降低了 React 应用的性能，那么 `memo` 将有助于防止重新渲染。

有时单独使用 `memo` 并没有什么用，每次父组件中都会重新定义回调，并且将其作为*变化了的* props 传给该组件，这会触发另一次重新渲染。在这种情况下，`useCallback` 可以使回调只有在依赖项发生变化时才进行改变。

### 不要反复运行大开销的计算

有时候，每次渲染都会在 React 组件的函数签名和 return 块之间进行频繁的计算。对于这种情况，我们必须首先在我们当前的应用程序中创建一个用例。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const getSumComments = stories => {
  console.log('C');

  return stories.data.reduce(
    (result, value) => result + value.num_comments,
    0
  );
};
# leanpub-end-insert

const App = () => {
  ...

# leanpub-start-insert
  const sumComments = getSumComments(stories);
# leanpub-end-insert

  return (
    <div>
# leanpub-start-insert
      <h1>My Hacker Stories with {sumComments} comments.</h1>
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

如果所有的参数都传递给函数，那么就可以允许在组件外部使用它。它防止了每次渲染都创建一个函数，因此 `useCallback` Hook 变得没那么必要了。但该函数仍然在每次渲染时计算 comments 的总和，这对于更大开销的计算来说就成为了一个问题。

![](images/usememo-1.png)

每一次在 SearchForm 组件输入内容时，此计算都会再次输出 “C”。对于像这种没那么大的开销的计算来说还可以接受，但是可以想象一下如果计算时间要花费500毫秒以上，这会给重新渲染带来延迟，因此组件中所有的组件都必须等待此计算。我们可以告诉 React 仅在其依赖项之一改变的情况下运行函数，如果未更改任何依赖关系，那么函数结果将保持不变。React 的 useMemo Hook 在这里为我们提供了帮助：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const sumComments = React.useMemo(() => getSumComments(stories), [
    stories,
  ]);
# leanpub-end-insert

  return ( ... );
};
~~~~~~~

每次在 SearchForm 中输入内容的时候，不会再次计算，只有当依赖项的数组（在这儿指 `stories`）改变时才会再次运行。毕竟，这只应用于那些因计算量开销很大而导致渲染（或重新渲染）时造成延迟的组件。

![](images/usememo-2.png)

现在，我们已经了解了使用 `useMemo`、`useCallback` 和 `memo` 的场景，请记住一般情况下不一定必须使用这些工具。只有在遇到性能瓶颈时才应用这些优化方式，大多数情况下并不需要优化，因为 React 的渲染机制非常高效。有时，像 `memo` 之类优化方式的开销比组件重新渲染的开销还要大。

### 练习

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Performance-in-React).
* 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Performance-in-React?expand=1).
* 阅读更多关于 [React 的 memo API](https://reactjs.org/docs/react-api.html#reactmemo).
* 阅读更多关于 [React 的 useCallback Hook](https://reactjs.org/docs/hooks-reference.html#usecallback).
* 下载 *React 开发者工具* 作为浏览器拓展程序，并通过浏览器的开发者工具在浏览器的应用程序打开它，然后尝试其各种功能。比如，您可以用它可视化 React 组件树并且更新组件。
* 使用“删除”按钮从 List 中移除一项时 SearchForm 会重新渲染吗？如果会的话，请使用上述性能优化的方法防止其重新渲染。
* 当从 List 中移除一项时，List 中的其余项会重新渲染吗？如果会的话，请使用上述性能优化的方法防止其重新渲染。
* 移除所有的性能优化操作保持应用的简单性，我们现在的应用程序并没有遇到性能瓶颈。尽量避免[过早优化](https://en.wikipedia.org/wiki/Program_optimization)，如果遇到性能问题，请参考本节内容。
