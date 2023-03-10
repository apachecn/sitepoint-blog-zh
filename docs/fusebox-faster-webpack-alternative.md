# FuseBox 简介——更快、更简单的 webpack 替代方案

> 原文：<https://www.sitepoint.com/fusebox-faster-webpack-alternative/>

webpack 可以说已经成为事实上的 JavaScript 模块捆绑器，但是它以混乱和难学而闻名。在这篇文章中，我想展示一个更快、更简单的 webpack 替代产品— [FuseBox](http://fuse-box.org/) 。

在当今快速发展的前端环境中，牢固掌握 JavaScript 模块系统至关重要。模块可以帮助组织您的代码，使其更易于维护，并增加其可重用性。不幸的是，[浏览器对 ES 模块](http://caniuse.com/#feat=es6-module)的支持还没有完全到位，所以你总是需要一个[模块捆绑器](https://www.sitepoint.com/premium/screencasts/a-look-at-javascript-module-bundlers)来将它们缝合成一个可以提交给浏览器的文件。

FuseBox 是下一代工具生态系统，提供了开发生命周期的所有需求。它使开发者能够捆绑任何文件格式，它是一个模块加载器、传输器、任务运行器等等。

在本文中，我们将使用 FuseBox 带您完成开发 JavaScript 应用程序的常见任务。这些措施如下:

*   [捆绑](#bundlingabasicexample):定义一个入口点和一个捆绑名称
*   [trans pilling](#transpilingtypescriptandes6):写入 TypeScript，目标 ES5
*   [模块加载](#moduleloading):将你的模块合并成一个文件
*   [处理其他资产](#workingwithplugins):使用 FuseBox 插件编译 Sass
*   [热模块重装(HMR)](#hotmodulereloadinghmr) :查看您的项目自动更新以反映您的更改
*   [任务运行](#meetsparkythebuiltintaskrunner):fuse box 内置任务运行器 Sparky 介绍
*   [单元测试](#unittesting):fuse box 内置测试运行器介绍
*   [目标生产](#developmentvsproductionenvironments):生产一个缩小的、丑陋的包用于部署。

一旦你读完了，你就能把 FuseBox 放到你的下一个项目中，并从它的速度、简单性和灵活性中获益。

## 捆绑——一个基本的例子

免责声明:我是这个项目的核心贡献者之一。

项目变得越来越大，这是事实。如果我们要一个接一个地包含页面需要的所有文件，这会使事情变得相当慢，因为浏览器必须发出一堆阻塞的 HTTP 请求。捆绑解决了这个问题，减少了请求文件的数量，FuseBox 使这个过程尽可能简单。

要开始捆绑，我们需要告诉 FuseBox 我们想要什么。FuseBox 不需要太多的配置来捆绑沉重的项目。事实上，对于大多数用例，十行配置通常就足够了。然而，在我们开始进入一些真实世界的例子之前，让我们创建一些简单的东西。

首先，创建一个新文件夹。然后，从命令行导航到它并输入以下内容:`npm init -y`。这将初始化您的项目。然后键入`npm install fuse-box -D`，这将安装 FuseBox 作为开发依赖。

接下来创建一个名为`src`的文件夹，所有的代码都将存放在这里。另外，在您的`src`文件夹中创建一个`index.js`文件，并在其中添加以下内容:

```
console.log('Hello world'); 
```

接下来，在项目的根目录下创建一个`fuse.js`文件。这个文件将包含你所有的保险丝盒配置。

此时，我们的文件夹结构应该如下所示:

```
MyProject
├── node_modules
├── src
│    └── index.js
├── fuse.js
└── package.json 
```

将以下代码添加到`fuse.js`:

```
const { FuseBox } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js"
});

fuse.bundle("app")
  .instructions("> index.js");

fuse.run(); 
```

让我们一段一段地分解这段代码。

首先，我们需要保险丝盒。然后我们用`init`方法初始化 FuseBox 的一个新实例。在保险丝盒术语中，这也被称为`Producer`。这是我们为所有捆绑包定义全局配置的地方。

选项将 FuseBox 指向我们文件的主目录。这是因为 FuseBox 创建了一个模拟物理文件结构的虚拟文件结构。`output`选项告诉 FuseBox 我们的输出包应该放在哪里。注意`$name.js`:这是一个占位符，将用您提供给 bundle 的名称替换。

命令`fuse.bundle("app")`是我们告诉 FuseBox 关于我们的包的地方。我们告诉 FuseBox 创建一个名为`app.js`的包，它将驻留在项目的`dist`文件夹中。结束文件将是`project/dist/app.js`。

`instructions('>index.js')`部分是我们告诉 FuseBox 我们想要捆绑什么的地方。符号`>`就是我们所说的[算术指令](https://fuse-box.org/page/instructions):fuse box 使用这种语言来了解什么文件需要被捆绑。

命令`fuse.run()`告诉 FuseBox 开始捆绑过程。

现在从命令行输入`node fuse.js`——就这样，您就完成了！FuseBox 现在将开始其捆绑魔法，并在`dist/app.js`创建捆绑包。

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/basic)。

## 传输 TypeScript 和 ES6

到目前为止，我们做得很好，但这并不是许多现代 JavaScript 项目的开发。今天的应用程序是使用 ES6 开发的，ES6 是 ECMAScript 语言规范的第六个主要版本。ES6 很棒:它支持新的语言特性，比如[类](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)、[箭头函数](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/)等等。但问题是，它还不被所有浏览器或 Node.js 版本完全支持。所以我们需要将代码转换成一个更常见的 JavaScript 支持版本，ES5。

有两个主要工具可以实现这一点:Typescript 和 Babel。FuseBox 支持两者。事实上，FuseBox 是用 Typescript 构建的，因此本机支持它。

要开始使用 FuseBox 和 Typescript，请执行以下操作:

*   创建新项目
*   使用命令行，导航到这个项目的根目录并执行`npm init -y`
*   创建一个`src`文件夹
*   在`src`文件夹中，添加`index.ts`
*   在项目的根目录下创建`fuse.js`
*   将 FuseBox 和 TypeScript 作为依赖项安装:`npm install fuse-box typescript -D`。

在`index.ts`中，添加以下内容:

```
const name: string = "FuseBox";
console.log(name); 
```

你可能在想什么`:string means`。这是 Typescript 类型系统的一个例子，它告诉编译器变量`name`的类型是`string`。要了解更多关于 Typescript 的信息，请查看[官方网站](https://www.typescriptlang.org)。

将以下内容添加到`fuse.js`:

```
const { FuseBox } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js"
});

fuse.bundle("app")
  .instructions("> index.ts");

fuse.run(); 
```

注意，事情还是和以前一样，唯一的不同是我们使用了`.ts`文件格式，而不是`instructions('>index.ts')`中的`.js`。现在先决条件已经就绪，从命令行输入`node fuse.js`，FuseBox 将开始捆绑。

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/transpiling)。

*注意:使用 ES6 语法时，FuseBox 会自动检测模块类型，无缝地传输代码。不需要巴别塔。保险丝盒摇滚！*

## 模块加载

到目前为止，我们只做了简单的例子。让我们更进一步，开始学习模块加载。模块是独立的、可重用的代码的离散单元。在 JavaScript 中，有很多方法可以创建模块。

FuseBox 将你的代码打包成 [CommonJS](https://en.wikipedia.org/wiki/CommonJS) 模块格式。不幸的是，浏览器不支持这种功能。但不需要担心:FuseBox 支持你，并提供了一个全面的 API，使在浏览器中使用模块变得轻而易举。

基于我们的 Typescript 示例，让我们创建一些模块并开始使用它们。因为我们使用 TypeScript，所以我们将使用 ES6 模块系统。

在您的`src`文件夹中，在`index.ts`旁边，创建`hello.ts`，并添加以下内容:

```
export function hello(name: string) {
  return `Hello ${name}`;
} 
```

在`index.ts`中，添加以下内容:

```
import { hello } from "./hello";

const name: string = `Mr. Mike`;
console.log(hello(name)); 
```

现在，从命令行输入`node fuse.js`，然后输入`node dist/app.js`。您应该会在控制台上看到以下内容:

```
 Hello Mr. Mike 
```

恭喜你！您刚刚用 FuseBox、ES6 和 Typescript 创建并加载了您的第一个模块。:)

我们已经学习了如何加载本地模块，但是 FuseBox 也可以处理外部节点包。因此，让我们扩展这个例子，并展示如何将 [Moment.js](https://www.npmjs.com/package/moment) 作为一个模块包含进来。

从命令行输入`npm install moment -S`。该命令将 Moment.js 包作为项目的依赖项进行安装。现在将以下内容添加到您的`index.ts`:

```
import {hello} from "./hello";
import * as moment from "moment"

const time = moment().format('MMMM Do YYYY, h:mm:ss a');
const name: string = `Mr. Mike`;
console.log(hello(name));
console.log(time); 
```

如果您现在输入`node fuse.js`，然后输入`node dist/index.js`，您应该会在您的控制台上看到以下内容(尽管日期显然会有所不同):

```
Hello Mr. Mike
March 7th 2018, 11:50:48 am 
```

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/module-loading)。

## 使用插件

没有一个工具生态系统能够满足所有用户的所有需求，FuseBox 也不例外。尽管 FuseBox 提供了广泛的开箱即用功能，但仍有一个活跃的开发者社区，用令人敬畏的新插件丰富了这个生态系统。

到目前为止，我们一直专门处理 JavaScript 和 TypeScript 文件。现在是时候深入挖掘并开始使用 FuseBox 插件了。我们将从使用两个重要插件开始: [CSSPlugin](https://fuse-box.org/page/css-plugin) 和 [SassPlugin](https://fuse-box.org/page/sass-plugin) 。

让我们像往常一样:

*   创建新项目
*   使用命令行，导航到这个项目的根目录并执行`npm init -y`
*   创建一个`src`文件夹
*   在`src`文件夹内添加`index.ts`
*   在`src`文件夹内添加`main.css`
*   在`src`文件夹内添加`extra.scss`
*   在项目的根目录下创建`fuse.js`
*   通过从命令行输入以下命令，安装 FuseBox、TypeScript 和 Sass 编译器作为依赖项:`npm install fuse-box typescript node-sass -D`。

注意文件`extra.scss` file:这是我们写 [Sass](http://sass-lang.com/) 的地方。当然，浏览器本身并不支持 Sass，这也是我们使用 FuseBox `SassPlugin`的原因。你的`fuse.js`应该是这样的:

```
const { FuseBox, CSSPlugin, SassPlugin } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js",
  plugins: [
    [SassPlugin(), CSSPlugin()],
    CSSPlugin()
  ]
});
fuse.bundle("app")
  .instructions(`> index.ts`);

fuse.run(); 
```

注意，我们在上面重复了两次 CSSPlugin。这是因为 FuseBox 中一个叫做**插件链接**的重要概念。这个概念非常强大，因为它使 FuseBox 能够将一个插件的结果传递给下一个插件。

接下来，让我们向样式表文件添加一些内容。

在`main.css`中，添加以下内容:

```
body {
  margin: 0;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  background: #ececec;
} 
```

在`extra.scss`中添加以下内容:

```
$font-stack:    Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
} 
```

现在让我们导入这些样式表文件进行处理和打包。在您的`index.ts`中，添加以下内容:

```
import "./main.css";
import "./extra.scss";

document.body.innerHTML = ` <div class="content">
  <h1>Welcome to FuseBox!</h1>
</div>`; 
```

与其他解决方案不同， *FuseBox 允许你导入 JavaScript 之外的文件，无需任何额外的变通*。现在 FuseBox 将运行 SassPlugin，将 Sass 编译成 CSS，并为您绑定`main.css`和`extra.scss`。然而，我们如何预览结果呢？现在是时候介绍另一个很酷的内置插件了，叫做 [WebIndexPlugin](https://fuse-box.org/page/web-index-plugin) 。

这里我们有另一个例子来说明 FuseBox 如何让开发人员的生活变得更容易。这个插件自动生成一个 HTML 文件，你可以传递给它任何其他的 HTML 文件作为模板。然而，关于这个插件最重要的一点是，它会自动为您包含这些包。这意味着您不需要考虑应该手动添加什么。一旦我们稍后讨论[散列](http://fuse-box.org/page/configuration#hash)，我们将会看到这种行为的更多好处。

您的`fuse.js`应该是这样的:

```
const { FuseBox, CSSPlugin, SassPlugin, WebIndexPlugin } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js",
  plugins: [
    [SassPlugin(), CSSPlugin()],
    CSSPlugin(),
    WebIndexPlugin({path: "."})
  ]
});

fuse.bundle("app")
  .instructions(">index.ts");

fuse.run(); 
```

从你的命令行，运行`node fuse.js`，现在 FuseBox 会在`dist`文件夹中生成`index.html`。打开这个文件，你会看到你的 CSS 应用到页面。

现在，在你的`main.css`中，试着将`background: #ececec;`改为`background: blue;`。在命令行中，输入`node fuse.js`，刷新浏览器中的页面，您会看到您的页面现在有了蓝色背景。

请注意，每当我们对代码进行更改并希望看到结果时，我们都必须输入`node fuse.js`。这不是一种明智的工作方式，对吗？为了解决这个问题，FuseBox 有一个[手表方法](https://fuse-box.org/page/watching)。方法指示 FuseBox 在任何文件改变时自动重新绑定。

让我们将手表添加到我们的产品包中。现在`fuse.js`应该是这样的:

```
const { FuseBox, CSSPlugin, SassPlugin, WebIndexPlugin } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js",
  plugins: [
    [SassPlugin(), CSSPlugin()],
    CSSPlugin(),
    WebIndexPlugin({path: "."})
  ]
});

fuse.bundle("app")
  .watch()
  .instructions(">index.ts");

fuse.run(); 
```

从命令行输入`node fuse.js`，然后修改你的任何文件。您将看到 FuseBox 自动为您重新绑定，而无需手动输入`node fuse.js`。

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/plugins)。

## 热模块重装(HMR)

热模块重载(HMR)允许 FuseBox 检测文件何时被更改，并更新内存中的这些模块，这意味着浏览器中的视图也将被更新。这是一个非常强大的功能！必须在编辑器和浏览器之间切换，点击`F5`才能看到变化的日子已经一去不复返了。与其他一些解决方案不同， *FuseBox HMR 适用于你所有的文件，而不仅限于 JavaScript 文件*。简单来说:修改你的代码，保存，FuseBox 会自动为你更新你的项目，在浏览器中显示更新后的应用。

当我们在谈论 FuseBox HMR 的时候，这是一个介绍 FuseBox 内置的[开发服务器](https://fuse-box.org/page/development-server)的好时机。这是一个完整的 Express 应用程序，这意味着你可以创建路线，添加中间件等。查看完整的文档以获得更多信息。

在最后一个例子的基础上，更新您的`fuse.js`文件，如下所示:

```
const { FuseBox, CSSPlugin, SassPlugin, WebIndexPlugin } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js",
  plugins: [
    [SassPlugin(), CSSPlugin()],
    CSSPlugin(),
    WebIndexPlugin({path: "."})
  ]
});

fuse.dev();

fuse.bundle("app")
  .instructions("> index.ts")
  .watch()
  .hmr();

fuse.run(); 
```

注意，我们添加了两个额外的命令:`fuse.dev()`和`.hmr()`。这就是使用 FuseBox 启用 HMR 所需的全部内容。从你的命令行输入`node fuse.js`，你会在控制台看到如下消息:`server running http://localhost:4444`。

这是 FuseBox 将为您提供的访问项目的 URL。前往 [http://localhost:4444/](http://localhost:4444/) ，开始修改一些 CSS 或 JavaScript 文件。您将看到浏览器中立即反映出的变化，无需一次刷新！

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/hmr)。

## 见见内置任务运行器 Sparky

到目前为止，我们一直在处理捆绑文件，但大多数项目通常需要执行一些额外的任务，如清理文件夹、复制文件、增加应用程序版本等。

还记得我们说过 FuseBox 是您开发需求所需要的唯一工具吗？好吧，在这里我们开始看到这种说法成为现实。FuseBox 有一个内置的任务运行器，名为`Sparky` ( [你可以在这里](http://fuse-box.org/page/sparky)了解更多)。默认情况下，它涵盖了前面提到的常见任务，但可以使用插件轻松扩展，以涵盖一些更多的自定义任务。

基于上一个示例，让我们创建以下内容:

*   每次开始捆绑过程时清理`dist`文件夹的任务
*   每当您添加、编辑或删除任何图像时，从您的`src`文件夹中复制图像文件的任务。

基于上一个例子，您的`fuse.js`应该是这样的:

```
const { FuseBox, CSSPlugin, SassPlugin, WebIndexPlugin, Sparky } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js",
  plugins: [
    [SassPlugin(), CSSPlugin()],
    CSSPlugin(),
    WebIndexPlugin({path: "."})
  ]
});

fuse.dev();

fuse.bundle("app")
  .instructions("> index.ts")
  .watch()
  .hmr();

Sparky.task("clean", () => {
  return Sparky.src("dist").clean("dist");
});

Sparky.task("watch:images", () => {
  return Sparky.watch("**/*.+(svg|png|jpg|gif)", {base: "./src"})
    .dest("./dist");
});

Sparky.task("default", ["clean", "watch:images"], () => {
  fuse.run();
}); 
```

让我们看看这里有什么新内容。首先，我们需要 Sparky，然后我们创建了三个任务。任务`default`是 Sparky 使用的约定，运行`node fuse.js`时会自动执行。注意我们在`default`任务定义中有`["clean", "watch:images"]`。这是 Sparky 执行流程的一个例子。Sparky 有两种模式:`waterfall`和`parallel`模式。在`waterfall`模式下，任务将被顺序执行，这意味着在`clean`任务完成之前，上述`watch:images`任务不会被执行。

还要注意，我们将`fuse.run`移动到了`default`任务中。这一点很重要，因为我们希望确保捆绑在其他任务完成后开始。

第二个`clean`任务非常简单:我们使用`clean`方法删除`dist`文件夹。

第三个`watch:images`任务负责复制图像。`Sparky.watch`是一个内置的文件监视方法，每当你的文件夹发生变化时就会启动。`"**/*.+(svg|png|jpg|gif)"`是一个文件包，告诉 Sparky 在我们的`src`文件夹中查看所有这些图像文件格式。`.dest`方法告诉 Sparky 将任何更改过的文件复制到`dist`文件夹。

向您的`src`文件夹中添加一些图像，然后运行`node fuse.js`。Sparky 现在将清除`dist`文件夹中的所有文件，并将`src`文件夹中的所有图像复制到`dist`文件夹中。现在尝试编辑`src`文件夹中的任何图像，你会看到 Sparky 文件监视器会启动并将该文件复制到`dist`文件夹中。

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/task-running)。

## 单元测试

测试是我们所知道的提高软件质量的最强大的工具。测试减少了错误，提供了准确的文档，并改进了设计。

保险丝盒带有一个[内置测试滑槽](http://fuse-box.org/page/test-runner)。它建立在 Fusebox 用于捆绑的相同引擎之上，这意味着它受益于 FuseBox 拥有的一切——速度、缓存、插件等。其次，它默认使用 Typescript，也就是说你不用连线任何东西；它只是工作。

让我们像往常一样:

*   创建新项目
*   在命令行中导航到这个项目的根目录，然后执行`npm init`
*   创建一个`src`文件夹
*   在`src`文件夹中，添加`index.ts`
*   在`src`文件夹中，添加`index.test.ts`
*   在项目的根目录下创建`fuse.js`
*   从命令行执行以下操作，将 FuseBox、babel-runtime 和 TypeScript 安装为依赖项:`npm install fuse-box fuse-test-runner babel-runtime typescript -D`。

您的`fuse.js`应该是这样的:

```
const { FuseBox } = require("fuse-box");

const fuse = FuseBox.init({
  homeDir: "src",
  output: "dist/$name.js"
});

fuse.dev();

fuse.bundle("app")
  .instructions("> index.ts")
  .watch()
  .hmr();

fuse.run();
fuse.bundle("app").test("[**/**.test.ts]"); 
```

配置还是一样的。我们只是多加了一行:`fuse.bundle("app").test("[**/**.test.ts]");`。这一行指示 FuseBox 运行任何以`.test.ts`结尾的测试文件。

在`index.ts`中，添加以下内容:

```
export const count = 1;

export function calculate(num) {
  return num + 1;
} 
```

在`index.test.ts`中，添加以下内容:

```
import { should } from "fuse-test-runner";
import { count, calculate } from "./index";

export class BarTest {
  "Should be okay"() {
    should(count).beOkay();
  }

  "Should equal 2"() {
    should(calculate(count))
      .equal(2);
  }

  "Should not equal 2"() {
    should(calculate(5))
      .equal(2);
  }
} 
```

让我们看看我们在做什么:

*   我们需要内置的断言库
*   我们需要来自`index.ts`的必要功能
*   我们创建一个类来包装我们的测试方法
*   我们创建测试方法来测试我们的代码。

先来考第一个`Should be okay`。这里我们只想验证`count`变量不是空的或者未定义的。为此，我们使用`should(count).beOkay();`。我们用方法`should`开始任何断言，它接受变量、函数、对象和表达式。然后我们使用一个内置的断言。在上面的例子中，我们使用了`beOkay`，它断言`count`变量不为空或未定义。

在第二个测试`Should equal 2`中，我们将`count`传递给`calculate`函数，并断言返回正确的结果。

在第三个测试`Should not equal 2`中，我们故意通过传递数字`5`来迫使测试失败，导致值`6`，它不等于预期值`2`。

现在运行`node fuse.js`，您将在控制台中看到前两个测试通过，第三个测试失败。

![Unit testing](img/2d0da3c1e933e446280ade2cb6c032f4.png)

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/unit-testing)。

## 开发与生产环境

我们已经讨论了主要的 FuseBox 概念，但是通常您对开发和生产环境都有不同的需求。例如，您不会在启用 HMR 的情况下发布您的捆绑代码。为了帮助您做到这一点，我们将通过一个推荐的设置来满足开发和生产需求。

让我们像往常一样:

*   创建新项目
*   在命令行中导航到这个项目的根目录，然后执行`npm init -y`
*   创建一个`src`文件夹
*   在`src`文件夹中，添加`index.ts`
*   在`src`文件夹中，添加`calc.ts`
*   在`src`文件夹中，添加`calc.test.ts`
*   在`src`文件夹中，添加`main.css`
*   在`src`文件夹中，添加`extra.scss`
*   在项目的根目录下创建`fuse.js`
*   通过从命令行运行以下命令，将 FuseBox、Sass、UglifyJS、babel-runtime 和 TypeScript 安装为依赖项:`npm install fuse-box fuse-test-runner babel-runtime typescript node-sass uglify-js -D`。

将以下内容添加到`fuse.js`:

```
const { FuseBox, CSSPlugin, SassPlugin, WebIndexPlugin, UglifyJSPlugin, Sparky } = require("fuse-box");

let fuse, app, vendor, isProduction = false;

Sparky.task("config", () => {
  fuse = FuseBox.init({
    homeDir: "src",
    output: "dist/$name.js",
    hash: isProduction,
    sourceMaps: !isProduction,
    plugins: [
      [SassPlugin(), CSSPlugin()],
      CSSPlugin(),
      WebIndexPlugin({path: "."}),
      isProduction && UglifyJSPlugin()
    ]
  });

  // vendor should come first
  vendor = fuse.bundle("vendor")
    .instructions("~ index.ts");

  // out main bundle
  app = fuse.bundle("app")
    .instructions(`!> [index.ts]`);

  if (!isProduction) {
    fuse.dev();
  }
});

// development task "node fuse""
Sparky.task("default", ["config"], () => {
  vendor.hmr().watch();
  app.watch();
  return fuse.run();
});

// Dist task "node fuse dist"
Sparky.task("dist", ["set-production", "config"], () => {
  return fuse.run();
});

Sparky.task("set-production", () => {
  isProduction = true;
  return Sparky.src("dist/").clean("dist/");
});

Sparky.task("test", ["config"], () => {
  return app.test();
}); 
```

以上可能看起来有很多要消化，但是不要担心:一旦我们把它分解，你就会意识到它是多么的强大和简单。

概念保持不变，但是我们添加了一个新变量`isProduction`。这将设置一个条件，定义 Fusebox 应该为您创建开发包还是生产包。

然后在我们传递给`FuseBox.init()`的对象上有一个`hash`属性，它被设置为`isProduction`的值。这是 FuseBox 的散列特性，对于管理版本非常重要。它允许我们在提供新版本的应用程序时使浏览器缓存无效，这反过来保证了我们的用户将获得我们应用程序的最新版本。启用散列后，每次我们开始捆绑过程时，FuseBox 都会为每个捆绑包生成一个文件散列名称。例如:

```
d72d7ad8-app.js
b841f28-vendor.js 
```

当我们投入生产时，我们需要我们的代码尽可能的小和高性能。我们通过使用添加的`UglifyJSPlugin`来缩小包来实现这一点。

在`plugins`属性中，注意下面一行:`isProduction && UglifyJSPlugin()`。这是一个 JavaScript 表达式，这意味着如果`isProduction`变量的计算结果为`true`，则应用`UglifyJSPlugin`。

接下来，我们创建一个`vendor`包。捆绑包背后的想法是将本地代码与外部包和依赖项分开。这允许使用模块化方法，并减少捆绑包中的重复代码。注意在`.instructions("~ index.ts"`中，`~`符号是一个 [FuseBox 算术符号](https://fuse-box.org/page/instructions#arithmetic-symbols)，指示 FuseBox 提取所有外部依赖，忽略实际的项目文件。简单地说，这将捆绑所有的外部依赖项(npm 包)减去本地代码。

继续，我们创建主包。注意`.instructions("!> [index.ts]")`中的`!>`符号。这些都是算术符号。`!`指示 FuseBox 从一个捆绑包中移除加载器 API(因为我们已经将它包含在供应商捆绑包中)。`>`符号指示 FuseBox 在加载时自动执行文件。

### 这些任务

最后要说明的是我们的任务。注意，我们甚至将我们的配置封装在一个单独的`config`任务中。这允许我们在其他任务中使用它，而不会重复自己或污染我们的代码。

让我们从`default`任务开始。注意，我们用`vendor.hmr().watch()`启用了它内部的`HMR`。我们这样做是因为默认任务将针对我们的开发环境，所以这里的`isProduction`是`false`。

在`set-production`任务中，我们将`isProduction`变量设置为`true`。

另一方面,`dist`任务将针对我们的生产环境。因此，它依赖于`set-production`和`config`任务。这意味着在`set-production`任务完成之前`dist`任务不会运行，反过来在`config`任务完成之前`dist`任务也不会运行。

最后是`test`任务，它只是执行我们在项目中定义的测试。

现在，让我们来看看所有这一切是如何运作的。从命令行运行`node fuse.js`。您会注意到 FuseBox 执行以下操作:

*   开始捆绑过程
*   启动开发服务器
*   开始 HMR
*   每次我们修改代码时，HMR 和捆绑过程都会再次发生。

### 但是生产呢？

不要害怕！如果我们运行`node fuse.js dist`(注意`dist`参数)，FuseBox 执行以下操作:

*   开始捆绑过程。

没有运行 HMR 或开发服务器。这是因为我们处于生产模式。打开你的`dist`文件夹，你会看到 FuseBox 用随机的名字生成了你的包。打开任何一个包，你会发现你的代码看起来很有趣。那是因为 FuseBox 使用了`UglifyJSPlugin`来缩小你的代码。如果您比较一下开发模式和生产模式下的包的大小，您会发现不同之处！

最后，如果您想要运行您的测试，从命令行运行`node fuse.js test`。

此处提供了[的完整示例](https://github.com/fuse-box/getting-started/tree/master/examples/development-vs-production)。

## 轻量级 Webpack 替代产品

正如我们所看到的，我们仅仅通过使用 FuseBox 就能够完全覆盖现代开发工作流程的所有需求——从开发到生产。在探索这个库的过程中，您会发现很多更高级的选项和概念，但希望本文能帮助您踏上正确的道路，并享受使用 FuseBox 的乐趣。

我们在 [Gitter](https://gitter.im/fusebox-bundler/Lobby) 上的社区正在成长，我们非常友好和支持！如果你喜欢这个项目，别忘了启动我们的 [GitHub](https://github.com/fuse-box/fuse-box) 资源库，并在 [Twitter](https://twitter.com/FuseBoxJS) 上关注我们。否则，如果你有任何问题或意见，请不要犹豫，在下面的评论中留下它们。

*本文由 [Dominic Myers](https://github.com/annoyingmouse) 进行同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章