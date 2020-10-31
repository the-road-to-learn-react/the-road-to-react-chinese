## React 项目结构

一个文件中有多个组件，你可能会好奇为什么一开始我们没有把在 *src/App.js* 文件里的组件放入不同文件里。我们已经有了多个组件在文件中，可以被定义在他们自己的文件/文件夹中（也称为模块）。为了方便学习，把这些组件放在一个地方更加实用。一旦我们应用需要扩展，我们将考虑拆分这些组件进入多个模块，以便它正确的扩展。

在我们重组 React 项目之前，请回顾 [JavaScript 的导入和导出语句](https://www.robinwieruch.de/javascript-import-export)。在 JavaScript 中，导入和导出文件是两个基本的概念，你必须在 React 之前学习。没有唯一正确的方法构建 React 项目，因为它们会随着项目结构自然发展。

为了学习这个过程，我们将完成一个简单的关于项目文件夹/文件结构的重构。之后，通常将会有其他的选项来重组这个项目或者 React 项目。你仍然可以继续用重组后项目，即使我们将继续开发 *src/App.js* 文件，为了保持简单。

在项目的文件夹的命令上，导航到 *src/* 文件夹并创建以下组件专用的文件：

{title="Command Line",lang="text"}
~~~~~~~
cd src
touch List.js InputWithLabel.js SearchForm.js
~~~~~~~

将 *src/App.js* 文件中的每个组件移动到它自己的文件，除了 List 组件必须与 *src/List.js* 中的 Item 组件共享它的位置。然后在每个文件中确保引入了 React 并从文件中导出需要被使用的组件。例如，*src/List.js* 文件：

{title="src/List.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
import React from 'react';
# leanpub-end-insert

const List = ({ list, onRemoveItem }) =>
  list.map(item => (
    <Item
      key={item.objectID}
      item={item}
      onRemoveItem={onRemoveItem}
    />
  ));

const Item = ({ item, onRemoveItem }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
    <span>
      <button type="button" onClick={() => onRemoveItem(item)}>
        Dismiss
      </button>
    </span>
  </div>
);

# leanpub-start-insert
export default List;
# leanpub-end-insert
~~~~~~~

由于仅有 List 组件使用了 Item 组件，我们可以保持它在相同的文件。如果 Item 组件在其他地方被使用，而导致情况变化，我们会给 Item 组件提供它自己的文件。*src/SearchForm.js* 文件中的 SearchForm 组件必须引入 InputWithLabel 组件。像 Item 组件，我们可以把 InputWithLabel 组件留在 SearchForm 旁，但是我们的目标是让 InputWithLabel 组件和其他组件可以重复使用，我们最后将引入它：

{title="src/SearchForm.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
import React from 'react';
# leanpub-end-insert

# leanpub-start-insert
import InputWithLabel from './InputWithLabel';
# leanpub-end-insert

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

# leanpub-start-insert
export default SearchForm;
# leanpub-end-insert
~~~~~~~

App 组件必须引入全部它需要渲染的组件。它不需要引入 InputWithLabel，因为它仅被使用在 SearchForm 组件中。

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';

# leanpub-start-insert
import SearchForm from './SearchForm';
import List from './List';
# leanpub-end-insert

...

const App = () => {
  ...
};

export default App;
~~~~~~~

在其他组件中被使用的组件现在有了自己的文件。仅当组件（比如：Item）被另一个组件（比如：List）专用时，我们将它保持在一个文件里。如果一个组件应作为可重用的组件（比如：InputWithLabel）被使用时，它也会有自己的文件。从这里开始，这里有几个关于构造你的文件夹/文件结构的策略。一个方案是为每一个组件创造一个文件夹：

{title="Project Structure",lang="text"}
~~~~~~~
- List/
-- index.js
- SearchForm/
-- index.js
- InputWithLabel/
-- index.js
~~~~~~~

在 *index.js* 文件里保留了关于组件详细的实现，而同一个文件夹中的其他组件有不同的职责，像样式、测试和类型：

{title="Project Structure",lang="text"}
~~~~~~~
- List/
-- index.js
-- style.css
-- test.js
-- types.js
~~~~~~~

如果使用 CSS-in-JS，那么就不需要使用 css 文件。关于所有有样式的组件，仍然可以有一个单独的 *style.js* 文件：

{title="Project Structure",lang="text"}
~~~~~~~
- List/
-- index.js
# leanpub-start-insert
-- style.js
# leanpub-end-insert
-- test.js
-- types.js
~~~~~~~

有时我们需要从**面向技术的文件夹结构**转向**面向域文件夹结构**，特别是随着项目的增长。通用的 *shared/* 文件夹被指定的组件跨域共享：

{title="Project Structure",lang="text"}
~~~~~~~
- Messages.js
- Users.js
- shared/
-- Button.js
-- Input.js
~~~~~~~

如果你扩展到更深的的文件夹结构中，在面向域的项目结构中每一个组件也将会有它自己的文件夹：

{title="Project Structure",lang="text"}
~~~~~~~
- Messages/
-- index.js
-- style.css
-- test.js
-- types.js
- Users/
-- index.js
-- style.css
-- test.js
-- types.js
- shared/
-- Button/
--- index.js
--- style.css
--- test.js
--- types.js
-- Input/
--- index.js
--- style.css
--- test.js
--- types.js
~~~~~~~

从小项目到大项目，这里有很多方式构建 React 项目。简单到复杂的文件夹结构，一级嵌套到二级文件嵌套，靠近实现逻辑，有专门的文件夹针对样式、类型和测试。关于文件夹/文件结构没有绝对正确的方法。

项目的需求会随着时间而变化，其结构也会随着变化。如果认为是正确的，保持所有的资源在一个文件里面，这里没有任何规则反对它。只需保持嵌套结构简单，否则你会迷失在文件夹中。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Folder-Structure)
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/React-Folder-Structure?expand=1)
* 阅读更多关于 [JavaScript 的输入和输出语句](https://www.robinwieruch.de/javascript-import-export).
*阅读更多关于 [React 文件夹结构](https://www.robinwieruch.de/react-folder-structure)
* 如果你有信心，就保持当前的文件夹结构。正在进行的部分将被省略，仅使用 *src/App.js* 文件。
