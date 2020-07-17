> ## React Asynchronous Data
## React 异步数据

> We have two interactions in our application: searching the list, and removing items from the list. The first interaction is a fluctuant interference through a third-party state (`searchTerm`) applied on the list; the second interaction is a non-reversible deletion of an item from the list.

在这个应用中我们有两种交互方式：搜索列表以及从列表中删除项目。第一个交互是通过应用在列表中的第三方 state（`searchTerm`）产生的影响；第二种交互是不可撤销的从列表中删除项目。

> Sometimes we must render a component before we can fetch data from a third-party API and display it. In the following, we will simulate this kind of asynchronous data in the application. In a live application, this asynchronous data gets fetched from a real remote API. We start off with a function that returns a promise -- data, once it resolves -- in its shorthand version. The resolved object holds the previous list of stories:

有的时候我们必须先渲染出一个组件，然后再请求第三方 API 得到数据并将其显示在组件上。接下来我们将在应用中模拟这种异步数据过程。在真实应用里，异步数据是从真正的远端 API 中获取的。我们从一个简单返回 promise 的函数出发，一直到数据被解析完成结束。被解析的对象数据包括了之前的 stories 列表数据：

{title="src/App.js",lang="javascript"}
~~~~~~~
const initialStories = [ ... ];

# leanpub-start-insert
const getAsyncStories = () =>
  Promise.resolve({ data: { stories: initialStories } });
# leanpub-end-insert
~~~~~~~

> In the App component, instead of using the `initialStories`, use an empty array for the initial state. We want to start off with an empty list of stories, and simulate fetching these stories asynchronously. In a new `useEffect` hook, call the function and resolve the returned promise. Due to the empty dependency array, the side-effect only runs once the component renders for the first time:

在 APP 组件中，使用一个空数组作为初始的 state，而不是使用 `initialStories`。我们希望从一个空的 stories 列表开始，并模拟异步获取这些 stories 的数据的过程。在新的 `useEffect` hook 中，调用方法并返回被解析的 promise。由于 useEffect 的依赖数组是个空数组，因此管理副作用的函数仅在组件首次渲染后运行：

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

> Even though the data should arrive asynchronously when we start the application, it appears to arrive synchronously, because it's rendered immediately. Let's change this by giving it a bit of a realistic delay, because every network request to an API would come with a delay. First, remove the shorthand version for the promise:

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

> And second, when resolving the promise, delay it for a few seconds:

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

> Once you start the application again, you should see a delayed rendering of the list. The initial state for the stories is an empty array. After the App component rendered, the side-effect hook runs once to fetch the asynchronous data. After resolving the promise and setting the data in the component's state, the component renders again and displays the list of asynchronously loaded stories.

再次启动应用之后，你应该能看到列表数据会延迟几秒后再渲染出来。而由于初始的 stories 是个空数组，在 APP 组件渲染之后副作用 hook 会运行一次以获取异步数据。在解析 promise 以及将数据设置为组件的 state 之后，组件会再次渲染并显示异步加载的 stories 列表。

> ### Exercises:
>
> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Asynchronous-Data).
>   * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Inline-Handler-in-JSX...hs/React-Asynchronous-Data?expand=1).
> * Read more about [JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).

### 练习：

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Asynchronous-Data)。
  * 检查[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Inline-Handler-in-JSX...hs/React-Asynchronous-Data?expand=1)。
* 阅读更多关于[JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。
