# 理解 JavaScript 模块:捆绑和传输

> 原文：<https://www.sitepoint.com/javascript-modules-bundling-transpiling/>

*这篇文章由[丹普林斯](https://www.sitepoint.com/author/dprince)和[拉维基兰](https://www.sitepoint.com/author/rkiran/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

大多数人认为模块、依赖管理和动态加载是任何现代编程语言的基本要求——这些是 2015 年 JavaScript 中添加的一些最重要的功能。

模块在 Node 中被广泛使用，但我们在这里的重点是如何在浏览器中使用模块。我们将探索一点历史，在危险的当前环境中导航，最终目标是有一个清晰的前进道路，并欣赏当今最重要的 JavaScript 模块捆绑器:Browserify、Webpack 和 jspm。

最后，我们将看看如何在 CoffeeScript、TypeScript 和 Babel 等 transpilers 中使用这些工具。

## 历代模块

JavaScript 从 1995 年就存在了，直到今天还没有浏览器本身支持模块。Node 和 CommonJS 创建于 2009 年，npm 中的绝大多数包都使用 CommonJS 模块。

Browserify 于 2011 年发布，为浏览器带来了 CommonJS 模块，允许客户端 JavaScript 对 npm 包进行处理。该工具将所有必需的依赖项打包到一个 JavaScript 文件中。

### 过去

像 jQuery 这样的库将`$`添加到全局范围或`window`中。

```
window.$ = function() { ... }; 
```

我们将一个脚本包含到一个库中，并使用它所公开的全局对象。

```
<script src="jquery.js"></script>
<script>
$(function() { ... });
</script> 
```

你自己的应用程序代码通常被命名在一个像`App`这样的全局名字空间下，以防止污染全局范围。没有这个，你很快就会有名字冲突，事情就会分崩离析。

```
var App = {};
App.Models = {};
App.Models.Note = function() {}; 
```

### 未来

库以通用模块格式(ES6 模块)导出对象。

```
export default function $() { ... } 
```

我们将一个模块导入一个局部作用域并使用它。

```
import $ from 'jquery';

$(function() { ... }); 
```

*   不需要全局👍
*   源顺序独立性
*   接触国家预防机制
*   无需命名您自己的应用程序代码
*   根据需要随时动态加载模块

### 现在

真的*真的*复杂。首先，有多种模块格式可供使用:

*   [CommonJS](http://wiki.commonjs.org/wiki/Modules)
*   [AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)
*   [UMD](https://github.com/umdjs/umd)
*   [ES6 模块](http://www.2ality.com/2014/09/es6-modules-final.html#an_overview_of_the_es6_module_syntax)

捆绑资产的工具有多种形状和大小:

*   [浏览确认](http://browserify.org/)
*   [jspm](http://jspm.io/)
*   [网络包](http://webpack.github.io/)
*   [Rollup](http://rollupjs.org/)
*   [早午餐](http://brunch.io/) / [布罗科利](http://broccolijs.com/)
*   [链轮](https://github.com/rails/sprockets)
*   用[大口](http://gulpjs.com/) / [咕噜](http://gruntjs.com/)建造自己的

然后是你可能想要使用的传输器:

*   [巴别塔](https://babeljs.io/)为 ES6
*   [咖啡脚本](http://coffeescript.org/)
*   [打字稿](http://www.typescriptlang.org/)

此外，还有各种允许动态加载模块的库:

*   [Require.js](http://requirejs.org/)
*   [System.js](https://github.com/systemjs/systemjs)

这些是目前正在使用的流行工具的简短列表——对于初学者和专家来说都是一个雷区。[trans piling 的成本](https://github.com/samccone/The-cost-of-transpiling-es2015-in-2016)也凸显了你可以混合搭配很多这样的工具，得到不同的结果。

## 让我们在 2016 年巩固工装

前端开发人员使用构建工具已经有很长一段时间了，但只是在最近几年，我们才看到构建步骤成为规范。像 Sass 和 CoffeeScript 这样的工具帮助预处理成为主流，但围绕 ES6 的势头现在已经让每个人都参与进来了。

> JavaScript 社区在 2015 年做了一些很大的改进，但我们需要在 2016 年整合工具。[https://t.co/HGrLjiSQhb](https://t.co/HGrLjiSQhb)—尼古拉斯·贝瓦夸(@ nzgb)[2016 年 1 月 8 日](https://twitter.com/nzgb/status/685475705149329408)