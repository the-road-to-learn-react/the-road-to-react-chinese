> # Real World React (Advanced)

# 真实React世界（高级）

> We've covered most of React's fundamentals, its legacy features, and techniques for maintaining applications. Now it's time to dive into developing real-world React applications. Each of the following sections will come with a task. Try to tackle these tasks without the *optional hints* first, but be aware that these are going to be challenging on your first attempt. If you need help, use the *optional hints* or follow the instructions from the section.

我们已经介绍了React大部分基础知识，它的遗留特性以及维护应用程序的技术。现在是时候开发真实世界的React应用程序。下面跟着的每一节将会有一个任务。尝试首先在没有*选项提示*的情况下去解决这些问题，但是要注意，这些任务在你第一次尝试时，是具有挑战性的。如果你需要帮助，使用*选项提示*或者按照本节的说明操作。

> ## Sorting

## 排序

> **Task:** Working with a list of items often includes interactions that make data more approachable by users. So far, every item was listed with each of its properties. To make it explorable, the list should enable sorting of each property by title, author, comments, and points in ascending or descending order. Sorting in only one direction is fine, because sorting in the other direction will be part of the next section.

**任务:**在处理items列表时，经常包括互动，让数据更加能被用户接受。到目前为止，每一个item的属性都被列出来了。为了让它更加具有探索性，这个列表应该根据title， author， comments，和points进行升序或者降序，对每个属性进行排序。按照一个方向排序就好，因为另一个方向的排序在下一章的内容。

> **Optional Hints:**

**可选择的提示:**

> * Introduce a new sort state in the App or List component.

  * 在App组件或者List组件中，引入一种新的排序状态。

> * For each property (e.g. `title`, `author`, `points`, `num_comments`) implement an HTML button which sets the sort state for this property.

  * 对于每个属性（例如：`title`, `author`, `points`, `num_comments`，）实现了一个HTML按钮，用来设置这个属性的排序状态。

> * Use the sort state to apply an appropriate sort function on the `list`.

  * 使用排序状态对`list`应用适当的排序函数

> * Using a utility library like [Lodash](https://lodash.com/) for its `sortBy` function is encouraged.

  * 使用工具库比如[Lodash](https://lodash.com/)，它的`sortBy`函数是被鼓励的。

> We will treat the list of data like a table. Each row represents an item of the list and each column represents one property of the item. Headers provide the user more guidance about each column:

我们将把数据列表当作一个表来看。每一行代表列表里的一个Item，每一个列代表Item的一个属性。针对每一列，标题提供了用户更多的指导：

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = ({ list, onRemoveItem }) => (
  <div>
    <div style={{ display: 'flex' }}>
      <span style={{ width: '40%' }}>Title</span>
      <span style={{ width: '30%' }}>Author</span>
      <span style={{ width: '10%' }}>Comments</span>
      <span style={{ width: '10%' }}>Points</span>
      <span style={{ width: '10%' }}>Actions</span>
    </div>

    {list.map(item => (
# leanpub-end-insert
      <Item
        key={item.objectID}
        item={item}
        onRemoveItem={onRemoveItem}
      />
# leanpub-start-insert
    ))}
  </div>
);
# leanpub-end-insert
~~~~~~~

> We are using inline style for the most basic layout. To match the layout of the header with the rows, give the rows in the Item component a layout as well:

我们使用的是最基本的内联样式布局。为了让标题布局匹配行，也在给Item组件中的每一行布局：

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <div style={{ display: 'flex' }}>
    <span style={{ width: '40%' }}>
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
    </span>
# leanpub-start-insert
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
# leanpub-end-insert
      <button type="button" onClick={() => onRemoveItem(item)}>
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

> In the ongoing implementation, we will remove the style attributes, because it takes up lots of space and clutters the actual implementation logic (hence extracting it into proper CSS). But I encourage you to keep it for yourself.

在当前的实现中，我们将移除样式属性，因为它占用了很多空间并且干扰了实际的实现逻辑（因此提取它到适当的CSS）。但是我鼓励你留着它。

> The List component will handle the new sort state. This can also be done in the App component, but only the List component is in play, so we can lift the state management directly to it. The sort state initializes with a `'NONE'` state, so the list items are displayed in the order they are fetched from the API. Further, we added a new handler to set the sort state with a sort-specific key.

List组件将处理这个新的排序状态。App组件也可以这么做，但是仅在List里面，所以我们将状态管理提升到它的状态管理。排序状态初始值是 `'NONE'`，因此列表中的items会按照请求API获取的数据的顺序显示。此外，我们添加了一个新的处理程序来设置排序状态，根据一个特定的键。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = ({ list, onRemoveItem }) => {
  const [sort, setSort] = React.useState('NONE');

  const handleSort = sortKey => {
    setSort(sortKey);
  };

  return (
# leanpub-end-insert
    ...
# leanpub-start-insert
  );
};
# leanpub-end-insert
~~~~~~~

> In the List component's header, buttons can help us to set the sort state for each column/property. An inline handler is used to sneak in the sort-specific key (`sortKey`). When the button for the "Title" column is clicked, `'TITLE'` becomes the new sort state.

在List组件的头部，按钮可以帮助我们设置每个列/属性的排序状态。一个内联处理程序被使用来针对特定的键（`sortKey`）。当点击“Title”栏按钮时，`'TITLE'`会变成新的排序状态。

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }) => {
  ...

  return (
    <div>
      <div>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('TITLE')}>
            Title
          </button>
# leanpub-end-insert
        </span>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('AUTHOR')}>
            Author
          </button>
# leanpub-end-insert
        </span>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('COMMENT')}>
            Comments
          </button>
# leanpub-end-insert
        </span>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('POINT')}>
            Points
          </button>
# leanpub-end-insert
        </span>
        <span>Actions</span>
      </div>

      {list.map(item => ... )}
    </div>
  );
};
~~~~~~~

> State management for the new feature is implemented, but we don't see anything when our buttons are clicked yet. This happens because the sorting mechanism hasn't been applied to the actual `list`.

新功能的状态管理已经被实现，但是我们不会看到任何东西当我们按钮被点击的时候。这是因为排序机制没有被应用到新的`list`。

 > Sorting an array with JavaScript isn't trivial, because every JavaScript primitive (e.g. string, boolean, number) comes with edge cases when an array is sorted by its properties. We will use a library called [Lodash](https://lodash.com/) to solve this, which comes with many JavaScript utility functions (e.g. `sortBy`). First, install it via the command line:

 在JavaScript中，对数组进行排序是不是一个简单的事情，因为每一个JavaScript原始类型（比如：string，boolean，number）都有边缘问题，当数组按照它的属性进行排序的时候。我们将使用第三方库叫[Lodash](https://lodash.com/)来解决这个问题，它自带了很多JavaScript工具函数（比如：`sortBy`）。首先，安装它基于命令行：

{title="Command Line",lang="text"}
~~~~~~~
npm install lodash
~~~~~~~

> Second, at the top of your file, import the utility function for sorting:

第二，在你文件的顶部，引入工具函数排序：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';
# leanpub-start-insert
import { sortBy } from 'lodash';
# leanpub-end-insert

...
~~~~~~~

> Third, create a JavaScript object (also called dictionary) with all the possible `sortKey` and sort function mappings. Each specific sort key is mapped to a function that sorts the incoming `list`. Sorting by `'NONE'` returns the unsorted list; sorting by `'POINT'` returns a list and its items sorted by the `points` property.

第三，创造一个JavaScript对象（也称为字典），其中包含所有可能的`sortKey`和排序方法的映射。每一个被指定的排序键会被映射到一个函数，该函数对传入的`列表`进行排序。按`'NONE'`排序，返回未排序的列表，按`'POINT'`排序，返回一个列表，它的items是按照`points`属性排序的。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const SORTS = {
  NONE: list => list,
  TITLE: list => sortBy(list, 'title'),
  AUTHOR: list => sortBy(list, 'author'),
  COMMENT: list => sortBy(list, 'num_comments').reverse(),
  POINT: list => sortBy(list, 'points').reverse(),
};
# leanpub-end-insert

const List = ({ list, onRemoveItem }) => {
  ...
};
~~~~~~~

> With the `sort`  (`sortKey`) state and all possible sort variations with `SORTS` at our disposal, we can sort the list before mapping it over each Item component:

有了`排序`(`sortKey`)状态和所有可能的排序变化，我们就可以对列表进行排序，然后在将其映射到每个Item组件上：

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }) => {
  const [sort, setSort] = React.useState('NONE');

  const handleSort = sortKey => {
    setSort(sortKey);
  };

# leanpub-start-insert
  const sortFunction = SORTS[sort];
  const sortedList = sortFunction(list);
# leanpub-end-insert

  return (
    <div>
      ...

# leanpub-start-insert
      {sortedList.map(item => (
# leanpub-end-insert
        <Item
          key={item.objectID}
          item={item}
          onRemoveItem={onRemoveItem}
        />
      ))}
    </div>
  );
};
~~~~~~~

> It's done. First we extracted the sort function from the dictionary by its `sortKey` (state). Then, we applied the function to the list, before mapping it to render each Item component. Again, the initial sort state will be `'NONE'`, meaning it will sort nothing.

就这样完成了。首先我们提取了排序函数从字典中根据它的`sortKey`状态。然后，我们将函数应用到了列表中，在我们映射它对于渲染每个Item组件之前。同样，在排序状态的初始值是`'NONE'`，意味着它不会进行排序。

> Second we rendered more HTML buttons to give our users interaction. Then, we added implementation details for each button by changing the sort state. Finally, we used the sort state to sort the actual list.

其次为了给用户更多的互动，我们渲染了更多的HTMl按钮。然后，我们通过改变排序状态，添加了详细的实现关于每个按钮。最后，我们使用了排序状态来对实际的列表进行排序。

### Exercises:

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Sort).
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Sort?expand=1).
> * Read more about [Lodash](https://lodash.com/).

阅读跟更多关于[Lodash](https://lodash.com/).

> * Why did we use numeric properties like `points` and `num_comments` a reverse sort?

为什么我们使用比如 `points`和`num_comments`数字的属性进行反转排序？

> * Use your styling skills to give the user feedback about the current active sort. This mechanism can be as straightforward as giving the active sort button a different color.

针对你当前排序，使用你的样式技巧给用户反馈。这个机制会像给当前选中的排序按钮换颜色一样直接。