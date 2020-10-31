# 真实 React 世界（高级）

我们已经介绍了 React 大部分基础知识，它的遗留特性以及维护应用程序的技术。现在是时候开发真实世界的 React 应用程序。下面跟着的每一节将会有一个任务。尝试首先在没有*选项提示*的情况下去解决这些问题，但是要注意，这些任务在你第一次尝试时，是具有挑战性的。如果你需要帮助，使用*选项提示*或者按照本节的说明操作。


## 排序

**任务:**在处理 items 列表时，经常包括互动，让数据更加能被用户接受。到目前为止，每一个 item 的属性都被列出来了。为了让它更加具有探索性，这个列表应该根据 title， author， comments，和 points 进行升序或者降序，对每个属性进行排序。按照一个方向排序就好，因为另一个方向的排序在下一章的内容。

**可选择的提示:**

* 在 App 组件或者 List 组件中，引入一种新的排序状态。
* 对于每个属性（例如：`title`, `author`, `points`, `num_comments`，）实现一个 HTML 按钮，用来设置这个属性的排序状态。
* 使用排序状态对 `list` 应用适当的排序函数
* 使用工具库，比如使用 [Lodash](https://lodash.com/) 的 `sortBy` 函数。

![](images/sort.png)

我们将把数据列表当作一个表来看。每一行代表列表里的一个 Item，每一个列代表 Item 的一个属性。针对每一列，标题提供了用户更多的指导：

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

我们使用的是最基本的内联样式布局。为了让标题布局匹配行，也在给 Item 组件中的每一行布局：

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

在当前的实现中，我们将移除样式属性，因为它占用了很多空间并且干扰了实际的实现逻辑（因此提取它到适当的 CSS）。但是我鼓励你留着它。

List 组件将处理这个新的排序状态。App 组件也可以这么做，但是仅在 List 里面，所以我们将状态管理提升到它的状态管理。排序状态初始值是 `'NONE'`，因此列表中的 items 会按照请求 API 获取的数据的顺序显示。此外，我们添加了一个新的处理程序来设置排序状态，根据一个特定的键。

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

在 List 组件的头部，按钮可以帮助我们设置每个列/属性的排序状态。一个内联处理程序被用来针对特定的键（`sortKey`）。当点击 “Title” 栏按钮时，`'TITLE'` 会变成新的排序状态。

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

新功能的状态管理已经被实现，但是当按钮被点击的时候我们不会看到任何东西。这是因为排序机制没有被应用到新的 `list`。

在 JavaScript 中，对数组进行排序是不是一个简单的事情，因为每一个 JavaScript 原始类型（比如：string，boolean，number）都有边缘问题，当数组按照它的属性进行排序的时候。我们将使用第三方库 [Lodash](https://lodash.com/) 来解决这个问题，它自带了很多 JavaScript 工具函数（比如：`sortBy`）。首先，在命令行安装它：

{title="Command Line",lang="text"}
~~~~~~~
npm install lodash
~~~~~~~

第二，在你文件的顶部，引入排序工具函数：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';
# leanpub-start-insert
import { sortBy } from 'lodash';
# leanpub-end-insert

...
~~~~~~~

第三，创造一个 JavaScript 对象（也称为字典），其中包含所有可能的 `sortKey` 和排序方法的映射。每一个被指定的排序键会被映射到一个函数，该函数对传入的`列表`进行排序。按 `'NONE'` 排序，返回未排序的列表，按 `'POINT'` 排序，返回一个列表，它的 items 是按照 `points` 属性排序的。

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

有了`排序`(`sortKey`)状态和所有可能的排序变化，我们就可以对列表进行排序，然后在将其映射到每个 Item 组件上：

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

就这样完成了。首先我们根据它的`sortKey`状态从字典中提取了排序函数。然后，我们在列表数据映射并渲染每个 Item 组件之前，将函数应用到了列表上。同样，排序状态的初始值是 `'NONE'` 时，意味着它不会进行排序。

其次为了给用户更多的互动，我们渲染了更多的 HTMl 按钮。然后，我们通过改变排序状态，添加了关于每个按钮的详细实现。最后，我们使用了排序状态来对实际的列表进行排序。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Sort)
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Sort?expand=1)
* 阅读跟更多关于 [Lodash](https://lodash.com/)。
* 为什么我们使用比如 `points` 和 `num_comments` 数字的属性进行反转排序？
* 针对你当前排序，使用你的样式技巧给用户反馈。这个机制会像给当前选中的排序按钮换颜色一样直接。
