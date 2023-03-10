# 现代 JavaScript 应用程序的剖析

> 原文：<https://www.sitepoint.com/anatomy-of-a-modern-javascript-application/>

*本文精选于我们的书，* [JavaScript:最佳实践](https://www.sitepoint.com/premium/books/javascript-best-practice?ref_source=sitepoint&ref_medium=article_copy&ref_campaign=js-best-practice) *。跟上现代 JavaScript 快速变化的最佳实践。*

毫无疑问，JavaScript 生态系统变化很快。随着 ES2015(又名 ES6)的推出，不仅新工具和框架被快速引入和开发，语言本身也经历了巨大的变化。可以理解的是，现在已经有很多文章抱怨学习现代 JavaScript 开发有多么困难。

在本文中，我将向您介绍现代 JavaScript。我们将看看该语言的最新发展，并对当前用于编写前端 web 应用程序的工具和技术进行概述。如果您刚刚开始学习这种语言，或者您已经几年没有接触过这种语言，并且想知道您曾经熟悉的 JavaScript 发生了什么变化，那么这篇文章就是为您准备的。

![A woman playing a life-size game of Operation; a metaphor for the many components of a modern JavaScript app"](img/2f2be93c7a744532a9ea668f34e2814b.png)

## 关于 Node.js 的说明

Node.js 是一个允许用 JavaScript 编写服务器端程序的运行时。有可能有全栈 JavaScript 应用程序，其中应用程序的前端和后端都用同一种语言编写。虽然本文关注的是客户端开发，但 Node.js 仍然扮演着重要的角色。

Node.js 的到来对 JavaScript 生态系统产生了重大影响，引入了 npm 包管理器，普及了 CommonJS 模块格式。开发人员开始构建更具创新性的工具，并开发新的方法来模糊浏览器、服务器和本机应用程序之间的界限。

## JavaScript ES2015+

2015 年，[ECMAScript](https://en.wikipedia.org/wiki/ECMAScript)——定义 JavaScript 语言的规范——的第六个版本以 [ES2015](http://www.ecma-international.org/ecma-262/6.0/) (仍常被称为 ES6)的名字发布。这个新版本对语言进行了大量的添加，使得构建雄心勃勃的 web 应用程序变得更加容易和可行。但是，ES2015 并没有停止改进；每年都会发布一个新版本。

### 声明变量

JavaScript 现在有两种额外的方法来声明变量: [let 和 const](https://www.sitepoint.com/how-to-declare-variables-javascript/) 。

`let`是`var`的继承者。尽管`var`仍然可用，但是`let`将变量的范围限制在声明它们的块(而不是函数)中，这减少了出错的空间:

```
// ES5
for (var i = 1; i < 5; i++) {
  console.log(i);
}
// <-- logs the numbers 1 to 4
console.log(i);
// <-- 5 (variable i still exists outside the loop)

// ES2015
for (let j = 1; j < 5; j++) {
  console.log(j);
}
console.log(j);
// <-- 'Uncaught ReferenceError: j is not defined' 
```

使用`const`允许你定义不能反弹到新值的变量。对于诸如字符串和数字之类的原始值，这将产生类似于常数的结果，因为一旦声明了值，就不能更改它:

```
const name = 'Bill';
name = 'Steve';
// <-- 'Uncaught TypeError: Assignment to constant variable.'

// Gotcha
const person = { name: 'Bill' };
person.name = 'Steve';
// person.name is now Steve.
// As we're not changing the object that person is bound to, JavaScript doesn't complain. 
```

### 箭头功能

[箭头函数](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/)为声明匿名函数(lambdas)提供了更简洁的语法，当主体函数只有一个表达式时，删除`function`关键字和`return`关键字。这可以让您以更好的方式编写函数式代码:

```
// ES5
var add = function(a, b) {
  return a + b;
}

// ES2015
const add = (a, b) => a + b; 
```

箭头函数的另一个重要特性是它们从定义它们的上下文中继承了`this`的值:

```
function Person(){
  this.age = 0;

  // ES5
  setInterval(function() {
    this.age++; // |this| refers to the global object
  }, 1000);

  // ES2015
  setInterval(() => {
    this.age++; // |this| properly refers to the person object
  }, 1000);
}

var p = new Person(); 
```

### 改进的类语法

如果你是面向对象编程的爱好者，你可能会喜欢在现有的基于原型的机制之上给语言添加[类。虽然它主要只是语法糖，但它为试图用原型模拟经典面向对象的开发人员提供了更清晰的语法。](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)

```
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
} 
```

### 承诺/异步功能

JavaScript 的异步本质长期以来一直是一个挑战；当处理像 Ajax 请求这样的事情时，任何重要的应用程序都有陷入回调地狱的风险。

幸运的是，ES2015 增加了对[承诺](https://www.sitepoint.com/overview-javascript-promises/)的原生支持。承诺表示在计算时不存在但以后可能可用的值，这使得异步函数调用的管理更易于管理，而不会陷入深度嵌套的回调。

ES2017 引入了[异步函数](https://www.sitepoint.com/simplifying-asynchronous-coding-async-functions/)(有时称为 async/await)，在这方面进行了改进，允许您将异步代码视为同步代码:

```
async function doAsyncOp () {
  var val = await asynchronousOperation();
  console.log(val);
  return val;
}; 
```

### 模块

ES2015 中添加的另一个突出功能是本机模块格式，使模块的定义和使用成为语言的一部分。加载模块以前只能以第三方库的形式提供。我们将在下一节更深入地研究模块。

还有一些特性我们不会在这里讨论，但是我们已经讨论了一些主要的区别，当你看现代 JavaScript 时，你可能会注意到这些区别。你可以在[巴别塔网站](https://babeljs.io/)上的 [Learn ES2015](https://babeljs.io/learn-es2015/) 页面查看完整的示例列表，你可能会发现这对你掌握这门语言很有用。其中一些特性包括模板字符串、块范围的变量和常量、迭代器、生成器、新的数据结构(如 Map 和 Set)等等。

* * *

**要了解更多关于 ES2015 的信息，请查看我们的高级课程:[走进 ES2015](https://www.sitepoint.com/premium/courses/diving-into-es2015-2924) 。**

* * *

### 代码林挺

Linters 是解析您的代码并将其与一组规则进行比较的工具，用于检查语法错误、格式和良好实践。尽管向每个人推荐使用棉绒，但如果你刚开始使用，它会特别有用。为代码编辑器/IDE 正确配置后，您可以获得即时反馈，以确保在学习新的语言特性时不会遇到语法错误。

可以[去看看 ESLint](https://www.sitepoint.com/up-and-running-with-eslint-the-pluggable-javascript-linter/) ，是最受欢迎的之一，支持 ES2015+。

## 模块化代码

现代 web 应用程序可能有几千行(甚至几十万行)代码。如果没有一种机制来将所有东西组织成更小的组件，编写可以以受控方式在必要时重用的专门和隔离的代码片段，那么在这样的规模下工作几乎是不可能的。这是模块的工作。

### CommonJS 模块

这些年出现了一些模块格式，其中最流行的是 [CommonJS](https://en.wikipedia.org/wiki/CommonJS) 。这是 Node.js 中的默认模块格式，可以在模块捆绑器的帮助下在客户端代码中使用，我们很快就会谈到这一点。

它利用一个`module`对象从 JavaScript 文件中导出功能，并利用一个`require()`函数将该功能导入到您需要的地方。

```
// lib/math.js
function sum(x, y) {
  return x + y;
}

const pi = 3.141593

module.exports = {
  sum: sum,
  pi: pi
};

// app.js
const math = require("lib/math");

console.log("2π = " + math.sum(math.pi, math.pi)); 
```

### ES2015 模块

ES2015 引入了一种直接在语言中定义和使用组件的方法，这在以前只有第三方库才能实现。您可以拥有具有所需功能的单独文件，并只导出某些部分，使它们可用于您的应用程序。

*注意:对 ES2015 模块的原生浏览器支持仍在开发中，因此您目前需要一些附加工具才能使用它们。*

这里有一个例子:

```
// lib/math.js

export function sum(x, y) {
  return x + y;
}
export const pi = 3.141593; 
```

这里我们有一个模块，*导出*一个函数和一个变量。我们可以将该文件包含在另一个文件中，并使用这些导出的函数:

```
// app.js

import * as math from "lib/math";

console.log("2π = " + math.sum(math.pi, math.pi)); 
```

或者，我们也可以具体说明，只导入我们需要的内容:

```
// otherApp.js

import {sum, pi} from "lib/math";

console.log("2π = " + sum(pi, pi)); 
```

这些例子摘自[巴别塔网站](https://babeljs.io/learn-es2015)。要深入了解，请查看[了解 ES6 模块](https://www.sitepoint.com/understanding-es6-modules/)。

## 包装管理

其他语言早就有了自己的包存储库和管理器，以便更容易找到和安装第三方库和组件。Node.js 自带包管理器和库， [npm](https://www.sitepoint.com/beginners-guide-node-package-manager/) 。尽管还有其他可用的包管理器，但 npm 已经成为事实上的 JavaScript 包管理器，据说是世界上最大的包注册中心。

在 [npm 资源库](https://www.npmjs.com/)中，您可以找到第三方模块，您可以通过一个`npm install <package>`命令轻松下载并在您的项目中使用这些模块。这些包被下载到一个本地的`node_modules`目录中，该目录包含所有的包及其依赖项。

您下载的包可以在一个 [package.json](https://docs.npmjs.com/files/package.json) 文件中注册为项目的依赖项，以及关于您的项目或模块的信息(它本身可以作为一个包发布在 npm 上)。

您可以为开发和生产定义单独的依赖关系。虽然生产依赖项是包工作所必需的，但是开发依赖项只是包的开发人员所必需的。

**示例 package.json 文件**

```
{
  "name": "demo",
  "version": "1.0.0",
  "description": "Demo package.json",
  "main": "main.js",
  "dependencies": {
    "mkdirp": "^0.5.1",
    "underscore": "^1.8.3"
  },
  "devDependencies": {},
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Sitepoint",
  "license": "ISC"
} 
```

## 构建工具

我们在开发现代 JavaScript web 应用程序时编写的代码几乎永远不会是将投入生产的代码。我们用浏览器可能不支持的现代版本的 JavaScript 编写代码，我们大量使用第三方包，这些包位于`node_modules`文件夹中以及它们自己的依赖项，我们可以有像静态分析工具或缩小器这样的过程。构建工具的存在有助于将所有这些转换成可以有效部署并且大多数 web 浏览器能够理解的东西。

### 模块捆绑

当使用 ES2015/CommonJS 模块编写干净、可重用的代码时，我们需要某种方式来加载这些模块(至少在浏览器支持 ES2015 模块原生加载之前)。在 HTML 中包含一堆脚本标记实际上并不是一个可行的选择，因为它对于任何严肃的应用程序来说都会很快变得笨拙，并且所有这些单独的 HTTP 请求都会损害性能。

我们可以使用 ES2015 中的`import`语句(或 CommonJS 中的`require`)包含所有需要的模块，并使用模块捆绑器将所有内容组合到一个或多个文件(捆绑包)中。我们将把这个捆绑文件上传到我们的服务器，并包含在我们的 HTML 中。它将包含所有导入的模块及其必要的依赖项。

目前有几个流行的选项，最流行的是 [Webpack](http://webpack.js.org) 、 [Browserify](http://browserify.org) 和 [Rollup.js](http://rollupjs.org) 。你可以根据你的需要选择其中之一。

* * *

如果你想了解更多关于模块捆绑以及它如何融入应用程序开发的大环境，我推荐你阅读[理解 JavaScript 模块:捆绑&传输](https://www.sitepoint.com/javascript-modules-bundling-transpiling/)。

* * *

### 蒸腾作用

虽然新浏览器对现代 JavaScript 的支持非常好，但你的目标受众可能包括部分支持或不支持的旧浏览器和设备。

为了让我们的现代 JavaScript 工作，我们需要将我们编写的代码翻译成早期版本中的对等物(通常是 ES5)。完成这项任务的标准工具是[Babel](https://babeljs.io)——一个编译器，可以将你的代码翻译成大多数浏览器兼容的代码。这样，你就不必等待厂商来实现一切；你可以使用所有现代的 JS 特性。

有几个特性需要的不仅仅是语法翻译。Babel 包括一个 [Polyfill](http://babeljs.io/docs/usage/polyfill/) ，它模拟了一些复杂功能(如承诺)所需的一些机制。

### 构建系统和任务运行者

模块捆绑和移植只是我们项目中可能需要的两个构建过程。其他包括代码精简(减少文件大小)、分析工具，以及可能与 JavaScript 无关的任务，如图像优化或 CSS/HTML 预处理。

任务的管理可能会变得很费力，我们需要一种自动化的方式来处理它，能够用更简单的命令来执行一切。两个最流行的工具是 [Grunt.js](http://gruntjs.com) 和 [Gulp.js](http://gulpjs.com) ，它们提供了一种以有序的方式将你的任务组织成组的方法。

例如，你可以有一个类似于`gulp build`的命令，它可以运行代码 linter，用 Babel 执行 transpilation 过程，用 Browserify 执行模块捆绑。我们不必按顺序记住三个命令及其相关的参数，只需执行一个命令就可以自动处理整个过程。

无论您在哪里发现自己手动组织项目的处理步骤，请考虑是否可以使用任务运行器来实现自动化。

* * *

**延伸阅读: *[一饮而尽简介. js](https://www.sitepoint.com/introduction-gulp-js/)* 。**

* * *

## 应用架构

Web 应用程序与网站有不同的要求。例如，虽然页面重新加载对于博客来说是可以接受的，但对于像 Google Docs 这样的应用程序来说肯定不是这样。您的应用程序应该尽可能地接近桌面应用程序。否则，可用性会受到影响。

旧式的 web 应用程序通常是通过从 web 服务器发送多个页面来完成的，当需要大量动态内容时，通过 Ajax 加载内容，根据用户动作替换大量的 HTML。尽管这是迈向更具活力的网络的一大步，但它肯定有其复杂性。从用户的角度来看，发送每个用户动作的 HTML 片段甚至整个页面都是浪费资源——尤其是浪费时间。可用性仍然比不上桌面应用程序的响应能力。

为了改善现状，我们创建了两种新的方法来构建 web 应用程序——从我们向用户展示它们的方式，到我们在客户机和服务器之间通信的方式。虽然应用程序所需的 JavaScript 数量也急剧增加，但结果是现在的应用程序行为非常接近本地应用程序，无需每次单击按钮都重新加载页面或长时间等待。

### 单页应用程序(spa)

web 应用最常见的高级架构叫做 [SPA](https://en.wikipedia.org/wiki/Single-page_application) ，代表*单页面应用*。spa 是 JavaScript 的大块，包含了应用程序正常工作所需的一切。UI 完全在客户端呈现，因此不需要重新加载。唯一改变的是应用程序内部的数据，这些数据通常由远程 API 通过 [Ajax](https://en.wikipedia.org/wiki/Ajax_(programming)) 或另一种异步通信方法来处理。

这种方法的一个缺点是首次加载应用程序需要更长的时间。然而，一旦它被加载，视图(页面)之间的转换通常会快得多，因为它只是在客户机和服务器之间发送的纯数据。

### 通用/同构应用

虽然 SPAs 提供了很好的用户体验，但根据您的需求，它们可能不是最佳解决方案——尤其是如果您需要更快的初始响应时间或搜索引擎的最佳索引。

最近有一种解决这些问题的方法，叫做[同构](http://isomorphic.net/javascript)(或通用)JavaScript 应用。在这种类型的架构中，大部分代码都可以在服务器和客户端上执行。您可以选择要在服务器上呈现的内容，以加快初始页面加载速度，之后，当用户与应用程序交互时，客户端将接管呈现。因为页面最初是在服务器上呈现的，所以搜索引擎可以正确地索引它们。

## 部署

对于现代 JavaScript 应用程序，您编写的代码与您为生产部署的代码不同:您只部署构建过程的结果。完成这个任务的工作流程可能会有所不同，这取决于项目的规模、开发人员的数量，有时还取决于您使用的工具/库。

例如，如果您独自处理一个简单的项目，每次准备好部署时，您只需运行构建过程并将结果文件上传到 web 服务器。请记住，您只需要上传构建过程(编译、模块捆绑、缩小等)的结果文件。)，它可以只是一个包含整个应用程序和依赖项的`.js`文件。

您可以有这样的目录结构:

```
├── dist
│   ├── app.js
│   └── index.html
├── node_modules
├── src
│   ├── lib
│   │   ├── login.js
│   │   └── user.js
│   ├── app.js
│   └── index.html
├── gulpfile.js
├── package.json
└── README 
```

因此，您的所有应用程序文件都在一个`src`目录中，用 ES2015+编写，从一个`lib`目录导入随 npm 安装的包和您自己的模块。

然后，您可以运行 Gulp，它将执行来自`gulpfile.js`的指令来构建您的项目——将所有模块捆绑到一个文件中(包括使用 npm 安装的模块),将 ES2015+传输到 ES5，缩小结果文件，等等。然后您可以配置它，将结果输出到一个方便的`dist`目录中。

*注意:如果你有不需要任何处理的文件，你可以把它们从`src`复制到`dist`目录。您可以在您的构建系统中为此配置一个任务。*

现在，您可以将文件从`dist`目录上传到 web 服务器，而不必担心其他文件，这些文件只对开发有用。

### 团队发展

如果你和其他开发人员一起工作，很可能你也在使用一个共享的代码库，比如 GitHub，来存储项目。在这种情况下，您可以在提交之前运行构建过程，并将结果与其他文件一起存储在 Git 存储库中，以便以后下载到生产服务器上。

然而，如果几个开发人员一起工作，在存储库中存储构建的文件很容易出错，并且您可能希望保持所有构建工件的整洁。幸运的是，有一个更好的方法来处理这个问题:你可以放一个像[詹金斯](http://jenkins.io)、[特拉维斯 CI](http://travis-ci.org) 、 [CircleCI](http://circleci.com) 等服务。在这个过程中，它可以在每次提交到存储库之后自动构建您的项目。开发人员只需担心推进代码变更，而不必每次都先构建项目。存储库也没有自动生成的文件，最后，您仍然拥有可用于部署的构建文件。

## 结论

如果您最近几年远离了 web 开发，那么从简单的 web 页面到现代 JavaScript 应用程序的转变可能会令人望而生畏，但是我希望本文作为一个起点是有用的。在可能的情况下，我链接了每个主题的更深入的文章，以便您可以进一步探索。

请记住，如果在某个时候，看了所有可用的选项后，所有的东西都显得势不可挡和杂乱无章，只要记住[亲吻原则](https://en.wikipedia.org/wiki/KISS_principle)，只使用你认为你需要的东西，而不是你所有可用的东西。归根结底，解决问题才是最重要的，而不是使用最新的东西。

学习现代 JavaScript 开发有什么体验？还有什么我在这里没有提到的，你希望以后能看到的吗？我很想收到你的来信！

## 分享这篇文章