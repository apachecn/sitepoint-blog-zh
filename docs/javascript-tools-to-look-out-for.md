# 2021 年要注意的 5 种 JavaScript 工具

> 原文：<https://www.sitepoint.com/javascript-tools-to-look-out-for/>

JavaScript 生态系统发展迅速，你知道你的工具集在你选择它的那一刻就会被取代！

要跟上所有的库、框架和技术是不可能的，但是您可以观察行业内的趋势和发展方向。React.js、Vue.js、Svelte、Node.js 和 Express.js 将在 2021 年期间继续流行，但一些有趣的助手工具正在浮出水面。

以下是我 2021 年的首选。但是请不要依赖我的意见。*自己评价一下*。

## [Rollup.js](https://rollupjs.org/)

[Rollup.js](https://rollupjs.org/) 是由《T2》的作者 Rich Harris 开发的下一代 JavaScript 模块 bundler。它将小块代码编译成较大的单个文件，包括:

*   **插件架构**

    核心系统可以用插件扩展[，比如 Babel ES5 transpiling，TypeScript integration，ESLinting，Tersermini 化，甚至 CSS 处理。](https://github.com/rollup/awesome)

*   **模块兼容性**

    Rollup.js 支持标准的 ES6 模块，但是基于节点的 CommonJS `require()`模块可以用[插件](https://github.com/rollup/plugins/tree/master/packages/commonjs)解析。

*   **摇树**

    对代码进行静态分析，以排除不使用的变量、函数和方法。因此，您可以导入一个大的库，但是只有您正在使用的特性会包含在最终的包中。

*   **代码拆分**

    Rollup 可以将代码分割成块，用于动态(按需)加载或多个入口点。

Rollup.js 可以从命令行、一个`npm`脚本和一般的任务运行程序如 [Gulp](https://rollupjs.org/guide/en/#gulp) 中执行，可以有也可以没有观察选项。

可以为更复杂的配置定义一个`rollup.config.js`文件。例如:

```
// rollup.config.js

// CommonJS plugin
import commonjs from '@rollup/plugin-commonjs';

export default {

  // primary source entry script
  input: './src/main.js',

  // output script and format
  output: {
    file: './build/main.js',
    format: 'iife'
  },

  // plugins
  plugins: [
    commonjs()
  ]

}; 
```

Rollup.js 于 2018 年首次出现，但由于其速度和简单性，它的势头越来越大。你可能在 Snowpack 里没意识到就用过了。

## 积雪场

Snowpack 是一个快速的前端构建工具，是 webpack 和 package 等重量级选项的直接竞争对手。好处包括:

*   即时启动
*   带缓存的单一版本
*   热模块重装
*   几十个[插件](https://www.snowpack.dev/plugins)
*   内置支持 ES6 模块、CommonJS 模块、TypeScript、Svelte、React、JSX、CSS 模块、[等等](https://www.snowpack.dev/reference/supported-files)

Snowpack 自动建造资产。您可以将它作为开发依赖项安装到任何项目中:

```
npm install --save-dev snowpack 
```

然后启动开发服务器:

```
npx snowpack dev 
```

这将在浏览器中打开默认的`index.html`文件。对所有页面进行 JavaScript 和 CSS 文件扫描，这些文件被捆绑到单个资产中。

最终生产站点可在`build`目录中创建，包含:

```
npx snowpack build 
```

一个 [`snowpack.config.js`](https://www.snowpack.dev/reference/configuration) 配置文件可以定义插件和进一步的选项。

发展一直很快，Snowpack 版本于 2021 年 1 月推出。根据该网站的说法，*“一旦你尝试了，就不可能再回到别的事情上去了。”*

## [罗马](https://rome.tools/)

现代开发要求您使用不同的方法和技术安装、配置和学习一系列工具。 [Rome](https://rome.tools/) 旨在通过为 HTML 内容、CSS 和 JavaScript 提供 linter、编译器、bundler、文档生成器、格式化程序、测试运行程序和缩小程序来统一前端开发工具链。本质上，它是一个零依赖包，取代了 webpack、Babel、ESLint、appeller、Jest 和其他包。

罗马在 2020 年一直处于积极开发中，在撰写本文时，只有林挺受到支持。然而，该项目已经获得了相当大的关注，最近的[筹资](https://rome.tools/funding/#contribute)已经超过了其 10 万美元目标的四分之一以上。

如果罗马能成功实现目标，它可能会成为你唯一需要的工具。

## [esbuild](https://esbuild.github.io/)

不出所料，大多数 JavaScript 构建工具都是用 JavaScript 编写的。速度通常是可以接受的，但是编译后的应用程序总是更快。esbuild 是另一个 JavaScript 模块捆绑器，但它是用 [Go](https://golang.org/) 编写的。它声称:

*   比 Rollup.js 快 100 倍
*   比 Webpack 5 快 173 倍
*   比包裹 2 快 294 倍

使用默认设置、缩小和源地图创建十个 [three.js](https://threejs.org/) 副本的[生产包](https://esbuild.github.io/faq/#benchmark-details)的时间:

![esbuild build time comparisons](img/21676f722b75b9ce8d707fef8d27558c.png)

esbuild 在没有缓存的情况下实现了这一速度，它仍然支持 ES6 模块、CommonJS 模块、TypeScript、JSX、树抖动、源地图、缩小、[插件](https://esbuild.github.io/plugins/)、Node.js 捆绑、完整的 API 等等。

Evan Wallace 是 esbuild 的唯一主要开发者，1.0 版本尚未发布。这可能会引起开发关键任务应用程序的团队的警觉，但是更新已经很快到达了。关注这个项目。

## [航路点](https://www.waypointproject.io/)

向主机发布您的生产站点仍然是一个挑战。有些提供基于 Git 的构建系统。其他人使用集装箱化过程。许多都有自己怪异而奇妙的术语和技术。假设你成功地解决了 AWS 构建过程的复杂性，你会因为老板或客户的一时兴起而转向 Azure 吗？

[Waypoint](https://www.waypointproject.io/) 对发布流程进行抽象，以提供跨任何平台构建、部署和发布的一致工作流。部署只需要一个命令:

```
waypoint up 
```

Waypoint 是一个开源项目，目前支持 JavaScript、Ruby、Python、Go 和。NET 项目、Amazon ECS、Google Cloud Run、Azure Container 实例、Docker、Buildpacks 等等。它是可扩展的，一个插件系统允许它与任何工具或平台一起工作。成功部署后，Waypoint 提供对日志、监视器和其他流程的完全访问，以管理您的应用程序。

Waypoint 于 2020 年年中发布，但使用量看起来将在 2021 年爆发。这个网站很棒，当你滚动的时候会显示终端命令。即使您对部署不感兴趣，也值得一看！

## 奖励工具

这里有几个可能在 2021 年达到临界质量的工具…

### 第十一次

Eleventy 是由 Netlify 的 [Zach Leatherman](https://twitter.com/zachleat) 开发的 Node.js 静态站点生成器。它简单、快速，已经被 web 行业中的许多重要人物所采用。而且还是没到 1.0 版本。当里程碑发布时，商业使用量会大幅增长。

有关更多信息，请参见[十一项](https://www.sitepoint.com/getting-started-with-eleventy/)入门。

### [Deno](https://deno.land/)

Deno 是一个 JavaScript 运行时，使用 Chrome 的 V8 引擎。它由 Node.js 的创始人瑞安·达尔(Ryan Dahl)开发，并于 2020 年 5 月发布。本质上，这是一个十年后见之明的节点。

Deno 是新的，它消除了您在开发服务器端 JavaScript 时可能遇到的一些困难。首先，它增加了安全性，并选择了从 URL 导入的类似浏览器的 [ES6 模块](https://www.sitepoint.com/deno-modules/)，而不是由`npm`管理的 CommonJS 模块。模块版本在你的系统中只存储一次，所以没有必要在每个项目中有一个多兆字节的`node_modules`文件夹。

Deno 还提供了许多内置工具,因此不太需要第三方选项。它包括升级程序、帮助系统、读取-评估-打印循环(REPL)、依赖检查器、linter、代码格式化程序、测试运行程序、文档生成器、调试器、脚本捆绑器和平台安装程序。

最后，Deno 支持浏览器中的一些[API。最值得注意的有`fetch`、`window`、`URL`、`File`、`FileReader`，以及`load`、`unload`等事件。](https://doc.deno.land/builtin/stable)

Node.js 并没有消亡，Deno 也还没有席卷全球，但是对于运行时来说，2021 年将会是有趣的一年。

## 新年快乐

2020 年可能是不寻常的一年，但 JavaScript 继续呈指数级增长。如果我错过了你对 2021 年的预测，请告诉我。

## 分享这篇文章