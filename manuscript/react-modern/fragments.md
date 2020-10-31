## React Fragments

JSX 里有个需要注意的地方，尤其当我们创建专门的 Search 组件时，必须引入一个 HTML 元素把它包起来才能渲染：

{title="src/App.js",lang="javascript"}

~~~~~~~
const Search = ({ search, onSearch }) => (
# leanpub-start-insert
 <div>
# leanpub-end-insert
   <label htmlFor="search">Search: </label>
   <input
     id="search"
     type="text"
     value={search}
     onChange={onSearch}
   />
# leanpub-start-insert
 </div>
# leanpub-end-insert
);
~~~~~~~

通常一个 React 组件返回的 JSX 只能有一个顶层元素。如果需要渲染多个同级的顶层元素，就必须把它们放在数组里。因为是一组元素，我们又必须给每个兄弟元素加上 `key` 属性：

{title="src/App.js",lang="javascript"}
~~~~~~~
const Search = ({ search, onSearch }) => [
 <label key="1" htmlFor="search">
   Search:{' '}
 </label>,
 <input
   key="2"
   id="search"
   type="text"
   value={search}
   onChange={onSearch}
 />,
];
~~~~~~~

这是一种解决 JSX 里同时需要多个顶层元素的方式。但看起来可读性并不好，而且需要一个额外的 key 属性，显得很冗余。另外一种解决方式就是使用 **React fragment** ：

{title="src/App.js",lang="javascript"}
~~~~~~~
const Search = ({ search, onSearch }) => (
# leanpub-start-insert
 <>
# leanpub-end-insert
   <label htmlFor="search">Search: </label>
   <input
     id="search"
     type="text"
     value={search}
     onChange={onSearch}
   />
# leanpub-start-insert
 </>
# leanpub-end-insert
);
~~~~~~~

Fragment 可以用来把多个元素用一个顶层元素包起来，又不会产生额外的渲染结果。Search 里的元素：输入框和标签，现在应该都可以显示在浏览器里了。所以如果你想要省略外面包的这层 `<div>` 或 `<span>` 元素，JSX 允许你用一个空的 tag 替换掉它们，这样就不会在渲染出来的 HTML 里引入中间元素了。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Fragments)。
* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Custom-Hooks...hs/React-Fragments?expand=1)。
* 阅读更多关于 [React fragments](https://reactjs.org/docs/fragments.html) 的内容。
