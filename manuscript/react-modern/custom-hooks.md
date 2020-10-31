## 自定义 React Hook（高级）

到这里我们已经讲到了 React 里两个最常用的 hook：useState 和 useEffect。useState 用来使你的应用可交互；useEffect 可以让组件有选择地使用生命周期。

最终我们会讲到更多React 自带的 hook （在这里或是其他材料里），但肯定不会涵盖所有的。接下来我们要来对付 **自定义的 React Hook**；也就是自己来打造一个 hook。

我们将用到两个已有的 hook 来创建一个新的自定义 hook 叫做 `useSemiPersistentState`，这样命名的原因是它不但管理 state 还可以和本地存储同步。它不是一个完全的持久化，因为清除浏览器的本地存储会将应用相关的数据都删掉。我们可以从把所有相关的实现细节从 App 组件里抽出来，放到这个新的自定义 hook 里开始：

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

自定义 hook 的另外一个目标应该是可复用性。目前这个自定义 hook 的内部看起来还是搜索相关的，但它应该是用来管理 state 里的值并将它同步到本地存储的。那么让我们来重新命一下名：

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

我们用自定义 hook 处理的是一个抽象的值 “value”。在 App 组件里，我们可以在数组解构的过程中，使用有业务含义的词来命名它所返回的当前 state 和 state 更新函数，例如 `searchTerm` 和 `setSearchTerm`。

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

因为键值来自外部，自定义 hook 假设它可能会发生变化，所以要把它放到 `useEffect` 的依赖数组里。如果不做这一步，当键值在不同的渲染之间产生了变化的时候，副作用就可能会使用过期的键值。

我们还可以进一步改进它，让自定义 hook 从外部接受一个初始 state：

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

这样你就创建了你的第一个自定义 hook。如果你还没准备好开始使用自定义 hook，那么你可以还原代码，然后像以前一样在 App 组件里使用 `useState` 和 `useEffect`。

然而，了解更多关于自定义 hook 的知识会给你更多的选择。自定义 hook 可以把复杂的实现细节包装起来，从而使组件的逻辑看起来更加清晰；还可以被复用到更多的 React 组件中；甚至可以作为一个三方库开源出去。用喜欢的搜索引擎搜索一下你就会发现，已经有成百上千个 React hook 供你使用，而不需要关心它们是如何实现的。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Custom-Hooks)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Side-Effects...hs/React-Custom-Hooks?expand=1)。
* 阅读更多关于 [React Hooks](https://www.robinwieruch.de/react-hooks) 的文章来深入理解这个概念。它们和 React 函数组件的关系，就如同豆浆和油条，理解它们对你来说是至关重要的。([0](https://reactjs.org/docs/hooks-overview.html), [1](https://reactjs.org/docs/hooks-custom.html))
