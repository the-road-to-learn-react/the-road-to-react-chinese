> ## Reverse Sort

## 逆序排序

> **Task:** The sort feature works, but the ordering only includes one direction. Implement a reverse sort when the button is clicked twice, so it becomes a toggle between normal (ascending) and reverse (descending) sort.

> **任务：** 排序功能可以使用，但是只有一个方向的顺序。实现在点击两次按钮时逆序排序，所以它就变成了在正常（升序）和反向（降序）排序之间切换。

> **Optional Hints:**

**可选的提示:**

> * Consider that reverse or normal sort could be just another state (e.g. `isReverse`) next to the `sortKey`.

* 考虑逆序或者升序排序可能只是 `sortKey` 的另一个 state （例如 `isReverse`）。

> * Set the new state in the `handleSort` handler based on the previous sort.

* 在 `handleSort` 处理函数中根据之前的排序设置新的 state。

> * Use the new `isReverse` state for sorting the list with the sort function from the dictionary with the optionally applied `reverse()` function from JavaScript arrays.

* 使用新的 `isReverse` 状态，并且使用 JavaScript 数组的 `reverse()` 函数根据字典中的顺序对列表进行排序。

> The initial sort direction works for strings, as well as numeric sorts like the reverse sort for JavaScript numbers that arranges them from high to low. Now we need another state to track whether the sort is reversed or normal, to make it more complex:

最初的排序方式对字符串和数组都有效，就像 JavaScript 数字的逆序排序一样，它可以将数字从高到低排列。现在我们需要另一种 state 来记录排序的方式，使其更加复杂：

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }) => {
# leanpub-start-insert
  const [sort, setSort] = React.useState({
    sortKey: 'NONE',
    isReverse: false,
  });
# leanpub-end-insert

  ...
};
~~~~~~~

> Next, give the sort handler logic to see if the incoming `sortKey` triggers are a normal or reverse sort. If the `sortKey` is the same as the one in the state, it could be a reverse sort, but only if the sort state wasn't already reversed:

接下来，给排序处理函数添加一些逻辑来判断传入的 `sortKey` 触发器是正常排序还是逆序排序。如果 `sortKey` 与 state 中的相同，则可能是逆序排序，但前提是排序的状态还没有反转：

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }) => {
  const [sort, setSort] = React.useState({
    sortKey: 'NONE',
    isReverse: false,
  });

  const handleSort = sortKey => {
# leanpub-start-insert
    const isReverse = sort.sortKey === sortKey && !sort.isReverse;

    setSort({ sortKey: sortKey, isReverse: isReverse });
# leanpub-end-insert
  };

# leanpub-start-insert
  const sortFunction = SORTS[sort.sortKey];
# leanpub-end-insert
  const sortedList = sortFunction(list);

  return (
    ...
  );
};
~~~~~~~

> Lastly, depending on the new `isReverse` state, apply the sort function from the dictionary with or without the built-in JavaScript reverse method for arrays:

最后，根据新的 `isReverse` state，使用字典中的排序函数，如果没有内置的 JavaScript reverse 数组方法：

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }) => {
  const [sort, setSort] = React.useState({
    sortKey: 'NONE',
    isReverse: false,
  });

  const handleSort = sortKey => {
    const isReverse = sort.sortKey === sortKey && !sort.isReverse;

# leanpub-start-insert
    setSort({ sortKey, isReverse });
# leanpub-end-insert
  };

  const sortFunction = SORTS[sort.sortKey];

# leanpub-start-insert
  const sortedList = sort.isReverse
    ? sortFunction(list).reverse()
    : sortFunction(list);
# leanpub-end-insert

  return (
    ...
  );
};
~~~~~~~

> The reverse sort is now operational. For the object passed to the state updater function, we use what is called a **shorthand object initializer notation**:

现在逆序排序可以工作了，对于传给 state 更新函数的对象，我们使用了所谓的 **shorthand 对象初始化符号**：

{title="src/App.js",lang="javascript"}
~~~~~~~
const firstName = 'Robin';

const user = {
  firstName: firstName,
};

console.log(user);
// { firstName: "Robin" }
~~~~~~~

> When the property name in your object is the same as your variable name, you can omit the key/value pair and just write the name:

当对象中的属性名与变量名相同时，你可以省略键值对，只需要写上名字即可：

{title="src/App.js",lang="javascript"}
~~~~~~~
const firstName = 'Robin';

const user = {
  firstName,
};

console.log(user);
// { firstName: "Robin" }
~~~~~~~

> If necessary, read more about [JavaScript Object Initializers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer).

如有必要，阅读更多关于 [JavaScript 对象初始化](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)。

### 练习：

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Reverse-Sort).
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Sort...hs/Reverse-Sort?expand=1).
> * Consider the drawback of keeping the sort state in the List instead of the App component. If you don't know, sort the list by "Title" and search for other stories afterward. What would be different if the sort state would be in the App component.
* 考虑到这个缺点，将排序状态保留在 List 中，而不是 App 组件中。如果你不知道的话，那就根据 Title 进行排序之后，再搜索其他的内容。如果排序的状态在 App 组件中那会有什么不同呢？
> * Use your styling skills to give the user feedback about the current active sort and its reverse state. It could be an [arrow up or arrow down SVG](https://www.flaticon.com/packs/arrow-set-2) next to each active sort button.
* 使用你的风格设计技巧，给用户反映出当前活动排序及其逆序排序状态。它可以是每个活动排序按钮旁边的[向上或向下的箭头](https://www.flaticon.com/packs/arrow-set-2))。
