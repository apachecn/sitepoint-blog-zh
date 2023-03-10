# 什么是 Node，我应该什么时候使用它？

> 原文：<https://www.sitepoint.com/an-introduction-to-node-js/>

当那篇文章发表后，用下面的链接版本替换“构建跨平台桌面应用程序”: [构建跨平台桌面应用程序](https://www.sitepoint.com/build-native-desktop-gif-searcher-app-using-nodegui) ->

你听说过 Node.js，但是不确定它是什么，或者它适合你的开发工作流程。或者你可能已经听到人们对 Node 的赞美，现在你想知道这是否是你需要学习的东西。也许您熟悉另一种后端技术，并希望了解 Node 的不同之处。

如果这听起来像你，那么继续读下去。在本文中，我将对 Node.js 及其主要范例做一个初学者友好的、高水平的介绍。我将研究 Node 的主要用例，以及 Node 环境的当前状态，并在此过程中为您提供广泛的出发点(供进一步阅读)。

请注意，在整篇文章中，我将交替使用“Node”和“Node.js”。

## Node.js 是什么？

网上有很多定义。让我们来看几个比较流行的。这是[项目主页要说的话](https://nodejs.org/en/):

> Node.js 是基于 Chrome 的 V8 JavaScript 引擎构建的 JavaScript 运行时。

这就是[堆栈溢出所能提供的](https://stackoverflow.com/tags/node.js/info):

> Node.js 是一个基于事件的、非阻塞的异步 I/O 运行时，它使用 Google 的 V8 JavaScript 引擎和 libuv 库。

嗯，“基于事件的”、“非阻塞的”、“异步 I/O”——一口气消化掉太多了。因此，让我们从一个不同的角度来看这个问题，从关注两个描述都提到的另一个细节开始——V8 JavaScript 引擎。

### Node 建立在 Google Chrome 的 V8 JavaScript 引擎上

V8 引擎是开源的 JavaScript 引擎，运行在谷歌 Chrome 和其他基于 Chrome 的网络浏览器上，包括 Brave、Opera 和 Vivaldi。它的设计考虑了性能，负责将 JavaScript 直接编译成您的计算机可以执行的本机代码。

然而，当我们说 Node 构建在 V8 引擎上时，我们并不意味着 Node 程序是在浏览器中执行的。他们不是。相反，Node 的创建者( [Ryan Dahl](https://en.wikipedia.org/wiki/Ryan_Dahl) )采用了 V8 引擎，并用各种功能对其进行了增强，例如一个[文件系统 API](https://www.sitepoint.com/useful-built-in-node-js-apis/) ，一个 HTTP 库，以及许多与操作系统相关的实用程序方法。

这意味着 Node.js 是一个我们可以用来在我们的计算机上执行 JavaScript 的程序。换句话说，它是一个 JavaScript 运行时。

## 如何安装 Node.js？

在下一节中，我们将安装 Node 并编写几个简单的程序。我们还将看看 [npm](https://www.npmjs.com/) ，一个与 Node 捆绑在一起的包管理器。

### 节点二进制文件与版本管理器

许多网站会推荐你去官方的节点下载页面获取你的系统的节点二进制文件。虽然这可行，但我建议您使用版本管理器。这是一个程序，可以让你安装多个版本的节点，并在它们之间随意切换。使用版本管理器有很多好处。例如，它消除了将 Node 与 npm 一起使用时的潜在权限问题，并允许您在每个项目的基础上设置节点版本。

如果你喜欢使用版本管理器，请参考我们的快速提示:[使用 nvm](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) 安装 Node.js 的多个版本。否则，从上面的链接中为您的系统获取正确的二进制文件并安装它们。

### “你好，世界！”Node.js 方式

您可以通过打开终端并键入`node -v`来检查 Node 是否安装在您的系统上。如果一切顺利，您应该会看到类似于`v12.14.1`的显示。这是目前的 <abbr title="long-term support">LTS</abbr> 版本在写作的时候。

接下来，创建一个新文件`hello.js`，并复制以下代码:

```
console.log("Hello, World!"); 
```

这使用节点的[内置控制台模块](https://nodejs.org/api/console.html#console_console)在终端窗口中显示消息。要运行该示例，请输入以下命令:

```
node hello.js 
```

如果 Node.js 配置正确，“Hello，World！”将会显示。

### Node.js 对现代 JavaScript 有很好的支持

从这张[兼容性表](https://node.green/)上可以看出，Node 对 ECMAScript 2015 (ES6)以及更高版本的支持非常出色。由于您只针对一个运行时(V8 引擎的特定版本)，这意味着您可以使用最新、最现代的语法编写 JavaScript。这也意味着您通常不必担心兼容性问题——如果您正在编写可以在不同浏览器中运行的 JavaScript，您就会担心。

为了说明这一点，下面是第二个程序，它利用了几个现代 JavaScript 特性，比如带标签的[模板文字](https://www.sitepoint.com/understanding-ecmascript-6-template-strings/)、[对象析构](https://www.sitepoint.com/es6-destructuring-assignment/)和[array . prototype . flat map()](https://thecodebarbarian.com/whats-new-in-es2019-flat-flatmap-catch.html#arrayflat-and-arrayflatmap):

```
function upcase(strings, ...values) {
  return values.map(name => name[0].toUpperCase() + name.slice(1))
    .join(' ') + strings[2];
}

const person = {
  first: 'brendan',
  last: 'eich',
  age: 56,
  position: 'CEO of Brave Software',
};

const { first, last } = person;
const emoticon = [ ['┌', '('], ['˘', '⌣'], ['˘', ')', 'ʃ'] ];

console.log(
  upcase`${first}  ${last} is the creator of JavaScript! ` + emoticon.flat().join('')
); 
```

将这段代码保存到一个名为`index.js`的文件中，并使用命令`node index.js`从您的终端运行它。您应该看到`Brendan Eich is the creator of JavaScript! ┌(˘⌣˘)ʃ`输出到终端。

## 介绍 JavaScript 包管理器 npm

正如我前面提到的，Node 附带了一个名为 npm 的包管理器。要检查您的系统上安装了哪个版本，请键入`npm -v`。

除了作为 JavaScript 的包管理器，npm 还是世界上最大的软件注册中心。有超过 1，000，000 个 JavaScript 代码包可供下载，每周下载数十亿次。让我们快速地看一下我们如何使用 npm 来安装一个包。

### 全局安装软件包

打开您的终端并键入以下内容:

```
npm install -g jshint 
```

这将在你的系统上全局安装 [jshint 包](https://www.npmjs.com/package/jshint)。我们可以用它来 lint 前一个例子中的`index.js`文件:

```
jshint index.js 
```

您现在应该会看到许多与 ES6 相关的错误。如果你想修复它们，将`/* jshint esversion: 6 */`添加到`index.js`文件的顶部，重新运行命令，林挺应该会通过。

如果你想重温一下林挺，请看[JavaScript 林挺工具对比](https://www.sitepoint.com/comparison-javascript-linting-tools/)。

### 在本地安装软件包

我们也可以在本地将包安装到项目中，而不是在我们的系统中全局安装。创建一个`test`文件夹，并在该目录下打开一个终端。接下来输入这个:

```
npm init -y 
```

这将在同一文件夹中创建并自动填充一个`package.json`文件。接下来，使用 npm 安装 [lodash 包](https://www.npmjs.com/package/lodash)，并将其保存为项目依赖项:

```
npm install lodash --save 
```

创建一个名为`test.js`的文件，并添加以下内容:

```
const _ = require('lodash');

const arr = [0, 1, false, 2, '', 3];
console.log(_.compact(arr)); 
```

最后，使用`node test.js`运行脚本。您应该看到`[ 1, 2, 3 ]`输出到终端。

### 使用`package.json`文件

如果您查看`test`目录的内容，您会注意到一个名为`node_modules`的文件夹。这是 npm 保存 lodash 和任何 lodash 依赖的库的地方。`node_modules`文件夹不应该被签入到版本控制中，事实上，可以在任何时候通过从项目的根目录中运行`npm install`来重新创建。

如果您打开`package.json`文件，您会看到 lodash 列在`dependencies`字段下。通过以这种方式指定项目的依赖项，您允许任何地方的任何开发人员克隆您的项目，并使用 npm 安装它需要运行的任何包。

如果您想了解更多关于 npm 的信息，请务必阅读我们的文章[NPM 初学者指南——节点包管理器](https://www.sitepoint.com/beginners-guide-node-package-manager/)。

## Node.js 是用来做什么的？

既然我们知道了 Node 和 npm 是什么以及如何安装它们，我们可以将注意力转向它们的第一个常见用途:安装(通过 npm)和运行(通过 Node)各种构建工具——旨在自动化开发现代 JavaScript 应用程序的过程。

这些构建工具有各种形状和大小，在现代 JavaScript 环境中，你不会走得太远而不碰到它们。它们可以用于任何事情，从将 JavaScript 文件和依赖项捆绑到静态资产，到运行测试，或者自动代码林挺和样式检查。

我们在 SitePoint 上有大量涵盖构建工具的文章。以下是我最喜欢的一小段:

*   [web pack 初学者指南](https://www.sitepoint.com/webpack-beginner-guide/)
*   [启动并运行 ESLint——可插拔的 JavaScript Linter](https://www.sitepoint.com/up-and-running-with-eslint-the-pluggable-javascript-linter/)
*   [gulp . js 简介](https://www.sitepoint.com/introduction-gulp-js/)
*   使用 Mocha 和 Chai 对您的 JavaScript 进行单元测试

如果你想开始用任何现代 JavaScript 框架开发应用程序(例如，React 或 Angular)，你应该具备 Node 和 npm 的工作知识(或者可能是 [Yarn](https://www.sitepoint.com/yarn-vs-npm/) )。这并不是因为您需要一个节点后端来运行这些框架。你不知道。相反，这是因为这些框架(以及许多许多相关的包)都可以通过 npm 获得，并依赖于 Node 来创建一个合理的开发环境，它们可以在其中运行。

如果你有兴趣了解 Node 在现代 JavaScript 应用中扮演的角色，请阅读[现代 JavaScript 应用剖析](https://www.sitepoint.com/anatomy-of-a-modern-javascript-application/)。

## Node.js 允许我们在服务器上运行 JavaScript

接下来我们来看 Node.js 最大的用例之一——在服务器上运行 JavaScript。这不是一个新概念，早在 1994 年 Netscape 就首次尝试了。然而，Node.js 是第一个获得真正吸引力的实现，与传统语言相比，它提供了一些独特的好处。Node 现在在许多知名公司的技术堆栈中扮演着关键角色。让我们来看看这些好处是什么。

### Node.js 执行模型

简单地说，当您连接到传统服务器(如 Apache)时，它会产生一个新线程来处理请求。在 PHP 或 Ruby 这样的语言中，任何后续的 I/O 操作(例如，与数据库交互)都会阻止代码的执行，直到操作完成。也就是说，服务器必须等待数据库查找完成，然后才能继续处理结果。如果在此期间有新的请求进来，服务器将产生新的线程来处理它们。这可能是低效的，因为大量的线程会导致系统变得缓慢，在最坏的情况下，会导致站点宕机。支持更多连接的最常见方法是添加更多服务器。

然而，Node.js 是单线程的。它也是**事件驱动的**，这意味着 Node 中发生的一切都是对事件的反应。例如，当一个新的请求进来时(一种事件)，服务器将开始处理它。如果遇到阻塞的 I/O 操作，它不会等待这个操作完成，而是在继续处理下一个事件之前注册一个回调。当 I/O 操作完成时(另一种事件)，服务器将执行回调并继续处理原始请求。在幕后，Node 使用 [libuv](https://github.com/libuv/libuv) 库来实现这种异步(即非阻塞)行为。

Node 的执行模型给服务器带来的开销非常小，因此它能够处理大量的并发连接。扩展节点应用程序的传统方法是克隆它，并让克隆的实例分担工作负载。Node.js 甚至有一个内置模块来帮助你在单个服务器上实现克隆策略。

下图描述了节点的执行模型:

![Node’s execution model](img/d1d3d8a27e186c14cac6d47a7df3c435.png)

*资料来源:Christian Maderazo、James Santos 教授对 Node.js 的介绍*

或者，如果你喜欢视频，看看这个精彩的演讲:[到底什么是事件循环？](https://www.youtube.com/watch?v=8aGhZQkoFbQ)它不是特定于节点的，但是很好地解释了这个概念。

### 有什么不好的地方吗？

Node 在单线程中运行的事实确实带来了一些限制。例如，应该避免阻塞 I/O 调用，应该将 CPU 密集型操作交给工作线程，并且应该始终正确处理错误，以免整个进程崩溃。

一些开发人员也不喜欢 JavaScript 强加的基于回调的编码风格(以至于有一个网站专门讨论编写异步 JavaScript 的恐怖之处)。但是随着 native Promises 的到来，紧随其后的是 async await，[现代 JavaScript](https://www.sitepoint.com/flow-control-callbacks-promises-async-await/) 中的流控制变得比以往任何时候都更容易。

### “你好，世界！”—服务器版本

我们来快速看一个“你好，世界！”HTTP 服务器示例:

```
const http = require('http');

http.createServer((request, response) => {
  response.writeHead(200);
  response.end('Hello, World!');
}).listen(3000);

console.log('Server running on http://localhost:3000'); 
```

要运行它，将代码复制到名为`hello-world-server.js`的文件中，并使用`node hello-world-server.js`运行它。打开浏览器，导航到 [http://localhost:3000](http://localhost:3000) 看到“你好，世界！”显示在浏览器中。

现在让我们看一下代码。

我们从要求节点的本机 [HTTP 模块](https://nodejs.org/api/http.html)开始。然后，我们使用它的 [createServer](https://nodejs.org/api/http.html#http_http_createserver_options_requestlistener) 方法创建一个新的 web 服务器对象，并向其传递一个匿名函数。每次与服务器建立新的连接时，都会调用这个函数。

匿名函数用两个参数调用(`request`和`response`)。这些包含来自用户的请求和响应，我们用它们发回一个 200 HTTP 状态代码，以及我们的“Hello World！”消息。

最后，我们告诉服务器侦听端口 3000 上的传入请求，并向终端输出一条消息，让我们知道它正在运行。

显然，在 Node 中创建一个简单的服务器还有很多事情要做(例如，正确处理错误很重要)，所以我建议你[查看文档](https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction/)或者[查阅我们的教程](https://www.sitepoint.com/build-a-simple-web-server-with-node-js/)，如果你想了解更多信息。

## Node.js 适合什么样的 app？

Node 特别适合构建需要某种形式的实时交互或协作的应用程序——例如，聊天网站，或者像 [CodeShare](https://codeshare.io/) 这样的应用程序，在那里你可以观看别人正在实时编辑的文档。它也很适合构建 API

尽管如此，并不是每个人都会构建下一个 Trello 或下一个 Google Docs，事实上，没有理由不能使用 Node 来构建一个简单的 CRUD 应用程序。然而，如果你遵循这条路线，你很快就会发现 Node 是非常基本的，你构建和构造应用程序的方式在很大程度上取决于你。有各种各样的框架可以用来减少样板文件，其中 [Express](http://expressjs.com) 已经成为领跑者。然而，即使像 Express 这样的解决方案也是最小的，这意味着如果您想做一些稍微不同寻常的事情，您将需要从 npm 中引入额外的模块。这与 Rails 或 Laravel 等框架形成了鲜明的对比，这些框架提供了大量现成的功能。

如果你想看看如何构建一个基本的、更传统的应用程序，请查看我们的教程[如何构建 Node.js MVC 应用程序](https://www.sitepoint.com/node-js-mvc-application/)。

## Node.js 有什么优势？

除了速度和可伸缩性，在 web 服务器上使用 JavaScript 的一个经常被吹捧的优势——以及在浏览器中——是你的大脑不再需要切换模式。你可以用同一种语言做任何事情，作为一名开发人员，这会让你更有效率(也希望更快乐)。例如，您可以轻松地在服务器和客户端之间共享代码。

Node 的另一大优势是它会说 JSON。JSON 可能是 Web 上最重要的数据交换格式，也是与对象数据库(比如 MongoDB)交互的通用语言。JSON 非常适合 JavaScript 程序使用，这意味着当您使用 Node 时，数据可以在各层之间流畅地流动，而不需要重新格式化。从浏览器到服务器到数据库，你可以有一个语法。

最后，JavaScript 无处不在:我们大多数人都熟悉 JavaScript，或者曾经使用过它。这意味着过渡到节点开发可能比过渡到其他服务器端语言更容易。引用 Craig Buckler 在他的 [Node vs PHP Smackdown](https://www.sitepoint.com/sitepoint-smackdown-php-vs-node-js/) 中的话，JavaScript 可能仍然是[世界上最容易被误解的语言](http://www.crockford.com/javascript/javascript.html)——但是，一旦这些概念被点击，它会让其他语言看起来很麻烦。

## 节点的其他用途

它不会在服务器上停止。Node.js 还有许多其他令人兴奋的不同用法！

例如[它可以作为一种脚本语言](https://www.sitepoint.com/shell-scripts-javascript/)在你的电脑上自动执行重复或容易出错的任务。它也可以用来[编写自己的命令行工具](https://www.sitepoint.com/javascript-command-line-interface-cli-node-js/)，比如这个[自耕农式的生成器](https://www.sitepoint.com/scaffolding-tool-caporal-js/)来搭建新项目。

Node.js 也可以用来构建跨平台桌面应用

## 结论

JavaScript 无处不在，Node 是一个广阔的主题。尽管如此，我希望在本文中，我已经向您提供了对 Node.js 及其主要范例的初学者友好的、高水平的看法，这是我在开始时承诺的。我也希望当你重新阅读我们之前看到的定义时，事情会变得更有意义。

> Node.js 是一个基于事件的、非阻塞的异步 I/O 运行时，它使用 Google 的 V8 JavaScript 引擎和 libuv 库。

如果你有任何问题或意见，请随时在 Twitter 上联系我。

## 分享这篇文章