## 在React中使用TypeScript

在 JavaScript 和 React 中使用 TypeScript，对于开发强大的应用程序有许多好处。TypeScript 集成后不会在运行时在命令行或浏览器中出现类型错误，而是会在 IDE 内编译时显示它们。它缩短了 JavaScript 开发的反馈循环的周期。TypeScript 不仅改善了开发人员的体验，也让代码变得更加自文档化，并且增加可读性，因为每个变量都使用了类型定义。同样的，移动代码块或执行更大的代码库的重构也变得更加高效。诸如 TypeScript 之类的静态类型语言是发展趋势，因为它们比诸如 JavaScript 之类的动态类型语言具有更多优势。所以，尽可能多地了解 [Typescript](https://www.typescriptlang.org/index.html) 是非常有用的。

要在 React 中使用 TypeScript，请使用命令行将 TypeScript 及其依赖项安装到你的应用程序中。如果遇到障碍，请遵循官方 TypeScript 安装说明中的 [create-react-app](https://create-react-app.dev/docs/adding-typescript/)：

{title="Command Line",lang="text"}
~~~~~~~
npm install --save typescript @types/node @types/react
npm install --save typescript @types/react-dom @types/jest
~~~~~~~

接下来，将所有 JavaScript 文件(*.js*)重命名为 TypeScript 文件(*.tsx*)。

{title="Command Line",lang="text"}
~~~~~~~
mv src/index.js src/index.tsx
mv src/App.js src/App.tsx
~~~~~~~

然后在命令行中重新启动开发服务器。你可能会在浏览器和 IDE 中遇到编译错误的问题。如果是 IDE 没有正确显示编译错误，请尝试为你的编辑器安装 TypeScript 插件，或者为 IDE 安装扩展程序。在 React 设置中初始化 TypeScript 之后，我们将为整个 *src/App.tsx* 文件添加安全类型，从编写自定义 hook 的参数开始：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (
# leanpub-start-insert
  key: string,
  initialState: string
# leanpub-end-insert
) => {
  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
    localStorage.setItem(key, value);
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

为函数的参数添加类型定义的做法主要关注在 Javascript，而不是 React。我们定义该函数需要两个参数，并且两个参数都是 JavaScript 中的基本类型 string。同样的，我们可以定义该函数的返回值类型为字符串数组，并告诉一些类似 `state updater function` 的方法，该函数不返回任何值(`void`)：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (
  key: string,
  initialState: string
# leanpub-start-insert
): [string, (newValue: string) => void] => {
# leanpub-end-insert
  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
    localStorage.setItem(key, value);
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

尽管与 React 有关，但考虑到以前对自定义 hook 的类型安全性的改进，我们没有在函数体内部的 React hooks 中添加类型。这是因为**类型推断**在大多数情况下对于 React hooks 都适用。如果一个 React useState Hook 的 *初始化 state* 是一个 JavaScript 的字符串类型，那么返回的 *当前的 state* 将被推断为一个字符串类型，而返回的 *state 更新函数* 将仅以字符串作为参数，并且没有任何返回值：

{title="Code Playground",lang="javascript"}
~~~~~~~
const [value, setValue] = React.useState('React');
// value is inferred to be a string
// setValue only takes a string as argument
~~~~~~~

我们有多种方法来实现为 React 应用程序及其组件添加类型安全。我们可以从应用程序的一个较小的组件的 props 和 state 开始进行修改。例如，Item 组件接收一个 story（此处为 `item`）以及一个回调处理函数（此处为 `onRemoveItem`）。我们可以像之前一样，为两个函数的参数添加内联的类型：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
const Item = ({
  item,
  onRemoveItem,
# leanpub-start-insert
}: {
  item: {
    objectID: string;
    url: string;
    title: string;
    author: string;
    num_comments: number;
    points: number;
  };
  onRemoveItem: (item: {
    objectID: string;
    url: string;
    title: string;
    author: string;
    num_comments: number;
    points: number;
  }) => void;
}) => (
# leanpub-end-insert
  <div>
    ...
  </div>
);
~~~~~~~

这里的代码会有两个问题：代码冗长，并且有重复项。我们可以通过在 *src/App.js* 顶部的组件外部添加自定义的 Story 类型来解决这两个问题：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
# leanpub-start-insert
type Story = {
  objectID: string;
  url: string;
  title: string;
  author: string;
  num_comments: number;
  points: number;
};
# leanpub-end-insert

...

const Item = ({
  item,
  onRemoveItem,
# leanpub-start-insert
}: {
  item: Story;
  onRemoveItem: (item: Story) => void;
}) => (
# leanpub-end-insert
  <div>
    ...
  </div>
);
~~~~~~~

`item` 定义为 `Story` 类型；`onRemoveItem` 函数将接受一个 `Story` 类型的参数，并且没有任何返回值。接下来，可以通过在外部定义 Item 组件的 props 的类型的方式来整理一下代码：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
# leanpub-start-insert
type ItemProps = {
  item: Story;
  onRemoveItem: (item: Story) => void;
};
# leanpub-end-insert

# leanpub-start-insert
const Item = ({ item, onRemoveItem }: ItemProps) => (
# leanpub-end-insert
  <div>
    ...
  </div>
);
~~~~~~~

这是用 TypeScript 定义 React 组件的 props 的最常用的方法。我们可以在组件树中向上寻找到 List 组件，并为 List 组件的 props 添加类型定义：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
type Story = {
  ...
};

# leanpub-start-insert
type Stories = Array<Story>;
# leanpub-end-insert

...

# leanpub-start-insert
type ListProps = {
  list: Stories;
  onRemoveItem: (item: Story) => void;
};
# leanpub-end-insert

# leanpub-start-insert
const List = ({ list, onRemoveItem }: ListProps) =>
# leanpub-end-insert
  list.map(item => (
    <Item
      key={item.objectID}
      item={item}
      onRemoveItem={onRemoveItem}
    />
  ));
~~~~~~~

我们在 `ItemProps` 和 `ListProps` 中重复定义了 `onRemoveItem` 方法。为了更加简洁，你*可以*将其提取为一个独立定义的 `OnRemoveItem` 的类型，并且在需要使用 `onRemoveItem` 属性的地方复用它。需要注意的是，随着组件被拆分成不同的文件，开发会变得越来越困难。因此，我们将在此处保留重复项。

当我们我们已经定义了 `Story` 和 `Stories` 类型，便可以将它们重新用于其他组件。将 Story 类型添加到 App 组件中的回调处理器中：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleRemoveStory = (item: Story) => {
# leanpub-end-insert
    dispatchStories({
      type: 'REMOVE_STORY',
      payload: item,
    });
  };

  ...
};
~~~~~~~

`reducer` 方法也可以管理 `Story` 类型，除非我们不关心 `state` 和 `action` 的类型。作为应用程序的开发人员，我们需要知道传递给该 reducer 函数的参数对象及其类型：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
# leanpub-start-insert
type StoriesState = {
  data: Stories;
  isLoading: boolean;
  isError: boolean;
};
# leanpub-end-insert

# leanpub-start-insert
type StoriesAction = {
  type: string;
  payload: any;
};
# leanpub-end-insert

# leanpub-start-insert
const storiesReducer = (
  state: StoriesState,
  action: StoriesAction
) => {
# leanpub-end-insert
  ...
};
~~~~~~~

带有 `string` 和 `any`（TypeScript **通配符**）类型定义的 `Action` 类型看起来不太明确；而且我们通过它无法获得任何类型的安全性，因为 actions 是不可区分的。我们可以通过将每个 action 的类型指定为**接口**来优化，并使用**联合类型**（此处为 `StoriesAction`）以确保最终类型安全：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
# leanpub-start-insert
interface StoriesFetchInitAction {
  type: 'STORIES_FETCH_INIT';
}
# leanpub-end-insert

# leanpub-start-insert
interface StoriesFetchSuccessAction {
  type: 'STORIES_FETCH_SUCCESS';
  payload: Stories;
}
# leanpub-end-insert

# leanpub-start-insert
interface StoriesFetchFailureAction {
  type: 'STORIES_FETCH_FAILURE';
}
# leanpub-end-insert

# leanpub-start-insert
interface StoriesRemoveAction {
  type: 'REMOVE_STORY';
  payload: Story;
}
# leanpub-end-insert

# leanpub-start-insert
type StoriesAction =
  | StoriesFetchInitAction
  | StoriesFetchSuccessAction
  | StoriesFetchFailureAction
  | StoriesRemoveAction;
# leanpub-end-insert

const storiesReducer = (
  state: StoriesState,
  action: StoriesAction
) => {
  ...
};
~~~~~~~

现在的 stories state，current state和 action 都定义了类型，并且返回新的 state（通过推断出来的）现在是安全类型。例如，如果你使用未定义的 `action` 类型将 action 分派给 reducer，则会出现类型错误。或者，如果你将 store 以外的其他属性传递给删除 story 的 action，则也会出现类型错误。

在 List 组件中，组件的 return 语句仍然存在类型安全问题。可以通过给 List 组件包装 HTML `div` 元素或 React 片段来解决此问题：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }: ListProps) => (
# leanpub-start-insert
  <>
    {list.map(item => (
# leanpub-end-insert
      <Item
        key={item.objectID}
        item={item}
        onRemoveItem={onRemoveItem}
      />
# leanpub-start-insert
    ))}
  </>
# leanpub-end-insert
);
~~~~~~~

根据 TypeScript 和 React 相关的 github issue 的说法：“*这是由于编译器的限制，功能组件无法返回除 JSX 表达式或 null 以外的任何内容，否则它会报告一个隐含的错误消息，内容是指其他类型不可分配给 Element。*”

接下来关注 SearchForm 组件，该组件具有事件的回调处理器：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
# leanpub-start-insert
type SearchFormProps = {
  searchTerm: string;
  onSearchInput: (event: React.ChangeEvent<HTMLInputElement>) => void;
  onSearchSubmit: (event: React.FormEvent<HTMLFormElement>) => void;
};
# leanpub-end-insert

const SearchForm = ({
  searchTerm,
  onSearchInput,
  onSearchSubmit,
# leanpub-start-insert
}: SearchFormProps) => (
# leanpub-end-insert
  ...
);
~~~~~~~

我们通常使用 `React.SyntheticEvent` 代替 `React.ChangeEvent` 或 `React.FormEvent` 。回到 App 组件，我们使用该类型来定义回调处理器：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleSearchInput = (
# leanpub-start-insert
    event: React.ChangeEvent<HTMLInputElement>
# leanpub-end-insert
  ) => {
    setSearchTerm(event.target.value);
  };

  const handleSearchSubmit = (
# leanpub-start-insert
    event: React.FormEvent<HTMLFormElement>
# leanpub-end-insert
  ) => {
    setUrl(`${API_ENDPOINT}${searchTerm}`);

    event.preventDefault();
  };

  ...
};
~~~~~~~

现在只剩下 InputWithLabel 组件了。在处理该组件的 props 之前，让我们看一下 React 的 useRef Hook 中的 `ref`。这里的问题在于还不能推断出返回值：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
# leanpub-start-insert
  const inputRef = React.useRef<HTMLInputElement>(null!);
# leanpub-end-insert

  React.useEffect(() => {
    if (isFocused && inputRef.current) {
      inputRef.current.focus();
    }
  }, [isFocused]);
~~~~~~~

我们将返回的 `ref` 设置为安全类型，并将其定义为只读属性，因为我们仅对它执行 `focus` 方法（只读）。 React将会帮助我们将 DOM 元素设置为 `current` 属性。

最后，我们将对 InputWithLabel 组件的 props 进行安全类型的检查。需要注意的是，`children` 属性具有其 React 的特定类型，并且还有带问号的**可选类型**：

{title="src/App.tsx",lang="javascript"}
~~~~~~~
# leanpub-start-insert
type InputWithLabelProps = {
  id: string;
  value: string;
  type?: string;
  onInputChange: (event: React.ChangeEvent<HTMLInputElement>) => void;
  isFocused?: boolean;
  children: React.ReactNode;
};
# leanpub-end-insert

const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
  isFocused,
  children,
# leanpub-start-insert
}: InputWithLabelProps) => {
# leanpub-end-insert
  ...
};
~~~~~~~

`type` 和 `isFocused` 属性都是可选的。使用 `TypeScript` 的时候，你可以告诉编译器不需要将这些作为 prop 传递给组件。`children` 属性有多个适用于此概念的 TypeScript 类型，其中最通用的是 `React` 库中的 `React.ReactNode`。

最后，我们的整个 React 应用程序都引入了 TypeScript 类型，从而很容易在编译时发现类型错误。将 TypeScript 添加到 React 应用程序时，首先需要在函数的参数中添加类型定义。这些函数可以是 JavaScript 函数，自定义的 React hook 或 React 函数组件。当我们在使用 React 时，了解表单元素，事件和 JSX 的特定类型很重要。

### 练习

* 检查 [上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/TypeScript-in-React)。
* 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/TypeScript-in-React?expand=1)。
* 深入研究 [React + TypeScript 备忘单](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet#reacttypescript-cheatsheets)，因为这里也涵盖了我们在本节中面临的最常见用例。无需从头至尾了解所有内容。
* 在接下来进行各章节中的学习时，请使用 TypeScript 删除或保留你的类型。如果你选择后者，则每当遇到编译错误时都添加新的类型。
