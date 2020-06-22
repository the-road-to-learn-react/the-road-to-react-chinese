> ## Deploy to Firebase

## 部署到 Firebase

> After we've built a full-fledged application in React, the final step is deployment. It is the tipping point of getting your ideas into the world, from learning how to code to producing applications. We will use Firebase Hosting for deployment.

在我们用 React 构建了一个完整的应用之后，最后一步就是部署。这是一个让你的想法走向世界的转折点，从学习如何编码到制作应用。我们将使用 Firebase Hosting 进行部署。

> Firebase works for create-react-app, as well as most libraries and frameworks like Angular and Vue. First, install the Firebase CLI globally to the node modules:

Firebase 适用于 create-react-app，以及 Angular 和 Vue 等大多数库和框架。首先，将 Firebase CLI 安装到全局依赖中：

{title="Command Line",lang="javascript"}
~~~~~~~
npm install -g firebase-tools
~~~~~~~

> Using a global installation of the Firebase CLI lets us deploy applications without concern over project dependency. For any globally-installed node package, remember to update it to a newer version with the same command as often as you can:

使用全局安装的 Firebase CLI 可以让我们在部署应用程序时，不必担心项目依赖性。对于任何一个全局安装的节点包，记得尽可能频繁地使用相同的命令将其更新到更新的版本：

{title="Command Line",lang="javascript"}
~~~~~~~
npm install -g firebase-tools
~~~~~~~

> If you don't have a Firebase project yet, sign up for a [Firebase account](https://console.firebase.google.com/) and create a new project there. Then you can associate the Firebase CLI with the Firebase account (Google account):

如果你还没有 Firebase 项目，请注册一个 [Firebase 账户](https://console.firebase.google.com/)，并在那里创建一个新项目。然后你就可以将 Firebase CLI 与 Firebase 账户（Google 账户）关联起来：

{title="Command Line",lang="javascript"}
~~~~~~~
firebase login
~~~~~~~

> A URL will display in the command line that you can open in a browser, or the Firebase CLI opens it. Choose a Google account to create a Firebase project, and give Google the necessary permissions. Return to the command line to verify a successful login.

命令行中会显示一个 URL，你可以在浏览器中打开，或者使用 Firebase CLI 打开它。选择一个 Google 账户来创建 Firebase 项目，并给予 Google 必要的权限。返回命令行，验证登录成功。

> Next, move to the project's folder and execute the following command, which initializes a Firebase project for the Firebase hosting features:

接下来，移动到项目的文件夹中，并执行下面的命令，为 Firebase 托管功能初始化一个 Firebase 项目：

{title="Command Line",lang="javascript"}
~~~~~~~
firebase init
~~~~~~~

> Next, choose the Hosting option. If you're interested in using another tool next to Firebase Hosting, add other options:

接下来，选择主机选项。如果你对使用 Firebase Hosting 相关的其他工具感兴趣，可以添加其他选项：

{title="Command Line",lang="javascript"}
~~~~~~~
? Which Firebase CLI features do you want to set up for this folder? Press Space to select features, then Enter to confirm your choices.
 ? Database: Deploy Firebase Realtime Database Rules
 ? Firestore: Deploy rules and create indexes for Firestore
 ? Functions: Configure and deploy Cloud Functions
-> Hosting: Configure and deploy Firebase Hosting sites
 ? Storage: Deploy Cloud Storage security rules
~~~~~~~

> Google becomes aware of all Firebase projects associated with an account after login, and we can select one from the Firebase platform:

Google 在登录后会意识到所有与账户相关联的 Firebase 项目，我们可以从 Firebase 平台上选择一个项目：

{title="Command Line",lang="javascript"}
~~~~~~~
First, let's associate this project directory with a Firebase project.
You can create multiple project aliases by running firebase use --add,
but for now we'll just set up a default project.

? Select a default Firebase project for this directory:
-> my-react-project-abc123 (my-react-project)
i  Using project my-react-project-abc123 (my-react-project)
~~~~~~~

> There are a few other configuration steps to define. Instead of using the default *public/* folder, we want to use the *build/* folder from create-react-app. If you set up the bundling with a tool like Webpack yourself, you can choose the appropriate name for the build folder:

还有一些其他的配置步骤需要定义。我们不使用默认的 *public/* 文件夹，而是要使用 create-react-app 的 *build/* 文件夹。如果你自己用 Webpack 等工具进行打包，你可以为 build 文件夹选择合适的名字：

{title="Command Line",lang="javascript"}
~~~~~~~
? What do you want to use as your public directory? build
? Configure as a single-page app (rewrite all urls to /index.html)? Yes
? File public/index.html already exists. Overwrite? No
~~~~~~~

> The create-react-app application creates a *build/* folder after we perform the `npm run build` for the first time. The folder contains all the merged content from the *public/* folder and the *src/* folder. Since it is a single page application, we want to redirect the user to the *index.html* file, so the React router can handle client-side routing.

在我们第一次执行 `npm run build` 后，create-react-app 应用程序会创建一个 *build/* 文件夹，这个文件夹包含了所有来自 *public/* 文件夹和 *src/* 文件夹的合并内容。由于这是一个单页应用，我们希望将用户重定向到 *index.html* 文件，这样 React 路由器就可以处理客户端的路由：

> Now your Firebase initialization is complete. This step created a few configuration files for Firebase Hosting in your project's folder. You can read more about them in [Firebase's documentation](https://firebase.google.com/docs/hosting/full-config) for configuring redirects, a 404 page, or headers. Finally, deploy your React application with Firebase in the command line:

现在你的 Firebase 初始化已经完成了。这一步在你的项目文件夹中为 Firebase Hosting 创建了一些配置文件。你可以在 [Firebase 的文档](https://firebase.google.com/docs/hosting/full-config) 中阅读更多关于它们的内容，以配置重定向、404 页面或头文件。最后，在命令行中用 Firebase 部署你的 React 应用：

{title="Command Line",lang="javascript"}
~~~~~~~
firebase deploy
~~~~~~~

> After a successful deployment, you should see a similar output with your project's identifier:

在成功部署后，你应该会看到一个类似的输出与你的项目标识符：

{title="Command Line",lang="javascript"}
~~~~~~~
Project Console: https://console.firebase.google.com/project/my-react-project-abc123/overview
Hosting URL: https://my-react-project-abc123.firebaseapp.com
~~~~~~~

> Visit both pages to observe the results. The first link navigates to your Firebase project's dashboard, where you'll see a new panel for the Firebase Hosting. The second link navigates to your deployed React application.

访问这两个页面来观察结果，第一个链接导航到你的 Firebase 项目的仪表板，在那里你会看到一个新的 Firebase 托管的面板，第二个链接导航到你部署的 React 应用。

> If you see a blank page for your deployed React application, make sure the `public` key/value pair in the *firebase.json* is set to `build`, or whichever name you chose for this folder. Second, verify you've run the build script for your React app with `npm run build`. Finally, check out the [official troubleshoot area for deploying create-react-app applications to Firebase](https://create-react-app.dev/docs/deployment). Try another deployment with `firebase deploy`.

如果你看到你部署的 React 应用有一个空白页，请确保 *firebase.json* 中的 `public` 键/值对设置为 `build`，或者你为这个文件夹选择的任何名称。其次，确认你已经用 `npm run build` 为你的 React 应用运行了构建脚本。最后，查看[官方将 create-react-app 应用部署到 Firebase 的故障排除区域](https://create-react-app.dev/docs/deployment)。用 `firebase deploy` 再尝试一次部署。

### 练习

* 了解更多 [Firebase Hosting](https://firebase.google.com/docs/hosting/).
* [将您的域名关联到您的 Firebase 部署的应用程序](https://firebase.google.com/docs/hosting/custom-domain).
* 可选： 如果你想拥有一个托管的云服务器，请查看 [DigitalOcean](https://m.do.co/c/fb27c90322f3) 。这会消耗更多的精力，但它允许更多的控制，[我把我所有的网站、网络应用和后端 API 都托管在那里](https://www.robinwieruch.de/deploy-applications-digital-ocean/)。