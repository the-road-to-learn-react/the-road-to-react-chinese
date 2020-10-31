## 基本要求

阅读本书之前，你需要熟悉 Web 开发的基础知识，例如怎样使用 HTML，CSS 和 JavaScript。这也有助于理解 [APIs](https://www.robinwieruch.de/what-is-an-api-javascript/)，因为它们将会覆盖全文。

### 编辑器和终端

我提供了[设置指南](https://www.robinwieruch.de/developer-setup/)来帮助进行一般的 Web 开发。在这次学习过程中，你需要一个文本编辑器（例如 Sublime Text）和命令行工具（例如 iTerm）或者 IDE（例如 Visual Studio Code）。对于初学者我推荐使用 Visual Studio Code （又名 VS Code），它为高级编辑器提供了集成的命令行工具，是一种多合一的解决方案，并且它是 Web 开发人员的热门选择。

在整个学习过程中，我将使用术语*命令行*，这跟术语*命令行工具*，*终端*和*集成终端*同义。术语*编辑器*，*文本编辑器*和*IDE*同样适用，这取决于你决定使用哪种设置。

可选地，我推荐使用 GitHub 来管理本书中的练习项目，并且我提供了有关如何使用这些工具的[简短指南](https://www.robinwieruch.de/git-essential-commands/)。Github 极为出色的版本控制，让你在犯了错误或者只是想采用一种更直接的方式来执行操作时，可以看到你所做的更改。这也是以后与他人共享代码的好方法。

如果你不想在本地计算机上设置编辑器/终端组合或 IDE，那么在线编辑器 [CodeSandbox](https://codesandbox.io/) 也是可行的选择。虽然 CodeSandbox 是在线共享代码的出色工具，但本机设置是学习用不同方法创建 Web 应用的更好工具。另外，如果你想专业地开发应用程序，则本地设置是必需的。

### Node 和 NPM

在开始之前，我们需要安装 [node and npm](https://nodejs.org/en/)。这两个工具都是用来管理你将会用到的各种库（node 包）的。这些 node 包可以是库，也可以是整个框架。我们将通过 npm（node 包管理器）安装外部 node 包。

你可以使用 `node --version` 命令在命令行中验证 node 和 npm 版本。如果你在终端中没有收到表示安装了哪个版本的输出，则需要安装 node 和 npm：

{title="Command Line",lang="text"}

```
node --version
*vXX.YY.ZZ
npm --version
*vXX.YY.ZZ
```

如果你已经安装了 Node 和 npm，请确保你安装的是最新版本。如果你不熟悉 npm 或需要复习，我创建的 [npm 速成教程](https://www.robinwieruch.de/npm-crash-course)将带你快速入门。
