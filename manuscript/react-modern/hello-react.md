> # Fundamentals of React

# React 基础

> In the first part of this learning experience, we'll cover the fundamentals of React, after which we'll create our first React project. Then we'll explore new aspects of React by implementing real features, the same as developing an actual web application. By the end we'll have a working React application with features like client and server-side searching, remote data fetching, and advanced state management.

在第一部分的学习中，我们会通过创建第一个 React 项目，介绍 React 的基础知识。然后，我们将通过实现真实的功能来探索 React 的新内容，就像开发真实的 Web 应用程序一样。最后，我们将拥有一个可正常使用的 React 应用程序，该应用程序具备客户端和服务器端搜索，获取远程数据以及高级状态管理等功能。

> ## Hello React

## 你好 React

> Single-page applications ([SPA](https://en.wikipedia.org/wiki/Single-page_application)) have become increasingly popular with first generation SPA frameworks like Angular (by Google), Ember, Knockout, and Backbone. Using these frameworks made it easier to build web applications which advanced beyond vanilla JavaScript and jQuery. React, yet another solution for SPAs, was released by Facebook later in 2013. All of them are used to create entire web applications in JavaScript.

单页面应用（[SPA](https://en.wikipedia.org/wiki/Single-page_application)）在第一代 SPA 框架如 Angular（由 Google 开发）、Ember、Knockout 和 Backbone 中变得越来越受欢迎。使用这些框架使构建 Web 应用更加简单，这些框架超越了普通的 JavaScript 和 jQuery。Facebook 于 2013 年发布了 React，这是另一种 SPA 的解决方案。这些框架全部使用 JavaScript 创建整个 Web 应用。

> For a moment, let's go back in time before SPAs existed: In the past, websites and web applications were rendered from the server. A user visits a URL in a browser and requests one HTML file and all its associated HTML, CSS, and JavaScript files from a web server. After some network delay, the user sees the rendered HTML in the browser (client) and starts to interact with it. Every additional page transition (meaning: visiting another URL) would initiate this chain of events again. In this version from the past, essentially everything crucial is done by the server, whereas the client plays a minimal role by just rendering page by page. While barebones HTML and CSS was used to structure the application, just a little bit of JavaScript was thrown into the mix to make interactions (e.g. toggling a dropdown) or advanced styling (e.g. positioning a tooltip) possible. A popular JavaScript library for this kind of work was jQuery.

让我们暂时回到 SPA 出现之前：在过去，网站和 Web 应用是在服务器渲染的。用户在浏览器中访问 URL，并从 Web 服务器请求 HTML 文件及其所有相关的 HTML、CSS 和 JavaScript 文件。经过一段网络延迟后，用户会在浏览器（客户端）中看到渲染后的 HTML，随后开始与其进行交互。每进行一次页面跳转（也就是访问另一个 URL）就会再次启动这一系列的过程。在过去，本质上所有关键的步骤都由服务器完成，而客户端仅通过展示一个个渲染后的页面扮演着一个小角色。虽然使用标准的 HTML 和 CSS 构建应用程序，但是只使用了少量的 JavaScript 使实现交互（如下拉菜单）或高级样式（如定位提示工具）成为可能。jQuery 就是实现这类功能最受欢迎的 JavaScript 库。

> In contrast, modern JavaScript shifted the focus from the server to the client. The most extreme version of it: A user visits a URL and requests one *minimal HTML file* and *one associated JavaScript file*. After some network delay, the user sees the *by JavaScript rendered HTML* in the browser and starts to interact with it. Every additional page transition *wouldn't* request more files from the web server, but would use the initially requested JavaScript to render the new page. Also every additional interaction by the user is handled on the client too. In this modern version, the server delivers mainly JavaScript across the wire with one minimal HTML file. The HTML file then executes all the linked JavaScript on the client-side to render the entire application with HTML and JavaScript for its interactions.

相反，现代的 JavaScript 将焦点从服务器转移到了客户端。举个最极端的例子：用户访问 URL 并请求一个 *最小的 HTML 文件* 和一个 *相关的 JavaScript 文件* 。经过一段网络延迟后，用户会在浏览器中看到 *由 JavaScript 渲染的 HTML* 并开始与其进行交互。每一次额外的页面跳转都 *不会* 从 Web 服务器请求更多的文件，而是会使用最初请求的 JavaScript 来渲染新的页面。同样，用户的其他所有交互也都在客户端上进行处理。在现代的版本中，服务器主要通过一个小体积的 HTML 文件跨线提供 JavaScript。然后，这个 HTML 文件在客户端执行所有相关的 JavaScript，以使用 HTML 和 JavaScript 渲染整个应用程序进行交互。

> React, among the other SPA solutions, makes this possible. Essentially a SPA is one bulk of JavaScript, which is neatly organized in folders and files, to create a whole application whereas the SPA framework gives you all the tools to architect it. This JavaScript focused application is delivered once over the network to your browser when a user visits the URL for your web application. From there, React, or any other SPA framework, takes over for rendering everything in the browser and for dealing with user interactions.

在其他 SPA 解决方案中，React 使这其成为可能。本质上来说，SPA 是创建整个应用程序的一个 JavaScript 主体，这些 JavaScript 整齐的组织在文件夹和文件中，而 SPA 框架提供了构建它的所有工具。当用户访问您的 Web 应用的 URL 时，JavaScript 应用程序会通过网络一次性发送至您的浏览器。从此，React 和其他 SPA 框架就接管了浏览器中渲染和处理用户交互的任务。

> With the rise of React, the concept of components became popular. Every component defines its look and feel with HTML, CSS and JavaScript. Once a component is defined, it can be used in a hierarchy of components for creating an entire application. Even though React has a strong focus on components as a library, the surrounding ecosystem makes it a flexible framework. React has a slim API, a stable yet thriving ecosystem, and a welcoming community. We are happy to welcome you :-)

随着 React 的兴起，组件的概念开始流行起来。每个组件都使用 HTML、CSS 和 JavaScript 定义外观。一旦定义好一个组件后，它就可以用在组件结构中来创建整个应用。尽管 React 作为一个库非常重视组件的概念，但周围的生态系统却使其成为了一个非常灵活的框架。React 拥有便捷的 API，还拥有稳定而繁荣的生态系统以及热情活跃的社区。我们非常欢迎您的加入 :-)

> ### Exercises

### 练习

> * Read more about [why I moved from Angular to React](https://www.robinwieruch.de/reasons-why-i-moved-from-angular-to-react/).

* 阅读关于[为什么我从 Angular 换成 React](https://www.robinwieruch.de/reasons-why-i-moved-from-angular-to-react/)

> * Read more about [how to learn a framework](https://www.robinwieruch.de/how-to-learn-framework/).

* 阅读关于[如何学习一个框架](https://www.robinwieruch.de/how-to-learn-framework/)

> * Read more about [how to learn React](https://www.robinwieruch.de/learn-react-js).

* 阅读关于[如何学习 React](https://www.robinwieruch.de/learn-react-js)

> * Read more about [why frameworks matter](https://www.robinwieruch.de/why-frameworks-matter).

* 阅读关于[为什么框架很重要](https://www.robinwieruch.de/why-frameworks-matter)

> * Scan through [JavaScript fundamentals needed for React](https://www.robinwieruch.de/javascript-fundamentals-react-requirements) -- without worrying too much about the React -- to test yourself about several JavaScript features used in React.

* 浏览[React 所需的 JavaScript 基础](https://www.robinwieruch.de/javascript-fundamentals-react-requirements) -- 不用太关注 React，看一下自己对 React 中用到的 JavaScript 特性了解多少
