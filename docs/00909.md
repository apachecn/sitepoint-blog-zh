# 巴别塔初学者指南

> 原文：<https://www.sitepoint.com/babel-beginners-guide/>

**这篇文章介绍了 [Babel](https://babeljs.io/) ，这是一个 JavaScript 编译器，允许开发人员今天使用下一代 JavaScript。**

构建 web 应用程序时编写 JavaScript 可能会令人沮丧。我们必须考虑目标浏览器中的可用特性，以及当某个特性没有实现时会发生什么。有些人会建议干脆不要使用它，如果我们正在构建一些复杂的东西，大多数时候这是一个痛苦的经历。

令人欣慰的是，一些工具让我们不必担心支持什么，只需尽我们所能编写最好的代码。他们被称为运输者。一个 **transpiler** 是一个工具，它将源代码作为输入，并产生新的源代码作为输出，具有不同的语法，但语义上尽可能接近——或者理想地等同于——原始代码。

Babel 几乎是将现代 JavaScript (ES2015+)翻译成在旧浏览器上运行的兼容实现的标准 transpiler。如果您只想专注于编写 JavaScript，这是完美的解决方案。

尽管 Babel 的主要目标是为旧的(有时是当前的)浏览器翻译 ECMAScript 的最新标准，但它可以做得更多。有一个预置和插件的生态系统，使添加非标准功能成为可能。每个插件为你的代码提供一个新的特性/转换，而预置只是一个插件的集合。

## 入门指南

根据您的项目和您使用的工具，有不同的方法来设置 Babel。在本文中，我们将解释如何使用 CLI 来设置 Babel，尽管如果您使用的是构建系统或框架，您可以在[官方网站](https://babeljs.io/docs/setup/)上查看具体说明。大多数情况下，CLI 是最快速、最简单的入门方式，所以如果您是第一次使用，请放心继续。

在项目中设置 Babel 的第一步是使用 npm 安装软件包，并将其添加为 dev 依赖项。假设您已经有了一个工作的 Node.js 环境，只需在终端中运行以下代码:

```
mkdir babel-test
cd babel-test
npm init -y
npm install --save-dev babel-cli 
```

这将创建一个目录(`babel-test`)更改到目录中，初始化一个 npm 项目(从而创建一个`package.json`文件)，然后安装 babel-cli 作为一个开发依赖项。

如果你在以上方面需要任何帮助，请参考我们的教程关于[安装节点](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)和[使用 npm](https://www.sitepoint.com/beginners-guide-node-package-manager/) 。

接下来，我们可以打开`package.json`并将`build`命令添加到 npm 脚本中:

```
"scripts": {
  "build": "babel src -d dist"
} 
```

这将从`src`目录中获取源文件，并将结果输出到`dist`目录中。那么我们可以这样执行它:

```
npm run build 
```

但是等等！在运行 Babel 之前，我们必须安装和设置将转换我们代码的插件。最简单快捷的方法是添加 [Env 预置](https://babeljs.io/docs/plugins/preset-env/)，它会根据您指定的目标浏览器选择合适的插件。它可以通过以下方式安装:

```
npm install babel-preset-env --save-dev 
```

然后在项目的根目录下创建一个`.babelrc`文件并添加预置:

```
{
  "presets": ["env"]
} 
```

`.babelrc`文件是你为 Babel 放置所有设置的地方。您将主要使用它来设置预置和插件，但是还有更多选项可用。你可以在[巴别 API 页面](https://babeljs.io/docs/usage/api/#options)查看完整列表。

请注意，根据您的操作系统，以点号开头的文件在默认情况下将被隐藏。如果这对你有问题(或者如果你只是喜欢更少的文件)，你可以把你的 Babel 设置放在`package.json`文件中，在`babel`键下，像这样:

```
{
  "name": "babel-test",
  "version": "1.0.0",
  "babel": {
    // config
  }
} 
```

最后，让我们创建 Babel 期望找到的目录和文件:

```
mkdir src dist 
```

给它一些可以改变的东西:

```
let a = 1;
let b = 2;
[a, b] = [b, a];
console.log(a);
console.log(b); 
```

这个例子使用[析构赋值](https://www.sitepoint.com/es6-destructuring-assignment/)来交换两个变量的值。

## 奔跑的巴别塔

现在您已经有了一个现成的 Babel 安装，您可以执行`build`命令来运行编译过程:

```
npm run build 
```

这将从`src/main.js`获取代码，将其转换为 ES5 代码，并将转换后的代码输出到`dist/main.js`。

这是它产生的结果:

```
"use strict";

var a = 1;
var b = 2;
var _ref = [b, a];
a = _ref[0];
b = _ref[1];

console.log(a);
console.log(b); 
```

如您所见，`let`已经被`var`所取代，Babel 引入了一个临时变量(用下划线表示)来促进交换。

仅此而已。您在`src`目录中编写的代码将被翻译成该语言以前的版本。默认情况下，如果您不向预设添加任何选项，它将加载所有变换。您还可以按如下方式指定目标浏览器:

```
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"]
      }
    }]
  ]
} 
```

这将加载所需的转换，以支持每个浏览器和 Safari 的最新两个版本(大于或等于版本 7)。您可以在[浏览器列表存储库](https://github.com/ai/browserslist#queries)中找到目标浏览器的可用选项。

## 巴别塔生态系统:快速概览

正如您在上一节中注意到的，当您安装 Babel 时，它不会自己做任何事情。我们必须安装一组插件来获得想要的行为，或者我们可以使用预设，这是预定义的插件集。

通常，你想要包含的每个特性都是以插件的形式出现的。ES2015 的一些示例包括:

*   [常数](https://babeljs.io/docs/plugins/check-es2015-constants/)
*   [箭头功能](https://babeljs.io/docs/plugins/transform-es2015-arrow-functions/)
*   [块范围的函数](https://babeljs.io/docs/plugins/transform-es2015-block-scoped-functions/)
*   [类](https://babeljs.io/docs/plugins/transform-es2015-classes/)
*   [for-of](https://babeljs.io/docs/plugins/transform-es2015-for-of/)
*   [传播](https://babeljs.io/docs/plugins/transform-es2015-spread/)
*   [模板文字](https://babeljs.io/docs/plugins/transform-es2015-template-literals/)

参见 Babel 文档中的[插件页面](https://babeljs.io/docs/plugins/#transform-plugins)获取完整列表。

但是有时候你不想一个一个的包含所有的插件。因此，有预置，将有助于安装每个插件的过程。

目前可用的三种官方预设是:

*   [Env](https://babeljs.io/docs/plugins/preset-env/)
*   [反应](https://babeljs.io/docs/plugins/preset-react/)
*   [流量](https://babeljs.io/docs/plugins/preset-flow/)

Env 是最常用的，也是我们在这里使用的。它会根据目标浏览器自动加载所有必要的转换，以使您的代码兼容。

**React** 预置转换了 React 项目中常见的代码，主要增加了与[流](https://flow.org/)注释和 [JSX](https://facebook.github.io/jsx/) 的兼容性。

最后，**流**预置用于从[流](https://flow.org/)类型注释中清理代码(尽管它不检查类型是否有效。)

### Babel Polyfill

有些 JavaScript 特性不能进行语法转换，通常是因为没有实现等效的功能——例如，承诺和生成器函数。

这些类型的特性必须在浏览器中由一个库实现，以便在您的代码中使用，这就是 polyfill 的工作。

巴别塔多孔填料由 [core-js](https://github.com/zloirock/core-js) 和[再生器](https://github.com/facebook/regenerator)运行时组成。它们涵盖了 ES2015+的所有功能。

### 高级用途

如前所述，Babel 还可以用来转换语言中尚未实现的特性。这方面的一个很好的例子是类字段提案(目前处于 [TC39 阶段 3:候选人](https://github.com/tc39/proposal-class-fields))。这在 React devs 中特别受欢迎，因为它消除了将方法显式绑定到特定组件的必要性，也意味着组件的`state`可以声明为类字段(潜在地消除了对构造函数的需要)。

对于那些现在想使用类字段的人来说，您需要添加[babel-plugin-transform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/)作为开发依赖项:

```
npm install --save-dev babel-plugin-transform-class-properties 
```

您还需要如下更新您的`.babelrc`文件:

```
{
  "presets": ["env"],
  "plugins": ["transform-class-properties"]
} 
```

现在你可以写:

```
class App extends Component {
  state = { count: 0 };

  incCount = () => {
    this.setState(ps => ({ count: ps.count + 1 }));
  };

  render() {
    return (
      <div>  <p>{ this.state.count }</p>  <button onClick={this.incCount}>add one</button>  </div>
    );
  }
} 
```

而且不止于此。你也可以使用 Babel 给语言添加你自己的新特性，正如我们的教程[通过构建你自己的 Babel 插件](https://www.sitepoint.com/understanding-asts-building-babel-plugin/)所展示的。

## 可供选择的事物

编写现代 web 应用程序有时需要的不仅仅是 JavaScript 中可用的特性。其他语言也可以翻译成兼容的 JavaScript，但也实现其他有用的功能。

最流行的选项是 [TypeScript](http://www.typescriptlang.org/) ，这是一个常规的 JavaScript，它实现了现代 ES 的特性，但也添加了其他特性，尤其是关于类型安全的特性。

在另一个极端，不同类别中有完全不同的语言，从像 PureScript 这样的函数式语言到像 Dart 这样的面向对象语言。

为了更深入地了解替代语言，请看一下编译成 JavaScript 的 10 种语言。

## 结论

Babel 是编写现代应用程序的一个很好的选择，同时还能提供所有开发人员都能理解的 JavaScript，以及代码需要运行的各种浏览器。

Babel 不仅有助于将 ES2015+转换到该语言的早期版本，无论是在浏览器中还是在 Node.js 等平台上，也有助于添加标准之外的新功能。要明白我的意思，只要看看 npm 目录，找到所有可用的巴别塔[插件](https://www.npmjs.com/search?q=babel-plugin)或[预置](https://www.npmjs.com/search?q=babel-preset)。

由于 JavaScript 发展速度如此之快，很明显浏览器制造商需要一段时间来实现最新的特性。在您的工具包中给 Babel 一个位置意味着您可以编写当今最先进的 JavaScript，安全地知道您不会抛弃任何用户。有什么不喜欢的？

## 分享这篇文章