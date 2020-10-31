> # React Maintenance

# React 维护

> Once a React application grows, maintenance becomes a priority. To prepare for this eventuality, we'll cover performance optimization, type safety, testing, and project structure.  Each can strengthen your app to take on more functionality without losing quality.

一旦 React 应用开始变得越来越庞大，维护将会成为重中之重。我们将介绍性能优化，类型安全，测试以及项目结构等方面来应对这种情况。每一个方面都能在不降低质量的情况下增强您的应用，以能承担更多的功能。

> Performance optimization prevents applications from slowing down by assuring efficient use of available resource.  Typed programming languages like TypeScript detect bugs earlier in the feedback loop.  Testing gives us more explicit feedback than typed programming, and provides a way to understand which actions can break the application. Lastly, project structure supports the organized management of assets into folders and files, which is especially useful in scenarios where team members work in different domains.

性能优化通过确保有效利用可用资源来防止应用程序变慢；诸如 TypeScript 之类的类型化编程语言会在反馈循环中更早的发现错误；相比于类型化编程，测试给了我们更多明确的反馈，并且提供了一种了解哪些操作可能对应用造成危害的方式；最后，项目结构支持将素材组织到文件和文件夹中，这在团队成员异地合作的情况下十分有用。

> ## Performance in React (Advanced)

## React 性能（高级）

> This section is just here for the sake of learning about performance improvements in React. We wouldn't need optimizations in most React applications, as React is fast out of the box. While more sophisticated tools exist for performance measurements in JavaScript and React, we will stick to a simple `console.log()` and our browser's developer tools for the logging output.

本节只是为了学习 React 的性能改进而已。因为 React 开箱即用的特点，我们在大多数 React 应用中并不需要优化。尽管有很多检测 JavaScript 和 React 性能的更复杂的工具，但是我们会坚持使用简单的 `console.log()` 和我们的浏览器开发者工具来记录日志。

> ### Don't run on first render

### 不要在第一次渲染时运行

> Previously we covered React's useEffect Hook, which is used for side-effects. It runs the first time a component renders (mounting), and then every re-render (updating). By passing an empty dependency array to it as a second argument, we can tell the hook to run on the first render only. Out of the box, there is no way to tell the hook to run only on every re-render (update) and not on the first render (mount). For instance, examine this custom hook for state management with React's useState Hook and its semi persistent state with local storage using React's useEffect Hook:

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

> With a closer look at the developer's tools, we can see the log for a first time render of the component using this custom hook. It doesn't make sense to run the side-effect for the initial rendering of the component, because there is nothing to store in the local storage except the initial value. It's a redundant function invocation, and should only run for every update (re-rendering) of the component.

仔细研究开发者工具，我们使用此自定义 Hook 能发现第一次组件渲染时的日志。为组件的初始渲染运行副作用没有任何意义，因为除了初始值之外，没有其他东西可以存储在 local storage 中。所以这是一个多余的函数调用，应该仅在每次组件更新或重新渲染时运行。

> As mentioned, there is no React Hook that runs on every re-render, and there is no way to tell the `useEffect` hook in a React idiomatic way to call its function only on every re-render. However, by using React's useRef Hook which keeps its `ref.current` property intact over re-renders, we can keep a *made up state* (without re-rendering the component on state updates) of our component's lifecycle:

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


> We are exploiting the `ref` and its mutable `current` property for imperative state management that doesn't trigger a re-render. Once the hook is called from its component for the first time (component render), the ref's `current` is initialized with a `false` boolean called `isMounted`. As a result, the side-effect function in `useEffect` isn't called; only the boolean flag for `isMounted` is toggled to `true` in the side-effect. Whenever the hook runs again (component re-render), the boolean flag is evaluated in the side-effect. Since it's `true`, the side-effect function runs. Over the lifetime of the component, the `isMounted` boolean will remain`true`. It was there to avoid calling the side-effect function for the first time render that uses our custom hook.

我们正在将 `ref` 及其可变的 `current` 属性用于状态管理，该操作并不会触发重新渲染。一旦 Hook 在组件第一次渲染时被调用，那么就会用一个名为 `isMounted` 的 `false` 布尔变量初始化 ref 的 `current` 值。结果是，并没有调用 `useEffet` 中的副作用函数，而副作用函数只有当布尔变量 `isMounted` 变为 `true` 后才会被调用。每当 Hook 再次运行（或重新渲染组件）时，都会在副作用中判断布尔值变量，只有当变量为 `true` 时，才会执行副作用函数。在组件的整个生命周期中，`isMounted` 布尔值将一直为 `true`，这样做是为了避免第一次使用我们的自定义 Hook 时调用副作用函数。

> The above was only about preventing the invocation of one simple function for a component rendering for the first time. But imagine you have an expensive computation in your side-effect, or the custom hook is used frequently in the application. It's more practical to deploy this technique to avoid unnecessary function invocations.

以上只是关于防止组件第一次渲染时调用一个简单的函数的问题，但是想象一下在副作用函数中有一个开销很大的计算，或者自定义 Hook 在应用中被频繁的使用，那么使用这种方法就可以更实用地避免不必要的函数调用。

> *Note: This technique isn't only used for performance optimizations, but for the sake of having a side-effect run only when a component re-renders. I used it several times, and I suspect you'll stumble on one or the other use case for it eventually.*

*提示：这种方式不仅仅适用于性能优化，还可以为了仅在组件重新渲染时产生副作用而使用。我使用过几次这种方式，并且我认为您也能发现更多这种方式适用的场景。*

> ### Don't re-render if not needed

### 不要进行不必要重新渲染

> Earlier, we explored React's re-rendering mechanism. We'll repeat this exercise for the App and List components. For both components, add a logging statement.

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

> Because the List component has no function body, and developers are lazy folks who don't want to refactor the component for a simple logging statement, the List component uses the `||` operator instead. This is a neat trick for adding a logging statement to a function component without a function body. Since the `console.log()` on the left hand side of the operator always evaluates to false, the right hand side of the operator gets always executed.

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

> Let's focus on the actual logging in the browser's developer tools. You should see a similar output. First the App component renders, followed by its child components (e.g. List component).

让我们关注在浏览器开发者工具中真正的日志，你应该看到相似的输出，首先 App 组件渲染，然后就是它的子组件（比如：List 组件）。

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
B:App
B:App
B:List
~~~~~~~

> Since a side-effect triggers data fetching after the first render, only the App component renders, because the List component is replaced by a loading indicator in a conditional rendering. Once the data arrives, both components render again.

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

> So far, this behavior is acceptable, since everything renders on time. Now we'll take this experiment a step further, by typing into the SearchForm component's input field. You should see the changes with every character entered into the element:

到目前为止，这种行为是可以接收的，因为所有内容都按时渲染了。现在我们将通过在 SearchForm 组件中输入内容，使该实验更进一步。当您每在元素中输入一个字符后，您应该会看到相应的改变：

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

> But the List component shouldn't re-render. The search feature isn't executed via its button, so the `list` passed to the List component should remain the same. This is React's default behavior, which surprises many people.

但是 List 组件不应该重新渲染，搜索功能不是通过按钮执行的，因此传递给 List 组件的 `list` 应该保持不变。这就是让很多人感到惊讶的 React 的默认行为。

> If a parent component re-renders, its child components re-render as well. React does this by default, because preventing a re-render of child components could lead to bugs, and the re-rendering mechanism of React is still fast.

如果一个父组件重新渲染，他的子组件也会随之重新渲染。React 默认就会这么做，因为阻止子组件的重新渲染可能会导致很多问题，并且 React 的重新渲染机制执行的很快。

> Sometimes we want to prevent re-rendering, however. For example, huge data sets displayed in a table shouldn't re-render if they are not affected by an update. It's more efficient to perform an equality check if something changed for the component. Therefore, we can use React's memo API to make this equality check for the props:

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

> However, the output stays the same when typing into the SearchForm's input field:

然而，当向 SearchForm 中输入值的时候输出仍然没有改变：

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

> The `list` passed to the List component is the same, but the `onRemoveItem` callback handler isn't. If the App component re-renders, it always creates a new version of this callback handler. Earlier, we used React's useCallacbk Hook to prevent this behavior, by creating a function only on a re-render (if one of its dependencies has changed).

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

> Since the callback handler gets the `item` passed as an argument in its function signature, it doesn't have any dependencies and is declared only once when the App component initially renders. None of the props passed to the List component should change now. Try it with the combination of `memo` and `useCallback`, to search via the SearchForm's input field. The "B:List" output disappears, and only the App component re-renders with its "B:App" output.

因为回调函数在函数签名中获取了作为参数传递的 `item`，所以它没有任何依赖关系，并且只在 App 组件初始渲染的时候被声明了一次。传递给 List 组件的 props 都不应该变化，尝试着将 `memo` 和 `useCallback` 一起使用来搜索输入在 SearchForm 中的内容。

![](images/memo.png)

> While all props passed to a component stay the same, the component renders again if its parent component is forced to re-render. That's React's default behavior, which works most of the time because the re-rendering mechanism is fast enough. However, if re-rendering decreases the performance of a React application, `memo` helps prevent re-rendering.

虽然传递给组件的所有 props 都保持不变，但是如果它们的父组件强制重新渲染，那么它们也会再次渲染。这是 React 的默认行为，因为重新渲染的机制足够快，所以它在大多数情况下都是这么运作。但是，如果重新渲染降低了 React 应用的性能，那么 `memo` 将有助于防止重新渲染。

> Sometimes `memo` alone doesn't help, though. Callback handlers are re-defined each time in the parent component and passed as *changed* props to the component, which causes another re-render. In that case, `useCallback` is used for making the callback handler only change when its dependencies change.

有时单独使用 `memo` 并没有什么用，每次父组件中都会重新定义回调，并且将其作为*变化了的* props 传给该组件，这会触发另一次重新渲染。在这种情况下，`useCallback` 可以使回调只有在依赖项发生变化时才进行改变。

> ### Don't rerun expensive computations

### 不要反复运行大开销的计算

> Sometimes we'll have performance-intensive computations in our React components -- between a component's function signature and return block -- which run on every render. For this scenario, we must create a use case in our current application first.

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

> If all arguments are passed to a function, it's acceptable to have it outside the component. It prevents creating the function on every render, so the `useCallback` hook becomes unnecessary. The function still computes the value of summed comments on every render, which becomes a problem for more expensive computations.

如果所有的参数都传递给函数，那么就可以允许在组件外部使用它。它防止了每次渲染都创建一个函数，因此 `useCallback` Hook 变得没那么必要了。但该函数仍然在每次渲染时计算 comments 的总和，这对于更大开销的计算来说就成为了一个问题。

![](images/usememo-1.png)

> Each time text is typed in the input field of the SearchForm component, this computation runs again with an output of "C". This may be fine for a non-heavy computation like this one, but imagine this computation would take more than 500ms. It would give the re-rendering a delay, because everything in the component has to wait for this computation. We can tell React to only run a function if one of its dependencies has changed. If no dependency changed, the result of the function stays the same. React's useMemo Hook helps us here:

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

> For every time someone types in the SearchForm, the computation shouldn't run again. It only runs if the dependency array, here `stories`, has changed. After all, this should only be used for cost expensive computations which could lead to a delay of a (re-)rendering of a component.

每次在 SearchForm 中输入内容的时候，不会再次计算，只有当依赖项的数组（在这儿指 `stories`）改变时才会再次运行。毕竟，这只应用于那些因计算量开销很大而导致渲染（或重新渲染）时造成延迟的组件。

![](images/usememo-2.png)

> Now, after we went through these scenarios for `useMemo`, `useCallback`, and `memo`, remember that these shouldn't necessarily be used by default. Apply these performance optimization only if you run into a performance bottlenecks. Most of the time this shouldn't happen, because React's rendering mechanism is pretty efficient by default. Sometimes the check for utilities like `memo` can be more expensive than the re-rendering itself.

现在，我们已经了解了使用 `useMemo`、`useCallback` 和 `memo` 的场景，请记住一般情况下不一定必须使用这些工具。只有在遇到性能瓶颈时才应用这些优化方式，大多数情况下并不需要优化，因为 React 的渲染机制非常高效。有时，像 `memo` 之类优化方式的开销比组件重新渲染的开销还要大。

> ### Exercises:

### 练习：

> * 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Performance-in-React).
  * 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Performance-in-React?expand=1).
* 阅读更多关于 [React 的 memo API](https://reactjs.org/docs/react-api.html#reactmemo).
* 阅读更多关于 [React 的 useCallback Hook](https://reactjs.org/docs/hooks-reference.html#usecallback).
> * Download *React Developer Tools* as an extension for your browser. Open it for your application in the browser via the browser's developer tools and try its various features. For example, you can use it to visualize React's component tree and its updating components.
* 下载 *React 开发者工具* 作为浏览器拓展程序，并通过浏览器的开发者工具在浏览器的应用程序打开它，然后尝试其各种功能。比如，您可以用它可视化 React 组件树并且更新组件。
> * Does the SearchForm re-render when removing an item from the List with the "Dismiss" button? If it's the case, apply performance optimization techniques to prevent re-rendering.
* 使用“删除”按钮从 List 中移除一项时 SearchForm 会重新渲染吗？如果会的话，请使用上述性能优化的方法防止其重新渲染。
> * Does each Item re-render when removing an item from the List with the "Dismiss" button? If it's the case, apply performance optimization techniques to prevent re-rendering.
* 当从 List 中移除一项时，List 中的其余项会重新渲染吗？如果会的话，请使用上述性能优化的方法防止其重新渲染。
> * Remove all performance optimizations to keep the application simple. Our current application doesn't suffer from any performance bottlenecks. Try to avoid [premature optimzations](https://en.wikipedia.org/wiki/Program_optimization). Use this section as reference, in case you run into performance problems.

* 移除所有的性能优化操作保持应用的简单性，我们现在的应用程序并没有遇到性能瓶颈。尽量避免[过早优化](https://en.wikipedia.org/wiki/Program_optimization)，如果遇到性能问题，请参考本节内容。