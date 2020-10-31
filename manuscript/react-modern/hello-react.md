# React 基础

在第一部分的学习中，我们会通过创建第一个 React 项目，介绍 React 的基础知识。然后，我们将通过实现真实的功能来探索 React 的新内容，就像开发真实的 Web 应用程序一样。最后，我们将拥有一个可正常使用的 React 应用程序，该应用程序具备客户端和服务器端搜索，获取远程数据以及高级状态管理等功能。

## 你好 React

单页面应用（[SPA](https://en.wikipedia.org/wiki/Single-page_application)）在第一代 SPA 框架如 Angular（由 Google 开发）、Ember、Knockout 和 Backbone 中变得越来越受欢迎。使用这些框架使构建 Web 应用更加简单，这些框架超越了普通的 JavaScript 和 jQuery。Facebook 于 2013 年发布了 React，这是另一种 SPA 的解决方案。这些框架全部使用 JavaScript 创建整个 Web 应用。

让我们暂时回到 SPA 出现之前：在过去，网站和 Web 应用是在服务器渲染的。用户在浏览器中访问 URL，并从 Web 服务器请求 HTML 文件及其所有相关的 HTML、CSS 和 JavaScript 文件。经过一段网络延迟后，用户会在浏览器（客户端）中看到渲染后的 HTML，随后开始与其进行交互。每进行一次页面跳转（也就是访问另一个 URL）就会再次启动这一系列的过程。在过去，本质上所有关键的步骤都由服务器完成，而客户端仅通过展示一个个渲染后的页面扮演着一个小角色。虽然使用标准的 HTML 和 CSS 构建应用程序，但是只使用了少量的 JavaScript 使实现交互（如下拉菜单）或高级样式（如定位提示工具）成为可能。jQuery 就是实现这类功能最受欢迎的 JavaScript 库。

相反，现代的 JavaScript 将焦点从服务器转移到了客户端。举个最极端的例子：用户访问 URL 并请求一个 *最小的 HTML 文件* 和一个 *相关的 JavaScript 文件* 。经过一段网络延迟后，用户会在浏览器中看到 *由 JavaScript 渲染的 HTML* 并开始与其进行交互。每一次额外的页面跳转都 *不会* 从 Web 服务器请求更多的文件，而是会使用最初请求的 JavaScript 来渲染新的页面。同样，用户的其他所有交互也都在客户端上进行处理。在现代的版本中，服务器主要通过一个小体积的 HTML 文件跨线提供 JavaScript。然后，这个 HTML 文件在客户端执行所有相关的 JavaScript，以使用 HTML 和 JavaScript 渲染整个应用程序进行交互。

在其他 SPA 解决方案中，React 使这其成为可能。本质上来说，SPA 是创建整个应用程序的一个 JavaScript 主体，这些 JavaScript 整齐的组织在文件夹和文件中，而 SPA 框架提供了构建它的所有工具。当用户访问您的 Web 应用的 URL 时，JavaScript 应用程序会通过网络一次性发送至您的浏览器。从此，React 和其他 SPA 框架就接管了浏览器中渲染和处理用户交互的任务。

随着 React 的兴起，组件的概念开始流行起来。每个组件都使用 HTML、CSS 和 JavaScript 定义外观。一旦定义好一个组件后，它就可以用在组件结构中来创建整个应用。尽管 React 作为一个库非常重视组件的概念，但周围的生态系统却使其成为了一个非常灵活的框架。React 拥有便捷的 API，还拥有稳定而繁荣的生态系统以及热情活跃的社区。我们非常欢迎您的加入 :-)

### 练习

* 阅读关于[为什么我从 Angular 换成 React](https://www.robinwieruch.de/reasons-why-i-moved-from-angular-to-react/)
* 阅读关于[如何学习一个框架](https://www.robinwieruch.de/how-to-learn-framework/)
* 阅读关于[如何学习 React](https://www.robinwieruch.de/learn-react-js)
* 阅读关于[为什么框架很重要](https://www.robinwieruch.de/why-frameworks-matter)
* 浏览[React 所需的 JavaScript 基础](https://www.robinwieruch.de/javascript-fundamentals-react-requirements) -- 不用太关注 React，看一下自己对 React 中用到的 JavaScript 特性了解多少
