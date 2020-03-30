> ## Lifting State in React
## 在 React 中提升状态

> Currently, the Search component still has its internal state. While we established a callback handler to pass information up to the App component, we are not using it yet. We need to figure out how to share the Search component's state across multiple components.

目前，Search 组件仍然持有其内部状态。虽然我们创建了一个回调处理函数来将信息向上传递给  App 组件，但我们还没有使用它。我们需要弄清楚如何在多个组件之间共享 Search 组件的状态。

> The search term is needed in the App to filter the list before passing it to the List component as props. We'll need to **lift state up** from Search to App component to share the state with more components.

在将 list 作为 props 传递给 List 组件之前，还需要在 App 中使用搜索词来过滤列表。我们需要将状态从 Search __提升__到 App 组件，以便与更多组件共享状态。


{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 const stories = [ ... ];

# leanpub-start-insert
 const [searchTerm, setSearchTerm] = React.useState('');
# leanpub-end-insert

 const handleSearch = event => {
# leanpub-start-insert
   setSearchTerm(event.target.value);
# leanpub-end-insert
 };

 return (
   <div>
     <h1>My Hacker Stories</h1>

     <Search onSearch={handleSearch} />

     <hr />

     <List list={stories} />
   </div>
 );
};

const Search = props => (
 <div>
   <label htmlFor="search">Search: </label>
# leanpub-start-insert
   <input id="search" type="text" onChange={props.onSearch} />
# leanpub-end-insert
 </div>
);
~~~~~~~

> We learned about the callback handler previously, because it helps us to keep an open communication channel from Search to App component. The Search component doesn't manage the state anymore, but only passes up the event to the App component after text is entered into the input field. You could also display the `searchTerm` again in the App component or Search component by passing it down as prop.

前面我们已经了解了回调处理函数，因为它帮助我们保持了一个从 Search 组件到 App 组件的开放通信通道。Search 组件不再管理状态，只是在文本输入到输入框之后，将事件向上传递给 App 组件。你也可以将 `searchTerm` 作为 prop 向下传递，并在 App 或 Search 组件中再次展示它。

> Always manage the state at a component where every component that's interested in it is one that either manages the state (using information directly from state) or a component below the managing component (using information from props). If a component below needs to update the state, pass a callback handler down to it (see Search component). If a component needs to use the state (e.g. displaying it), pass it down as props.

总是在组件中管理状态，其中每个感兴趣的组件，要么是管理状态的组件（直接使用来自状态的信息），要么是管理组件下游的组件（使用 prop 的信息）。如果下游的组件需要更新状态，请向下传递一个回调处理函数给它（参考 Search 组件）。如果组件需要使用状态（例如展示它），则将其作为 props 向下传递。

> By managing the search feature state in the App component, we can finally filter the list with the stateful `searchTerm` before passing the `list` to the List component:

通过管理 App 组件中的搜索功能状态，在将 `list` 传递给 List 组件之前，我们最终可以用有状态的 `searchTerm` 来过滤列表：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 const stories = [ ... ];

 const [searchTerm, setSearchTerm] = React.useState('');

 const handleSearch = event => {
   setSearchTerm(event.target.value);
 };

# leanpub-start-insert
 const searchedStories = stories.filter(function(story) {
   return story.title.includes(searchTerm);
 });
# leanpub-end-insert

 return (
   <div>
     <h1>My Hacker Stories</h1>

     <Search onSearch={handleSearch} />

     <hr />

# leanpub-start-insert
     <List list={searchedStories} />
# leanpub-end-insert
   </div>
 );
};
~~~~~~~

> Here, the [JavaScript array's built-in filter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) is used to create a new filtered array. The filter function takes a function as an argument, which accesses each item in the array and returns true or false. If the function returns true, meaning the condition is met, the item stays in the newly created array; if the function returns false, it's removed:

在这里， [JavaScript 数组的内置过滤函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 可以用来创建一个新的过滤数组。Filter 函数接受一个函数作为参数，该函数访问数组中的每一项并返回 true 或 false。如果函数返回 true，则表示满足条件，该项将被保留在新创建的数组中；如果函数返回 false，则将该项删除。

{title="Code Playground",lang="javascript"}
~~~~~~~
const words = [
 'spray',
 'limit',
 'elite',
 'exuberant',
 'destruction',
 'present'
];

const filteredWords = words.filter(function(word) {
 return word.length > 6;
});

console.log(filteredWords);
// ["exuberant", "destruction", "present"]
~~~~~~~

> The filter function checks whether the `searchTerm` is present in our story item's title, but it's still too opinionated about the letter case. If we search for "react", there is no filtered "React" story in your rendered list. To fix this problem, we have to lower case the story's title and the `searchTerm`.

Filter 函数会检查 `searchTerm` 是否出现在我们 story 的标题中，但是它对字母的大小写仍然过于敏感。如果我们搜索 "react"，你的渲染列表中不会有过滤出来的 "React" story。为了解决这个问题，我们必须将 story 的标题和 `searchTerm` 改为小写。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 ...

 const searchedStories = stories.filter(function(story) {
# leanpub-start-insert
   return story.title
     .toLowerCase()
     .includes(searchTerm.toLowerCase());
# leanpub-end-insert
 });

 ...
};
~~~~~~~

> Now you should be able to search for "eact", "React", or "react" and see one of two displayed stories. You have just added an interactive feature to your application.

现在你应该可以搜索 "eact"，"React" 或者 "react"，并看到两个展示 stories 中的其中一个。你刚刚向应用程序添加了一个交互功能。

> The remaining section shows a couple of refactoring steps. We will be using the final refactored version in the end, so it makes sense to understand these steps and keep them. As learned before, we can make the function more concise using a JavaScript arrow function:

剩下的部分展示了几个重构步骤。最后，我们将使用最终的重构版本，因此理解并保留这些步骤是有意义的。正如前面学到的那样，我们可以使用 JavaScript 箭头函数让该函数更加整洁：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 ...

# leanpub-start-insert
 const searchedStories = stories.filter(story => {
# leanpub-end-insert
   return story.title
     .toLowerCase()
     .includes(searchTerm.toLowerCase());
 });

 ...
};
~~~~~~~

> In addition, we could turn the return statement into an immediate return, because no other task (business logic) happens before the return:

此外，我们可以将 return 语句转换为立即返回，因为在返回之前没有其他任务（业务逻辑）发生。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 ...

# leanpub-start-insert
 const searchedStories = stories.filter(story =>
   story.title.toLowerCase().includes(searchTerm.toLowerCase())
 );
# leanpub-end-insert

 ...
};
~~~~~~~

> That's all to the refactoring steps of the inlined function for the filter function. There are many variations to it -- and it's not always simple to keep a good balance between readable and conciseness -- however, I feel like keeping it concise whenever possible keeps it most of the time readable as well.

这就是 filter 函数的内联函数的重构步骤。它有许多变体，在可读性和简洁性之间保持平衡并不总是那么简单。然而，我觉得只要尽可能保持简洁就能够在大部分时间保持可读性。

> Now we can manipulate state in React, using the Search component's callback handler in the App component to update it. The current state is used as a filter for the list. With the callback handler, we used information from the Search component in the App component to update the shared state and indirectly in the List component for the filtered list.

现在，我们可以在 App 组件中使用 Search 组件的回调函数来更新状态，从而在 React 中操作状态。当前状态用作列表的过滤器。通过回调处理函数，我们使用了来自 App 组件中的 Search 组件的信息来更新共享状态，并间接地在 List 组件中获取了过滤后的列表。

> ### Exercises:
> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Lifting-State-in-React).
> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Callback-Handler-in-JSX...hs/Lifting-State-in-React?expand=1).

练习：

* 检查[上一节的源码](remember to update link)。
* 确认[上一节之后的变更](remember to update link)。
