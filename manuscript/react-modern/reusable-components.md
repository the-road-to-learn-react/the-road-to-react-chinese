## 可复用组件

仔细看一下搜索组件。label 元素有文本值 "Search: "，它的 id 和 htmlFor 属性值均是 `search` 特有的，同时有 `onSearch` 回调处理函数。该组件通常与搜索功能相关联，使得该组件在应用程序的非搜索功能中的可复用性较差。如果将两个搜索组件同时渲染，由于2个组件的 htmlFor 与 id 值是相同的，当用户点击其中一个标签时会破坏焦点，从而引入 bug。

该 Search 组件实际没有任何的"搜索"能力，只需花费很少的精力即可覆盖其他通用的搜索场景，以使该组件可在应用程序的其余部分中复用。让我们向 Search 组件传递一个 `id` 和 `label` 参数，将值和回调函数重命名为更抽象的名称，并相应地重命名该组件：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 ...

 return (
   <div>
     <h1>My Hacker Stories</h1>

# leanpub-start-insert
     <InputWithLabel
       id="search"
       label="Search"
# leanpub-end-insert
       value={searchTerm}
# leanpub-start-insert
       onInputChange={handleSearch}
# leanpub-end-insert
     />

     ...
   </div>
 );
};

# leanpub-start-insert
const InputWithLabel = ({ id, label, value, onInputChange }) => (
# leanpub-end-insert
 <>
# leanpub-start-insert
   <label htmlFor={id}>{label}</label>
   &nbsp;
# leanpub-end-insert
   <input
# leanpub-start-insert
     id={id}
# leanpub-end-insert
     type="text"
     value={value}
# leanpub-start-insert
     onChange={onInputChange}
# leanpub-end-insert
   />
 </>
);
~~~~~~~

目前它尚未完全可复用。如果我们希望在 input 中输入数字（`number`）或电话号码（`tel`）之类的数据，则需要将 input 的 `type` 属性从外部传入：

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
 id,
 label,
 value,
# leanpub-start-insert
 type = 'text',
# leanpub-end-insert
 onInputChange,
}) => (
 <>
   <label htmlFor={id}>{label}</label>
   &nbsp;
   <input
     id={id}
# leanpub-start-insert
     type={type}
# leanpub-end-insert
     value={value}
     onChange={onInputChange}
   />
 </>
);
~~~~~~~

在 App 组件中，没有向 InputWithLabel 组件传递 `type` 参数，所以 `type` 没有从外部指定，而是从函数的
[默认参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)中得到。

只需一些更改，我们就将专用的 Search 组件变成了更可复用的组件。我们对内部实现细节的命名进行了抽象，并为新组件提供了更多的接口，以便可以从外部传递必要的参数。 我们还没有在其他地方使用该组件，但是我们增强了它的能力。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Reusable-React-Component)
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Fragments...hs/Reusable-React-Component?expand=1)
* 阅读更多关于[复用React 组件](https://www.robinwieruch.de/react-reusable-components)
* 在之前我们使用纯文本 "Search:" 中带着 ":"。 你现在将如何处理呢？
 你会使用 `label="Search:"` 作为参数将其传递给 InputWithLabel 组件呢，还是将其硬编码在 InputWithLabel 组件`<label htmlFor={id}>{label}:</label>`中使用？稍后章节我们将学习如何抉择。
