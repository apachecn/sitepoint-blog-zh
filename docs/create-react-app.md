# 创建 React 应用程序:快速准备 React 项目

> 原文：<https://www.sitepoint.com/create-react-app/>

开始一个新的 React 项目并不是那么简单。您必须花时间配置正确的构建工具来建立本地开发环境、单元测试和生产构建，而不是直接进入代码并使您的应用程序变得生动。幸运的是，Create React 应用程序提供了帮助。

Create-React-App 是一个来自脸书的命令行工具，它允许你生成一个新的 React 项目，并使用预先配置的 webpack 构建进行开发。它早已成为 React 生态系统不可或缺的一部分，消除了在项目中维护复杂构建管道的需要，让您专注于应用程序本身。

## Create React App 是如何工作的？

Create React App 是一个独立的工具，可以使用 npm 或 Yarn 运行。

您可以使用 npm 生成并运行一个新项目，只需几个命令:

```
npx create-react-app new-app
cd new-app
npm start 
```

如果你喜欢纱线，你可以这样做:

```
yarn create react-app new-app
cd new app
yarn start 
```

创建 React 应用程序将设置以下项目结构:

```
new-app
├── .gitignore
├── node_modules
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
├── README.md
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   ├── reportWebVitals.js
│   └── setupTests.js
└── yarn.lock 
```

它还将向您的项目添加一个`react-scripts`包，其中将包含所有的配置和构建脚本。换句话说，你的项目依赖于`react-scripts`，而不是`create-react-app`本身。一旦安装完成，您就可以启动开发服务器并开始您的项目。

## 基本特征

### 本地开发服务器

您首先需要的是一个本地开发环境。运行`npm start`将启动一个 webpack 开发服务器，它带有一个观察器，一旦你改变了什么，它将自动重新加载应用程序。从 v4 开始，Create React App 支持 React 的快速刷新，作为热模块替换的替代方法。像它的前身一样，这允许我们在代码库做出更改后快速刷新应用程序的部分内容，但也有一些新功能。快速重新加载将尝试仅重新加载应用程序的修改部分，保留功能组件和挂钩的状态，并在纠正语法错误后自动重新加载应用程序。

您还可以通过 HTTPS 服务您的应用程序，在运行服务器之前将变量`HTTPS`设置为`true`。

生成的应用程序将内置许多功能。

### ES6 和 ES7

该应用自带 Babel 预置—[Babel-preset-react-app](https://github.com/facebookincubator/create-react-app/tree/master/packages/babel-preset-react-app)—支持一组 ES6 和 ES7 特性。一些受支持的功能包括:

*   异步/等待
*   对象静止/扩散属性
*   动态导入()
*   类字段和静态属性

请注意，Create React App 没有为运行时特性提供任何 polyfills，因此如果您需要其中的任何一个，您需要自己包含它们。

### 资产导入

您可以从 JavaScript 模块导入 CSS 文件、图像或字体，这些模块允许您捆绑应用程序中使用的文件。应用程序构建完成后，Create React App 会将这些文件复制到构建文件夹中。以下是导入图像的示例:

```
import image from './image.png';

console.log(image); // image will contain the public URL of the image 
```

您也可以在 CSS 中使用此功能:

```
.image {
  background-image: url(./image.png);
} 
```

### 式样

如前所述，Create React App 允许您通过导入所需的 CSS 文件来添加样式。当构建应用程序时，所有的 CSS 文件将被连接成一个包，并添加到构建文件夹中。

Create React App 还支持 [CSS 模块](https://github.com/css-modules/css-modules)。按照惯例，名为`*.module.css`的文件被视为 CSS 模块。这种技术允许我们避免 CSS 选择器的冲突，因为 Create React App 将在生成的 CSS 文件中创建唯一的类选择器。

或者，如果你喜欢使用 CSS 预处理程序，你可以导入 [Sass](https://sass-lang.com/) `.scss`文件。然而，您需要单独安装`node-sass`包。

此外，Create React App 提供了一种通过在 CSS 文件的任意位置添加`@import-normalize;`来添加 [CSS 重置](https://cssreset.com/what-is-a-css-reset/)的方法。样式还会经过后处理，后处理会缩小样式，使用 Autoprefixer 添加供应商前缀，并聚合填充不支持的功能，如`all`特性、自定义特性和媒体查询范围。

### 运行单元测试

执行`npm test`将使用 Jest 运行测试，并启动一个观察器，以便在您更改某些内容时重新运行测试:

```
 PASS  src/App.test.js
  ✓ renders learn react link (19 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.995 s
Ran all test suites.

Watch Usage
 › Press f to run only failed tests.
 › Press o to only run tests related to changed files.
 › Press q to quit watch mode.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press Enter to trigger a test run. 
```

Jest 是脸书开发的一款测试跑步器，作为摩卡或 Karma 的替代品。它在一个节点环境而不是真实的浏览器中运行测试，但是使用 [jsdom](https://github.com/jsdom/jsdom) 提供了一些特定于浏览器的全局变量。

Jest 还与您的版本控制系统集成在一起，默认情况下，它只会对自您上次提交以来发生更改的文件运行测试。有关这方面的更多信息，请参考“[如何使用 Jest](https://www.sitepoint.com/test-react-components-jest/) 测试 React 组件”。

### 埃斯林特

在开发过程中，您的代码也将通过 [ESLint](https://www.sitepoint.com/up-and-running-with-eslint-the-pluggable-javascript-linter/) 运行，这是一个静态代码分析器，它将帮助您在开发过程中发现错误。

### 创建生产捆绑包

当您最终有了要部署的东西时，您可以使用`npm run build`创建一个产品包。这将生成应用程序的优化版本，准备部署到您的环境中。生成的工件将被放置在构建文件夹中:

```
build
├── asset-manifest.json
├── favicon.ico
├── index.html
├── logo192.png
├── logo512.png
├── manifest.json
├── robots.txt
└── static
    ├── css
    │   ├── main.ab7136cd.chunk.css
    │   └── main.ab7136cd.chunk.css.map
    ├── js
    │   ├── 2.8daf1b57.chunk.js
    │   ├── 2.8daf1b57.chunk.js.LICENSE.txt
    │   ├── 2.8daf1b57.chunk.js.map
    │   ├── 3.d75458f9.chunk.js
    │   ├── 3.d75458f9.chunk.js.map
    │   ├── main.1239da4e.chunk.js
    │   ├── main.1239da4e.chunk.js.map
    │   ├── runtime-main.fb72bfda.js
    │   └── runtime-main.fb72bfda.js.map
    └── media
        └── logo.103b5fa1.svg 
```

### 部署

由于 Create React 应用程序的构建只包含静态文件，因此有不同的方法可以将它们部署到远程环境中。如果在 Node.js 环境中运行，可以使用 Node.js 服务器，或者使用不同的 web 服务器(如 NGINX)为应用程序提供服务。

官方文档中的[部署部分](https://create-react-app.dev/docs/deployment)概述了如何将应用部署到 Azure、AWS、Heroku、Netlify 和其他流行的托管环境。

## 发展特征

### 环境变量

您可以在构建时使用节点环境变量将值注入到代码中。Create React App 会自动寻找任何以`REACT_APP_`开头的环境变量，并使它们在全局`process.env`下可用。为了方便起见，这些变量可以放在一个`.env`文件中:

```
REACT_APP_BACKEND=http://my-api.com
REACT_APP_BACKEND_USER=root 
```

然后，您可以在代码中引用它们:

```
fetch({process.env.REACT_APP_SECRET_CODE}/endpoint) 
```

### 代理到后端

如果您的应用程序将使用远程后端，您可能需要能够在本地开发期间代理请求，以绕过 CORS。这可以通过向您的`package.json`文件添加一个代理字段来设置:

```
"proxy": "http://localhost:4000", 
```

这样，服务器会将任何不指向静态文件的请求转发到给定的地址。

### 代码拆分

Create React App 支持使用[动态导入()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#Dynamic_Imports)指令进行代码拆分。它不是返回模块导出的值，而是返回一个解析为这些值的承诺。因此，以这种方式导入的模块不会包含在最终的包中，而是构建到单独的文件中。这允许您减少最终包的大小，并按需加载内容。

### 类型脚本支持

您可以启用 [TypeScript](https://www.typescriptlang.org/) ，以便在生成新项目时获得静态类型分析的好处。这可以通过使用不同的非默认模板来生成项目来实现:

```
npx create-react-app my-app --template typescript 
```

您还可以向现有项目添加类型脚本支持，如[文档](https://create-react-app.dev/docs/adding-typescript/)中所述。

### 渐进式网络应用

同样，您可以添加渐进式 web 应用程序支持。您可以通过添加一个`src/service-worker.js`文件来使用服务人员。从 v4 开始，这将被注入到使用[工具箱](https://developers.google.com/web/tools/workbox)库的应用程序中。

要在新项目中启用服务人员，需要从自定义模板中生成服务人员:

```
npx create-react-app my-app --template cra-template-pwa

# or with TypeScript support
npx create-react-app my-app --template cra-template-pwa-typescript 
```

### 网络生命

Create React App 允许您测量应用程序的性能和响应能力。这是通过跟踪由[网络生命特征](https://web.dev/vitals/)定义并使用[网络生命特征库](https://github.com/GoogleChrome/web-vitals)测量的指标来完成的。这些指标包括测量加载性能的*最大内容绘制*、第一输入延迟和响应性的*累积布局移位*。

Create React App 提供了一个方便的功能来跟踪所有可用的指标:

```
// index.js

reportWebVitals(console.log); 
```

## 退出权

如果在某个时候，您觉得所提供的特性不再适合您的项目，您可以通过运行`npm run eject`来退出使用`react-scripts`。这将把 webpack 配置和构建脚本从`react-scripts`复制到您的项目中，并移除依赖性。之后，您可以随意修改配置。

作为一种选择，您也可以分叉`react-scripts`并用您需要的特性维护您的分支。如果您有许多项目要维护，这可能会更容易。

## 最后

如果你想开始一个新的 React 项目，不要再犹豫了。Create React App 将允许您快速启动应用程序，而不是编写另一个 webpack 配置。它还使得用一个命令(`npm install react-scripts@latest`)更新您的构建工具变得很容易——这通常是一项令人畏惧且耗时的任务。

Create React App 已经成为 React 生态系统不可或缺的一部分。无论您是用它来拼凑一个快速原型，还是为您的下一个主要项目搭建脚手架，它都将节省您许多小时的开发时间。

## 分享这篇文章