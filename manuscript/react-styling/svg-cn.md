## React 中的 SVG

要创建一个现代 React 应用程序，我们可能需要使用 SVG。例如我们可能不想给每个按钮元素都加上文字，而是希望用一个图标来显示，从而变得轻量级。在本节中，我们将在一个 React 组件中使用一个可扩展的矢量图形（SVG）作为图标。

这一部分是基于我们前面介绍的 "React 中的 CSS "的基础上，让 SVG 图标有一个好的外观和感觉。使用不同的样式方法或者完全不使用样式都是可以接受的，尽管没有加上样式的 SVG 可能会显得不伦不类。

这个 SVG 的图标来自于 [Flaticon's Freepick](https://www.flaticon.com/authors/freepik)。这个网站上的许多 SVG 都是免费的，但需要注明作者。你可以从[这里](https://www.flaticon.com/free-icon/check_109748)下载 SVG 图标，并将其作为 *src/check.svg* 放到你的项目中。下载 SVG 文件是推荐的方式，这里是 SVG 的定义：

{title="Code Playground",lang="html"}
~~~~~~~
<?xml version="1.0" encoding="iso-8859-1"?>
<!-- Generator: Adobe Illustrator 18.0.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
   viewBox="0 0 297 297" style="enable-background:new 0 0 297 297;" xml:space="preserve">
  <g>
    <path d="M113.636,272.638c-2.689,0-5.267-1.067-7.168-2.97L2.967,166.123c-3.956-3.957-3.956-10.371-0.001-14.329l54.673-54.703
      c1.9-1.9,4.479-2.97,7.167-2.97c2.689,0,5.268,1.068,7.169,2.969l41.661,41.676L225.023,27.332c1.9-1.901,4.48-2.97,7.168-2.97l0,0
      c2.688,0,5.268,1.068,7.167,2.97l54.675,54.701c3.956,3.957,3.956,10.372,0,14.328L120.803,269.668
      C118.903,271.57,116.325,272.638,113.636,272.638z M24.463,158.958l89.173,89.209l158.9-158.97l-40.346-40.364L120.803,160.264
      c-1.9,1.902-4.478,2.971-7.167,2.971c-2.688,0-5.267-1.068-7.168-2.97l-41.66-41.674L24.463,158.958z"/>
  </g>
</svg>
~~~~~~~

因为我们再次使用了 create-react-app，所以我们可以马上导入 SVG（类似于 CSS）作为 React 组件。在 *src/App.js* 中，使用下面的语法导入 SVG：

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';

import './App.css';
# leanpub-start-insert
import { ReactComponent as Check } from './check.svg';
# leanpub-end-insert
~~~~~~~

我们正在导入一个 SVG，SVG 可以有许多不同用途（如 logo、背景）。用一个 SVG 组件作为 `height` 和 `width` 属性来代替一个按钮文本。

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <div className="item">
    <span style={{ width: '40%' }}>
      <a href={item.url}>{item.title}</a>
    </span>
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
      <button
        type="button"
        onClick={() => onRemoveItem(item)}
        className="button button_small"
      >
# leanpub-start-insert
        <Check height="18px" width="18px" />
# leanpub-end-insert
      </button>
    </span>
  </div>
);
~~~~~~~

无论你使用的是哪种样式的方法，你都可以给你的 SVG 按钮中的图标加上悬停效果。在基本的CSS方法中，*src/App.css* 文件中看起来像下面这样：

{title="src/App.css",lang="css"}
~~~~~~~
.button:hover > svg > g {
  fill: #ffffff;
  stroke: #ffffff;
}
~~~~~~~

creat-react-app 项目让使用 SVG 变得简单直接，不需要额外的配置。如果你用 Webpack 等构建工具从头开始创建一个 React 项目，那就不一样了，因为你必须自己去处理。总之，SVG 可以让你的应用程序更加平易近人，所以在你觉得适合的时候就可以使用它们。

### 练习：

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/CSS-in-React-SVG)。
  * 检查[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/CSS-in-React...hs/CSS-in-React-SVG?expand=1)。
* 阅读更多关于[create-react-app 中的 SVGs](https://create-react-app.dev/docs/adding-images-fonts-and-files)。
* 阅读更多关于[React 中的 SVG 背景图案](https://www.robinwieruch.de/react-svg-patterns)。
* 在你的应用程序中添加另一个 SVG 图标。
