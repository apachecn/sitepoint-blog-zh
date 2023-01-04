# 了解 CSS 模块方法

> 原文：<https://www.sitepoint.com/understanding-css-modules-methodology/>

在前端开发不断变化的世界中，很难找到一个真正有所作为的概念，并足够正确地介绍它，以便其他人实际上愿意尝试它。

当我们看 CSS 时，我会说我们编写 CSS 的方式(在工具方面)发生的最后一个重大转变是 CSS 处理器的到来，主要是 [Sass](http://sass-lang.com/) ，可能是最广为人知的一个。还有 [PostCSS](https://github.com/postcss) ，它有一个稍微不同的方法，但最终本质上在同一个篮子里——浏览器不支持的语法进去，浏览器支持的语法出来。

现在一个新成员以 CSS 模块的名字加入了这个模块。在这篇文章中，我想介绍这种技术，告诉你为什么它有一些优点，以及你如何开始使用它。

## 什么是 CSS 模块？

让我们从官方知识库的解释开始:

> 一个 **CSS 模块**是一个 CSS 文件，默认情况下所有的类名和动画名都在本地范围内。

现在，它比那要复杂一点。因为默认情况下类名*的作用域是局部的*，所以这涉及到一些设置、构建过程和一些有时难以掌握的魔法。

但最终，我们可以欣赏 CSS 模块的本来面目:一种将 CSS 作用于组件并逃离全局命名空间地狱的方法。不要再努力寻找一个好的方法来命名你的组件，因为构建步骤已经为你做好了！

## 它是如何工作的？

CSS 模块需要在构建步骤中通过管道传输，这意味着它们不能独立工作。将它视为 [webpack](https://webpack.github.io/) 或 [Browserify](http://browserify.org/) 的插件。它基本上是这样工作的:当你在一个 JavaScript 模块中导入一个 CSS 文件时(比如，但不一定，一个 [React](https://facebook.github.io/react/) 组件)，CSS 模块将定义一个对象，将类名从文件映射到动态范围/命名空间的类名，这些类名可以在 JavaScript 中用作字符串。请允许我举一个例子来说明:

下面是一个非常简单的 CSS 文件。`.base`类在项目中不必是唯一的，因为它不是将要呈现的实际类名。它只是要在 JavaScript 模块中使用的样式表中的一种别名。

```
.base {
  color: deeppink;
  max-width: 42em;
  margin: 0 auto;
}
```

下面是如何在虚拟 JavaScript 组件中使用它:

```
import styles from './styles.css';

element.innerHTML = `<div class="${styles.base}">
  CSS Modules are fun.
</div>`;
```

最终，这将生成如下内容(在默认设置下通过 webpack 使用它时):

```
<div class="_20WEds96_Ee1ra54-24ePy">CSS Modules are fun.</div>
```

```
._20WEds96_Ee1ra54-24ePy {
  color: deeppink;
  max-width: 42em;
  margin: 0 auto;
}
```

生成类名的方式可以配置为使它们更短，或者遵循特定的模式。这最终并不重要(尽管更短的类名意味着更短的样式表)，因为关键是它们是动态生成的、唯一的，并且映射到正确的样式。

## 可能合理的担忧

这就是它的工作原理。而现在，你一定在想“这到底是什么东西？我甚至不……”。好吧，坚持住！我听到了。让我们逐一解决这些问题，好吗？

看起来真他妈的丑！

这是真的。但是类名并不是为了好看；它们旨在将样式应用于元素。这正是他们所做的，所以我认为这不是一个有效的关注。

***调试起来很痛苦！***

一旦有了对样式表进行处理的构建步骤，调试就成了一件痛苦的事情。萨斯并不容易。这就是为什么我们有 [sourcemaps](https://www.sitepoint.com/using-source-maps-debug-sass-chrome/) ，你也可以为 CSS 模块设置它。

实际上，我认为尽管类名是不可预测的，但是调试起来并不困难，因为样式，根据定义，是特定于模块的。如果您知道您在开发工具中检查的是什么模块，您就知道在哪里可以找到附加的样式。

它使样式不可重复使用！

是也不是。一方面，是的，但这实际上是重点:它将样式绑定到一个组件，以避免全局样式泄漏和冲突。这是一件好事，我相信你会承认。

另一方面，仍然可以定义全局类(用`:global()`)，比如保留为作者的助手，使得风格抽象像往常一样容易。然后可以在 JavaScript 组件中使用这些类。

```
:global(.clearfix::after) {
  content: '';
  clear: both;
  display: table;
}
```

CSS 模块也有从另一个模块扩展样式的方法，这基本上与 Sass 中的`@extend`工作方式相同。它不复制样式，而是连接选择器来扩展样式。

```
.base {
  composes: appearance from '../AnoherModule/styles.css';
}
```

***需要 webpack，Browserify 或者【随便什么】工具！***

这与 Sass 需要将`.scss`文件编译成实际的 CSS 是一样的，PostCSS 需要处理样式表以使它们与浏览器理解的样式兼容。无论如何，构建步骤已经存在了。

为什么我们还要讨论这个？

嗯，我不完全确定将来 CSS 模块会保持现在的样子，但是我确实认为这是一种编写样式的明智方式。拥有一个大规模的全局样式表并不适合用小组件分离的大型站点。

CSS 的独特命名空间使它既强大又非常脆弱。这种解决方案，无论是 CSS 模块还是任何基于这种思想的未来工具，都允许我们保留与全局类共享样式的优势，同时轻松避免与作用域样式的命名冲突。双赢。

## 入门指南

如上所述，您将需要 webpack 或 Browserify 来启用 CSS 模块。

### 网络包

让我们从 webpack 版本开始。在`webpack.config.js`中，添加以下配置，告诉 webpack 用 CSS 模块处理 CSS 文件:

```
{
  test: /\.css$/,
  loader: 'style-loader!css-loader?modules'
}
```

现在，它将在页面的`<style>`元素中注入样式。这可能不是您想要的，所以让我们借助 webpack 的[提取文本插件来制作一个合适的样式表:](https://github.com/webpack/extract-text-webpack-plugin)

```
{
  test: /\.css$/,
  loader: ExtractTextPlugin.extract('style-loader', 'css-loader?modules')
}
```

对 webpack 来说差不多就是这样了。

### 浏览

我只在命令行中使用过 Browserify，所以我想这有点复杂。我所做的是将一个 [npm 脚本](https://docs.npmjs.com/misc/scripts)添加到我的`package.json`文件中，就像这样:

```
{
  "scripts": {
    "build": "browserify -p [ css-modulesify -o dist/main.css ] -o dist/index.js src/index.js"
  }
}
```

这告诉 Browserify 在`src/index.js`上运行，输出`dist/index.js`，并通过[CSS-modulesize](https://github.com/css-modules/css-modulesify)插件在`dist/main.css`编译一个样式表。如果你想给它添加 [Autoprefixer](https://github.com/postcss/autoprefixer) ，你可以这样完成命令:

```
{
  "scripts": {
    "build": "browserify -p [ css-modulesify --after autoprefixer -o dist/main.css ] -o dist/index.js src/index.js"
  }
}
```

如您所见，您可以使用`--after`选项来处理编译后的样式表。

## 结论

到今天为止， [CSS 模块](https://github.com/css-modules/css-modules)系统和生态系统还有点原始，你可以从浏览配置中看到。但我相信，随着越来越多的人意识到这是一个适用于小型到大型项目的可持续解决方案，情况会越来越好。

我认为 CSS 模块背后的想法是正确的。我并不是说这个库是最好的解决方案，但是它确实有一些关于 CSS 应该如何编写的特性:模块化，有作用域，但是仍然可以重用。

作为进一步的阅读，我推荐这个项目的创建者 Glen Maddern 的 CSS 模块介绍。

## 分享这篇文章