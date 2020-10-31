## 可复用组件

> Have a closer look at the Search component. The label element has the text "Search: "; the id/htmlFor attributes have the `search` identifier; the value is called `search`; and the callback handler is called `onSearch`. The component is very much tied to the search feature, which makes it less reusable for the rest of the application and non search-related tasks. It also risks introducing bugs if two of these Search components are rendered side by side, because the htmlFor/id combination is duplicated, breaking the focus when one of the labels is clicked by the user.

仔细看一下搜索组件。label 元素有文本值 "Search: "，它的 id 和 htmlFor 属性值均是 `search` 特有的，同时有 `onSearch` 回调处理函数。该组件通常与搜索功能相关联，使得该组件在应用程序的非搜索功能中的可复用性较差。如果将两个搜索组件同时渲染，由于2个组件的 htmlFor 与 id 值是相同的，当用户点击其中一个标签时会破坏焦点，从而引入 bug。

> Since the Search component doesn't have any actual "search" functionality, 
it takes little effort to generalize other search domain properties to make the component reusable for the rest of the application. 
Let's pass an additional `id` and `label` prop to the Search component, 
rename the actual value and callback handler to something more abstract, and rename the component accordingly:

该 Search 组件实际没有任何的"搜索"能力，只需花费很少的精力即可覆盖其他通用的搜索场景，以使该组件可在应用程序的其余部分中复用。让我们向 Search 组件传递一个 `id` 和 `label` 参数，将值和回调函数重命名为更抽象的名称，重命名该组件为 InputWithLabel。

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

> It's not fully reusable yet. If we want an input field for data like a number (`number`) or phone number (`tel`), the `type` attribute of the input field needs to be accessible from the outside too:

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

> From the App component, no `type` prop is passed to the InputWithLabel component,
so it is not specified from the outside. The 
[default parameter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)
from the function signature takes over for the input field.

在 App 组件中，没有向 InputWithLabel 组件传递 `type` 参数，所以 `type` 没有从外部指定，而是从函数的
[默认参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)中得到。


> With just a few changes we turned a specialized Search component into a more reusable component. 
We generalized the naming of the internal implementation details and gave the new component a larger API surface to provide all the necessary information from the outside.
 We aren't using the component elsewhere, but we increased its ability to handle the task if we do.

只需一些更改，我们就将专用的 Search 组件变成了更可复用的组件。我们对内部实现细节的命名进行了抽象，并为新组件提供了更多的接口，以便可以从外部传递必要的参数。 我们还没有在其他地方使用该组件，但是我们增强了它的能力。


> ### Exercises:

### 练习:

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Reusable-React-Component).
> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Fragments...hs/Reusable-React-Component?expand=1).
> * Read more about [Reusable React Components](https://www.robinwieruch.de/react-reusable-components).
> * Before we used the text "Search:" with a ":". How would you deal with it now? 
 Would you pass it with `label="Search:"` as prop to the InputWithLabel component or hardcode it after the `<label htmlFor={id}>{label}:</label>` usage in the InputWithLabel component? We will see how to cope with this later.


* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Reusable-React-Component)
   * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Fragments...hs/Reusable-React-Component?expand=1)
* 阅读更多关于[复用React 组件](https://www.robinwieruch.de/react-reusable-components)
* 在之前我们使用纯文本 "Search:" 中带着 ":"。 你现在将如何处理呢？
 你会使用 `label="Search:"` 作为参数将其传递给 InputWithLabel 组件呢，还是将其硬编码在 InputWithLabel 组件`<label htmlFor={id}>{label}:</label>`中使用？稍后章节我们将学习如何抉择。
