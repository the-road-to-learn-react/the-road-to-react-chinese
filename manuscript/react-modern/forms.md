> ## Forms in React

## React 表格

> Earlier we introduced a new button to fetch data explicitly with a button click. We'll advance its use with a proper HTML form, which encapsulates the button and input field for the search term with its label.

前一节中我们加入了一个显式获取数据的按钮。接下来，我们将使用 HTML 表单来完善它，将搜索输入框、按钮都封装起来。

> Forms aren't much different in React's JSX than in HTML. We'll implement it in two refactoring steps with some HTML/JavaScript. First, wrap the input field and button into an HTML form element:

表单在 JSX（React）和 HTML 中没有太大差别。我们将分几步进行重构，首先把输入框和按钮都用 `form` 元素包裹起来：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <form onSubmit={handleSearchSubmit}>
# leanpub-end-insert
        <InputWithLabel
          id="search"
          value={searchTerm}
          isFocused
          onInputChange={handleSearchInput}
        >
          <strong>Search:</strong>
        </InputWithLabel>

# leanpub-start-insert
        <button type="submit" disabled={!searchTerm}>
# leanpub-end-insert
          Submit
        </button>
# leanpub-start-insert
      </form>
# leanpub-end-insert

      <hr />

      ...
    </div>
  );
};
~~~~~~~

> Instead of passing the `handleSearchSubmit` handler to the button, it's used in the new form element. The button receives a new `type` attribute called `submit`, which indicates that the form element handles the click and not the button.

我们不再把 `handleSearchSubmit` 监听函数绑定在提交按钮上，而是绑在新的 `form` 元素上。另外把提交按钮的 `type` 属性设置为 `submit`，这样 `form` 元素就能处理提交事件。

> Since the handler is used for the form event, it executes `preventDefault` in React's synthetic event. This prevents the HTML form's native behavior, which leads to a browser reload.

监听函数用来处理表单事件，我们需要在其中执行 `event.preventDefault()`，来避免浏览器默认行为（这会导致页面重新加载）。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleSearchSubmit = event => {
# leanpub-end-insert
    setUrl(`${API_ENDPOINT}${searchTerm}`);

# leanpub-start-insert
    event.preventDefault();
# leanpub-end-insert
  };

  ...
};
~~~~~~~

> Now we can execute the search feature with the keyboard's `Enter` key. In the next two steps, we will only separate the component into its standalone SearchForm component:

现在，我们可以使用键盘的 `Enter` 键来执行搜索功能。 接下来，我们将该组件抽取为独立的 SearchForm 组件：


{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const SearchForm = ({
  searchTerm,
  onSearchInput,
  onSearchSubmit,
}) => (
  <form onSubmit={onSearchSubmit}>
    <InputWithLabel
      id="search"
      value={searchTerm}
      isFocused
      onInputChange={onSearchInput}
    >
      <strong>Search:</strong>
    </InputWithLabel>

    <button type="submit" disabled={!searchTerm}>
      Submit
    </button>
  </form>
);
# leanpub-end-insert
~~~~~~~

> The new component is used by the App component. The App component still manages the state for the form, because the state is used in the App component to fetch data passed as props (`stories.data`) to the List component:

把这个新组件引入到 App 组件中。App 组件仍然替表单组件管理状态，因为 App 组件的状态会通过 props(`stories.data`) 传递给 List 组件使用：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <SearchForm
        searchTerm={searchTerm}
        onSearchInput={handleSearchInput}
        onSearchSubmit={handleSearchSubmit}
      />
# leanpub-end-insert

      <hr />

      {stories.isError && <p>Something went wrong ...</p>}

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
      )}
    </div>
  );
};
~~~~~~~

> Forms aren't much different in React than HTML. When we have input fields and a button to submit data from them, we can give our HTML more structure by wrapping it into a form element with a `onSubmit` handler. The button that executes the submission needs only the "submit" `type`.

表单在 React 和 HTML 中没有太大差别。当我们通过按钮去提交输入框的数据时，我们可以把这些元素包裹进含有 `onSubmit` 监听器的 `form` 元素中，而提交按钮仅需要设置 `type="submit"`。

### 练习:

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Forms-in-React).
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Async-Await-in-React...hs/Forms-in-React?expand=1).
* 试试不使用 `preventDefault`  的效果.
* 阅读更多关于 [preventDefault for Events in React](https://www.robinwieruch.de/react-preventdefault).
* 阅读更多关于 [React Component Composition](https://www.robinwieruch.de/react-component-composition).