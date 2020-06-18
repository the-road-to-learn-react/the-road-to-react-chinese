## React 异步数据

在这个应用中我们有两种交互方式：搜索列表以及从列表中删除项目。第一个交互是通过应用在列表中的第三方 state（`searchTerm`）产生的影响；第二种交互是不可撤销的从列表中删除项目。

有的时候我们必须先渲染出一个组件，然后再请求第三方 API 得到数据并将其显示在组件上。接下来我们将在应用中模拟这种异步数据过程。在真实应用里，异步数据是从真正的远端 API 中获取的。我们从一个简单返回 promise 的函数出发，一直到数据被解析完成结束。被解析的对象数据包括了之前的故事列表数据：

{title="src/App.js",lang="javascript"}
~~~~~~~
const initialStories = [ ... ];

# leanpub-start-insert
const getAsyncStories = () =>
  Promise.resolve({ data: { stories: initialStories } });
# leanpub-end-insert
~~~~~~~

在 APP 组件中，使用一个空数组作为初始的 state，而不是使用 `initialStories`。我们希望从一个空的故事列表开始，并模拟异步获取这些故事的数据的过程。在新的 `useEffect` hook 中，调用方法并返回被解析的 promise。由于 useEffect 的依赖数组是个空数组，因此管理副作用的函数仅在组件首次渲染后运行：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [stories, setStories] = React.useState([]);
# leanpub-end-insert

# leanpub-start-insert
  React.useEffect(() => {
    getAsyncStories().then(result => {
      setStories(result.data.stories);
    });
  }, []);
# leanpub-end-insert

  ...
};
~~~~~~~

虽然我们启动应用的时候数据并非同时到达，但由于它是立刻渲染的，使数据看起来似乎是同步到达的。因为每个对 API 的网络请求都会带来延迟，所以现在让我们给它加上一个真实的延迟。首先，删除之前的简写版本：

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
# leanpub-start-insert
  new Promise(resolve =>
    resolve({ data: { stories: initialStories } })
  );
# leanpub-end-insert
~~~~~~~

其次在解析 promise 时，将其延迟几秒钟：

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
  new Promise(resolve =>
# leanpub-start-insert
    setTimeout(
      () => resolve({ data: { stories: initialStories } }),
      2000
    )
# leanpub-end-insert
  );
~~~~~~~

再次启动应用之后，你应该能看到列表数据会延迟几秒后再渲染出来。而由于初始的故事数据是个空数组，在 APP 组件渲染之后副作用 hook 会运行一次以获取异步数据。在解析 promise 以及将数据设置为组件的 state 之后，组件会再次渲染并显示异步加载的故事列表。

### 练习：

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Asynchronous-Data)。
  * 检查[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Inline-Handler-in-JSX...hs/React-Asynchronous-Data?expand=1)。
* 阅读更多关于[JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。
