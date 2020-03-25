## 自定义 React Hook（高阶）

> React Custom Hooks (Advanced)

> Thus far we've covered the two most popular hooks in React: useState and useEffect. useState is used to make your application interactive; useEffect is used to opt into the lifecycle of your components.

到这里我们已经覆盖了 React 里两个最常用的 hook：useState 和 useEffect。useState 用来使你的应用和用户产生交互；useEffect 可以让你的组件选择使用生命周期。

> We'll eventually cover more hooks that come with React -- in this volume and in other resources -- though we won't cover all of them here. Next we'll tackle **React custom Hooks**; that is, building a hook yourself.

// TODO

最终我们会提到 React 带来的更多 hook —— 在这里或是其他材料里 —— 但这里不会覆盖所有的。接下来我们要来对付 **自定义 React Hook**；也就是自己打造一个 hook。

> We will use the two hooks we already possess to create a new custom hook called `useSemiPersistentState`, named as such because it manages state yet synchronizes with the local storage. It's not fully persistent because clearing the local storage of the browser deletes relevant data for this application. Start by extracting all relevant implementation details from the App component into this new custom hook:

// TODO

我们将用到两个已有的 hook 来创建一个新的自定义 hook 叫做 `useSemiPersistentState`，这样命名的原因是它不但管理状态还可以和本地存储做同步。它不是一个完全的持久化，因为清除浏览器的本地存储会把这个应用相关的数据都清理掉。我们可以从把所有相关的实现细节从 App 组件里抽出来，放到这个新的自定义 hook 里开始：

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

// TODO

到这里，我们只是用函数把之前在 App 组件里用到 `useState` 和 `useEffect` 的部分包起来了而已。在被调用之前，我们还需要让它把 App 组件需要用到的值返回出来：

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

// TODO

这里我们遵循了 React 原生 hook 的两个规则。首先是命名规则，每个 hook 都使用 “use” 作为前缀。其次把返回值放在一个数组里。现在我们可以在 App 组件里使用自定义 hook 及其返回值，和往常一样用数组解构的方式：

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

// TODO

自定义 hook 的另外一个目标应该是可复用性。这个自定义 hook 的内部看起来还是搜索相关的，但这个 hook 本身应该是用来管理状态里的值并将它同步到本地存储的。那么让我们来重新命一下名：

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

// TODO

在自定义 hook 里我们处理一个抽象的值 “value”。在 App 组件里，我们可以在数组解构的过程中，使用有业务含义的词来命名它所返回的当前状态和状态更新函数，例如 `searchTerm` 和 `setSearchTerm`。

> There is still one problem with this custom hook. Using the custom hook more than once in a React application leads to an overwrite of the "value"-allocated item in the local storage. To fix this, pass in a key:

// TODO

这个自定义 hook 还有一个问题。如果在同一个 React 应用中多次使用到它的时候，会造成本地存储里的值被复写。可以通过传一个键值 `key`，来解决这个问题：

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

// TODO

> Another improvement is to give the custom hook the initial state we had from the outside:

// TODO

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

// TODO

> However, knowing more about custom hooks gives you lots of new options. A custom hook can encapsulate non-trivial implementation details that should be kept away from a component; can be used in more than one React component; and can even be open-sourced as an external library. Using your favorite search engine, you'll notice there are hundreds of React hooks that could be used in your application without worry over implementation details.

// TODO

### 练习：

* > Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Custom-Hooks).
 * > Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Side-Effects...hs/React-Custom-Hooks?expand=1).
* > Read more about [React Hooks](https://www.robinwieruch.de/react-hooks) to get a good understanding of them. They are the bread and butter in React function components, so it's important to really understand them ([0](https://reactjs.org/docs/hooks-overview.html), [1](https://reactjs.org/docs/hooks-custom.html)).



* // TODO： 等待统一翻译
* // TODO： 等待统一翻译
* 阅读更多关于 [React Hooks](https://www.robinwieruch.de/react-hooks) 的文章来深入理解这个概念。它们和 React 函数组件的关系，就如同豆浆和油条，理解它们对你来说是至关重要的。([0](https://reactjs.org/docs/hooks-overview.html), [1](https://reactjs.org/docs/hooks-custom.html))