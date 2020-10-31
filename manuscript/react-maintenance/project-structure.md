> ## React Project Structure

## React 项目结构

 > With multiple React components in one file, you might wonder why we didn't put components into different files for the *src/App.js* file from the start. We already have multiple components in the file that can be defined in their own files/folders (also called modules). For learning, it's more practical to keep these components in one place. Once our application grows, we'll consider splitting these components into multiple modules so it scales properly.

一个文件中有多个组件，你可能会好奇为什么一开始我们没有把在 *src/App.js* 文件里的组件放入不同文件里。我们已经有了多个组件在文件中，可以被定义在他们自己的文件/文件夹中（也称为模块）。为了方便学习，把这些组件放在一个地方更加实用。一旦我们应用需要扩展，我们将考虑拆分这些组件进入多个模块，以便它正确的扩展。

> Before we restructure our React project, recap [JavaScript's import and export statements](https://www.robinwieruch.de/javascript-import-export). Importing and exporting files are two fundamental concepts in JavaScript you must learn before React. There's no right way to structure a React application, as they evolve naturally along with the project's structure.

在我们重组 React 项目之前，请回顾 [JavaScript 的导入和导出语句](https://www.robinwieruch.de/javascript-import-export)。在 JavaScript 中，导入和导出文件是两个基本的概念，你必须在 React 之前学习。没有唯一正确的方法构建 React 项目，因为它们会随着项目结构自然发展。

> We'll complete a simple refactoring for the project's folder/file structure for the sake of learning about the process. Afterward, there will be a few additional options about restructuring this project or React projects in general. You can continue with the restructured project, though we'll continue developing with the *src/App.js* file to keep things simple.

为了学习这个过程，我们将完成一个简单的关于项目文件夹/文件结构的重构。之后，通常将会有其他的选项来重组这个项目或者 React 项目。你仍然可以继续用重组后项目，即使我们将继续开发 *src/App.js* 文件，为了保持简单。

> On the command line in your project's folder, navigate into the *src/* folder and create the following component dedicated files:

在项目的文件夹的命令上，导航到 *src/* 文件夹并创建以下组件专用的文件：

{title="Command Line",lang="text"}
~~~~~~~
cd src
touch List.js InputWithLabel.js SearchForm.js
~~~~~~~

> Move every component from the *src/App.js* file in its own file, except for the List component which has to share its place with the Item component in the *src/List.js* file. Then in every file make sure to import React and to export the component which needs to be used from the file. For example, in *src/List.js* file:

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

> Since only the List component uses the Item component, we can keep it in the same file. If this changes because the Item component is used elsewhere, we can give the Item component its own file. The SearchForm component in the *src/SearchForm.js* file must import the InputWithLabel component. Like the Item component, we could have left the InputWithLabel component next to the SearchForm; but our goal is to make InputWithLabel component reusable with other components. We'll probably import it eventually.

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

> The App component has to import all the components it needs to render. It doesn't need to import InputWithLabel, because it's only used for the SearchForm component.

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

> Components that are used in other components now have their own file. Only if a component (e.g. Item) is dedicated to another component (e.g. List) do we keep it in the same file. If a component should be used as a reusable component (e.g. InputWithLabel), it also receives its own file. From here, there are several strategies to structure your folder/file hierarchy. One scenario is to create a folder for every component:

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

> The *index.js* file holds the implementation details for the component, while other files in the same folder have different responsibilities like styling, testing, and types:

在 *index.js* 文件里保留了关于组件详细的实现，而同一个文件夹中的其他组件有不同的职责，像样式、测试和类型：

{title="Project Structure",lang="text"}
~~~~~~~
- List/
-- index.js
-- style.css
-- test.js
-- types.js
~~~~~~~

> If using CSS-in-JS, where no CSS file is needed, one could still have a separate *style.js* file for all the styled components:

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

> Sometimes we'll need to move from a **technical-oriented folder structure** to a **domain-oriented folder structure**, especially once the project grows. Universal *shared/* folder is shared across domain specific components:

有时我们需要从**面向技术的文件夹结构**转向**面向域文件夹结构**，特别是随着项目的增长。通用的 *shared/* 文件夹被指定的组件跨域共享：

{title="Project Structure",lang="text"}
~~~~~~~
- Messages.js
- Users.js
- shared/
-- Button.js
-- Input.js
~~~~~~~

> If you scale this to the deeper level folder structure, each component will have its own folder in a domain-oriented project structure as well:

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

> There are many ways on how to structure your React project from small to large project: simple to complex folder structure; one-level nested to two-level nested folder nesting; dedicated folders for styling, types and testing next to implementation logic. There is no right way for folder/file structures.

从小项目到大项目，这里有很多方式构建 React 项目。简单到复杂的文件夹结构，一级嵌套到二级文件嵌套，靠近实现逻辑，有专门的文件夹针对样式、类型和测试。关于文件夹/文件结构没有绝对正确的方法。

> A project's requirements evolve over time and so should its structure. If keeping all assets in one file feels right, then there is no rule against it. Just try to keep the nesting level shallow, otherwise you could get lost deep in folders.

项目的需求会随着时间而变化，其结构也会随着变化。如果认为是正确的，保持所有的资源在一个文件里面，这里没有任何规则反对它。只需保持嵌套结构简单，否则你会迷失在文件夹中。

> ### Exercises:

### 练习:

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Folder-Structure).

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Folder-Structure)

> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/React-Folder-Structure?expand=1).

* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/React-Folder-Structure?expand=1)

> * Read more about [JavaScript's import and export statements](https://www.robinwieruch.de/javascript-import-export).

* 阅读更多关于 [JavaScript 的输入和输出语句](https://www.robinwieruch.de/javascript-import-export).

> * Read more about [React Folder Structures](https://www.robinwieruch.de/react-folder-structure).

*阅读更多关于 [React 文件夹结构](https://www.robinwieruch.de/react-folder-structure)

> * Keep the current folder structure if you feel confident. The ongoing sections will omit it, only using the *src/App.js* file.

* 如果你有信心，就保持当前的文件夹结构。正在进行的部分将被省略，仅使用 *src/App.js* 文件。