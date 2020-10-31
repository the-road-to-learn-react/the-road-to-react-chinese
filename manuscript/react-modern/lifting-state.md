## React 状态提升

目前，Search 组件仍然持有其内部 state。虽然我们创建了一个回调处理函数来将信息向上传递给 App 组件，但我们还没有使用它。我们需要弄清楚如何在多个组件之间共享 Search 组件的 state。

在将 list 作为 props 传递给 List 组件之前，还需要在 App 中使用搜索词来过滤此列表。我们需要将 state 从 Search **提升** 到 App 组件，以便与更多组件共享 state。

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

前面我们已经了解了回调处理函数，因为它帮助我们保持了一个从 Search 组件到 App 组件的开放通信通道。Search 组件不再管理 state，只是在文本输入到输入框之后，将事件向上传递给 App 组件。你也可以将 `searchTerm` 作为 prop 向下传递，并在 App 或 Search 组件中再次展示它。

总是在组件中管理 state，其中每个感兴趣的组件，要么是管理 state 的组件（直接使用来自 state 的信息），要么是管理组件下游的组件（使用 prop 的信息）。如果下游的组件需要更新 state，请向下传递一个回调处理函数给它（参考 Search 组件）。如果组件需要使用 state（例如展示它），则将其作为 props 向下传递。

通过管理 App 组件中的搜索功能 state，在将 `list` 传递给 List 组件之前，我们最终可以用有状态的 `searchTerm` 来过滤列表：

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

现在你应该可以搜索 "eact"，"React" 或者 "react"，并看到两个展示 stories 中的其中一个。你刚刚向应用程序添加了一个交互功能。

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

这就是 filter 函数的内联函数的重构步骤。它有许多变体，在可读性和简洁性之间保持平衡并不总是那么简单。然而，我觉得只要尽可能保持简洁就能够在大部分时间保持可读性。

![](images/component-communication.png)

现在，我们可以在 App 组件中使用 Search 组件的回调函数来更新 state，从而在 React 中操作 state。当前 state 用作列表的过滤器。通过回调处理函数，我们使用了来自 App 组件中的 Search 组件的信息来更新共享 state，并间接地在 List 组件中获取了过滤后的列表。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Lifting-State-in-React)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Callback-Handler-in-JSX...hs/Lifting-State-in-React?expand=1)。
