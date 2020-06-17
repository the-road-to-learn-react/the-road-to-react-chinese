## 自定义 React Hook（高阶）

> React Custom Hooks (Advanced)

> Thus far we've covered the two most popular hooks in React: useState and useEffect. useState is used to make your application interactive; useEffect is used to opt into the lifecycle of your components.

到这里我们已经讲到了 React 里两个最常用的 hook：useState 和 useEffect。useState 用来使你的应用可交互；useEffect 可以让组件有选择地使用生命周期。

> We'll eventually cover more hooks that come with React -- in this volume and in other resources -- though we won't cover all of them here. Next we'll tackle **React custom Hooks**; that is, building a hook yourself.

最终我们会讲到更多的 React 自带 hook —— 在这里或是其他材料里 —— 但肯定不会覆盖所有的。接下来我们要来对付 **自定义的 React Hook**；也就是自己来打造一个 hook。

> We will use the two hooks we already possess to create a new custom hook called `useSemiPersistentState`, named as such because it manages state yet synchronizes with the local storage. It's not fully persistent because clearing the local storage of the browser deletes relevant data for this application. Start by extracting all relevant implementation details from the App component into this new custom hook:

我们将用到两个已有的 hook 来创建一个新的自定义 hook 叫做 `useSemiPersistentState`，这样命名的原因是它不但管理状态还可以和本地存储做同步。它不是一个完全的持久化，因为清除浏览器的本地存储会将应用相关的数据都删掉。我们可以从把所有相关的实现细节从 App 组件里抽出来，放到这个新的自定义 hook 里开始：

{title="src/App.js",lang="javascript"}

~~~~~~~
# leanpub-start-insert
const useSemiPersistentState = () => {
 const [searchTerm, setSearchTerm] = React.useState(
   localStorage.getItem('search') || ''
 );

 React.useEffect(() => {
   localStorage.setItem('search', searchTerm);
 }, [searchTerm]);
};
# leanpub-end-insert

const App = () => {
...
};
~~~~~~~

> So far, it's just a function around our previously in the App component used `useState` and `useEffect` hooks. Before we can use it, let's return the values that are needed in our App component from this custom hook:

到这里，我们只是把之前在 App 组件里用到 `useState` 和 `useEffect` 的部分用函数把包装起来了而已。在被调用之前，还需要让它把 App 组件需要用到的值返回出来：

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = () => {
 const [searchTerm, setSearchTerm] = React.useState(
   localStorage.getItem('search') || ''
 );

 React.useEffect(() => {
   localStorage.setItem('search', searchTerm);
 }, [searchTerm]);

# leanpub-start-insert
 return [searchTerm, setSearchTerm];
# leanpub-end-insert
};
~~~~~~~

> We are following two conventions of React's built-in hooks here. First, the naming convention which puts the "use" prefix in front of every hook name; second, the returned values are returned as an array. Now we can use the custom hook with its returned values in the App component with the usual array destructuring:

这里我们遵循了 React 原生 hook 的两个惯例。首先是命名规则，每个 hook 都使用 “use” 作为前缀。其次是把返回值放在一个数组里。现在我们可以在 App 组件里使用自定义 hook 及其返回值，和往常一样用数组解构的方式：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 const stories = [ ... ];

# leanpub-start-insert
 const [searchTerm, setSearchTerm] = useSemiPersistentState();
# leanpub-end-insert

 const handleSearch = event => {
   setSearchTerm(event.target.value);
 };

 const searchedStories = stories.filter(story =>
   story.title.toLowerCase().includes(searchTerm.toLowerCase())
 );

 return (
   ...
 );
};
~~~~~~~

> Another goal of a custom hook should be reusability. All of this custom hook's internals are about the search domain, but the hook should be for a value that's set in state and synchronized in local storage. Let's adjust the naming therefore:

自定义 hook 的另外一个目标应该是可复用性。目前这个自定义 hook 的内部看起来还是搜索相关的，但它应该是用来管理状态里的值并将它同步到本地存储的。那么让我们来重新命一下名：

{title="src/App.js",lang="javascript"}

~~~~~~~
const useSemiPersistentState = () => {
# leanpub-start-insert
 const [value, setValue] = React.useState(
   localStorage.getItem('value') || ''
# leanpub-end-insert
 );

 React.useEffect(() => {
# leanpub-start-insert
   localStorage.setItem('value', value);
 }, [value]);
# leanpub-end-insert

# leanpub-start-insert
 return [value, setValue];
# leanpub-end-insert
};
~~~~~~~

> We handle an abstracted "value" within the custom hook. Using it in the App component, we can name the returned current state and state updater function anything domain-related (e.g. `searchTerm` and `setSearchTerm`) with array destructuring.

我们用自定义 hook 处理的是一个抽象的值 “value”。在 App 组件里，我们可以在数组解构的过程中，使用有业务含义的词来命名它所返回的当前状态和状态更新函数，例如 `searchTerm` 和 `setSearchTerm`。

> There is still one problem with this custom hook. Using the custom hook more than once in a React application leads to an overwrite of the "value"-allocated item in the local storage. To fix this, pass in a key:

这个自定义 hook 还有一个问题。如果在同一个 React 应用中多次使用到它的时候，会造成本地存储里的值被覆盖。我们可以通过传一个键值 `key`，来解决这个问题：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const useSemiPersistentState = key => {
# leanpub-end-insert
 const [value, setValue] = React.useState(
# leanpub-start-insert
   localStorage.getItem(key) || ''
# leanpub-end-insert
 );

 React.useEffect(() => {
# leanpub-start-insert
   localStorage.setItem(key, value);
 }, [value, key]);
# leanpub-end-insert

 return [value, setValue];
};

const App = () => {
 ...

 const [searchTerm, setSearchTerm] = useSemiPersistentState(
# leanpub-start-insert
   'search'
# leanpub-end-insert
 );

 ...
};
~~~~~~~

> Since the key comes from outside, the custom hook assumes that it could change, so it needs to be included in the dependency array of the `useEffect` hook. Without it, the side-effect may run with an outdated key (also called *stale*) if the key changed between renders.

因为键值来自外部，自定义 hook 假设它可能会发生变化，所以要把它放到 `useEffect` 的依赖数组里。如果不做这一步，当键值在不同的渲染之间产生了变化的时候，副作用就可能会使用过期的键值。

> Another improvement is to give the custom hook the initial state we had from the outside:

我们还可以进一步改进它，让自定义 hook 从外部接受一个初始状态：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const useSemiPersistentState = (key, initialState) => {
# leanpub-end-insert
 const [value, setValue] = React.useState(
# leanpub-start-insert
   localStorage.getItem(key) || initialState
# leanpub-end-insert
 );

 ...
};

const App = () => {
 ...

 const [searchTerm, setSearchTerm] = useSemiPersistentState(
# leanpub-start-insert
   'search',
   'React'
# leanpub-end-insert
 );

 ...
};
~~~~~~~

> You've just created your first custom hook. If you're not comfortable with custom hooks, you can revert the changes and use the `useState` and `useEffect` hook as before, in the App component.

这样你就创建了你的第一个自定义 hook。如果你还没准备好开始使用自定义 hook，那么你可以还原代码，然后像以前一样在 App 组件里使用 `useState` 和 `useEffect`。

> However, knowing more about custom hooks gives you lots of new options. A custom hook can encapsulate non-trivial implementation details that should be kept away from a component; can be used in more than one React component; and can even be open-sourced as an external library. Using your favorite search engine, you'll notice there are hundreds of React hooks that could be used in your application without worry over implementation details.

然而，了解更多关于自定义 hook 的知识会给你更多的选择。自定义 hook 可以把复杂的实现细节包装起来，从而使组件的逻辑看起来更加清晰；还可以被复用到更多的 React 组件中；甚至可以作为一个三方库开源出去。用喜欢的搜索引擎搜索一下你就会发现，已经有成百上千个 React hook 供你使用，而不需要关心它们是如何实现的。

### 练习：

* > Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Custom-Hooks).
 * > Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Side-Effects...hs/React-Custom-Hooks?expand=1).
* > Read more about [React Hooks](https://www.robinwieruch.de/react-hooks) to get a good understanding of them. They are the bread and butter in React function components, so it's important to really understand them ([0](https://reactjs.org/docs/hooks-overview.html), [1](https://reactjs.org/docs/hooks-custom.html)).


* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Custom-Hooks)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Side-Effects...hs/React-Custom-Hooks?expand=1)。
* 阅读更多关于 [React Hooks](https://www.robinwieruch.de/react-hooks) 的文章来深入理解这个概念。它们和 React 函数组件的关系，就如同豆浆和油条，理解它们对你来说是至关重要的。([0](https://reactjs.org/docs/hooks-overview.html), [1](https://reactjs.org/docs/hooks-custom.html))