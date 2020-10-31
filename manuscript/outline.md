# 大纲

我们的 React 之路已经走到了尽头，希望你喜欢这本书，并希望你能从中获得一些帮助。如果你喜欢这本书，请与有兴趣了解更多 React 的朋友分享。此外，在[Amazon](https://amzn.to/2JHlP42) 或 [Goodreads](https://www.goodreads.com/book/show/37503118-the-road-to-learn-react)上发表评论，将有助于我根据你的反馈提供创作的内容。

从这里开始，我建议你在参与另一本书、课程或教程之前，先扩展应用来创建自己的 React 项目。试用一周，通过部署将其带入生产，并联系我或其他人来展示它。我总是有兴趣看到我的读者创造的东西，并了解我如何帮助他们一起。

如果你正在为你的应用寻找扩展，我推荐你在掌握基础知识后的几种学习途径：

* **连接数据库或鉴权：** 不断壮大的 React 应用最终会需要持久性的数据。这些数据应该存储在数据库中，这样可以在浏览器会话后保持数据的完整性，以便与不同的用户共享。Firebase 是引入数据库的最简单的方法之一，而无需编写后台应用程序。在我那本名为["Firebase 学习之道"](https://www.roadtofirebase.com/)的书中，你会发现一个关于如何在React中使用Firebase认证和数据库的分步指南。

* **连接后端：** React 处理前端应用程序，到目前为止，我们只从第三方后端的API请求数据。你也可以用连接到数据库并管理认证/授权的后端应用程序引入API。在["GraphQL 学习之道"](https://www.roadtographql.com/)中，我教你如何使用 GraphQL 进行客户端-服务器通信。你将学习到如何将你的后端连接到数据库，如何管理用户会话，以及如何通过 GraphQL API 从前端到后端应用程序的对话。

* **状态管理：** 你在这次学习经历中专门使用 React 来管理本地组件状态。对于大多数应用来说，这是一个好的开始，但React也有外部状态管理解决方案。我在["Redux 学习之道"](https://www.roadtoredux.com/)一书中探讨了最流行的一种。

* **使用 Webpack 和 Babel 工具：** 我们在本书中使用 *create-react-app* 来设置应用程序。在某些时候，你可能会想学习它的工具，在没有 *create-react-app* 的情况下创建项目。我推荐使用 [Webpack](https://www.robinwieruch.de/minimal-react-webpack-babel-setup/) 进行最低限度的设置，之后你可以应用更多的工具。

* **代码组织：** 回想一下关于代码组织的章节，并应用这些变化，如果你还没有。这将有助于将你的组件组织成结构化的文件和文件夹，它将帮助你理解代码拆分、可重用性、可维护性和模块API设计的原则。你的应用程序最终会成长并需要结构化的模块，所以最好现在就开始。

* **测试:** 我们只对测试进行了简单的介绍。如果你对测试 Web 应用程序不熟悉，[深入了解单元测试和集成测试](https://www.robinwieruch.de/react-testing-tutorial)，特别是 React 应用程序。[Cypress](https://www.robinwieruch.de/react-testing-cypress) 是 React 中端到端测试的一个有用的探索工具。

* **类型检查：** 前面我们在 React 中使用了 TypeScript，这是一个好的防止 bug 和提高开发者体验的实践方式。深入研究这个话题，让你的 JavaScript 应用更加强大。也许你最终会一直使用 TypeScript 而不是 JavaScript。

* **UI组件：** 很多初学者在项目中过早引入 Bootstrap 等 UI 组件库。在 React 中使用标准 HTML 元素的下拉、复选框或对话框是比较实用的。这些组件大多会管理自己的本地状态。复选框必须知道它是选中还是不选中，所以你应该把它们作为受控组件来实现。在你涵盖了这些关键的UI组件的基本实现之后，引入UI组件库应该会更容易。

* **路由：** 你可以用 [react-router](https://github.com/ReactTraining/react-router) 为你的应用程序添加路由。在我们创建的应用程序中，只有一个页面，但这个页面会增长。React Router可以帮助管理跨多个 URL 的多个页面。当你将路由引入到你的应用程序中时，不会向 Web 服务器发出下一个页面的请求。路由器会在客户端处理这个问题。

* **React Native:** [React Native](https://facebook.github.io/react-native/) 将你的应用带到 iOS 和 Android 等移动设备上。一旦你掌握了 React，React Native 的学习曲线应该不会那么陡峭，因为它们有着相同的原理。与移动设备的唯一区别是布局组件、构建工具和你的移动设备的 API。

我邀请你访问我的[网站](https://www.robinwieruch.de)，寻找更多关于网络开发和软件工程的有趣话题。你也可以[订阅我的电报](https://www.getrevue.co/profile/rwieruch)或[推特](https://twitter.com/rwieruch)来获取最新的文章、书籍和课程。

感谢阅读 *《React 学习之道》*。

Regards,

Robin Wieruch
