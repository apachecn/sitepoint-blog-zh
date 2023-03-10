# CSS 预处理程序的 6 个当前选项

> 原文：<https://www.sitepoint.com/6-current-options-css-preprocessors/>

CSS 预处理器帮助你编写可维护的、经得起未来考验的代码，它将大大减少你必须编写的 CSS 的数量。这些工具最出色的地方是需要巨大样式表和许多样式规则的大规模用户界面。

这是对目前最好的六个 CSS 预处理程序的回顾。

## [1。萨斯](http://sass-lang.com/)

[![Sass](img/5961477c8cc352dc8334758b9a007121.png)](http://sass-lang.com/)

我们将从最广为人知的选项开始本文。需要 Ruby 的 Sass 可能是您最熟悉的 CSS 预处理器。作为一个已经有八年历史的开源项目，它已经得到了很好的确立，并且它确实是定义现代 CSS 预处理程序类型的一个项目。对于在之前在 SitePoint 上还没有说过的[的无礼行为，我没有太多可说的，但我会在这里为那些对这个主题不熟悉的人做一个快速概述。](https://www.sitepoint.com/?s=sass)

这个 CSS 预处理程序有两种语法可以使用:

*   **Sass:** 原语法

*   **SCSS:** 一种与原生 CSS 非常相似的新语法

在 **Sass** 语法中，这是您定义和使用变量的方式:

```
$serif-font-stack: "Georgia", "Times New Roman", serif
$monospace-font-stack: "Cousin", "Courier"

body
 font: normal 18px/22px $serif-font-stack

pre, code
 font: 600 bold 18px/22px $monospace-font-stack
```

这是等效的 **SCSS** 语法:

```
$serif-font-stack: "Georgia", "Times New Roman", serif;
$monospace-font-stack: "Cousin", "Courier";

body {
 font: normal 18px/22px $serif-font-stack;
}

pre, code {
 font: 600 bold 18px/22px $monospace-font-stack;
}
```

您将看到新的语法(已经成为许多有经验的 Sass 开发人员最推荐的选项)与自然 CSS 更加相似。关于语法，我们还可以说更多，但之前已经说了很多，我鼓励你查看文档以了解更多细节。

*   [Sass 文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)

## [2。Less.js](http://lesscss.org/)

[![Less.js](img/bc5c5466e13a9a30ac00d9817af63edd.png)](http://lesscss.org/)

Less.js(通常简称为“Less”)是最流行的 CSS 预处理器之一，可能是目前 Sass 最强的竞争对手。Less 通过混合、变量、嵌套样式规则甚至规则集循环(其行为类似于编程语言中的`for`循环)彻底扩展了 CSS 语法。

CSS 预处理程序中的一个常见特性是 mixin，也就是将一个规则集的属性混合到另一个规则集中的能力(T2)。用更简单的语法来说，这就是如何将一个名为`.button`的类中的属性混合到另一个名为`.button-checkout-process`的类中:

```
.button {
    display: inline-block;
    width: 80%;
    max-width: 200px;
    border-radius: 5px;
    background-color: black;
    color: white;
    font-size: 14px;
    margin: 5px;
    padding: 8px;
}

.button-checkout- process {
    .button(); /* Mixin */
    background-color: silver;
}
```

少容易成立。如果您只是想玩玩它，而不想为生产而安装和配置它，只需下载 Less.js JavaScript 文件，并在您的 HTML 文档中引用它(或者使用您最喜欢的公共 CDN 中的[，创建一个`.less`文件，然后在 Less.js 脚本后引用它，就可以了:](http://sixrevisions.com/resources/free-public-cdns/)

```
<head>
  <!-- Reference Less JavaScript file -->
  <script src="path/to/your/less.js"></script>
  <link rel="stylesheet/less" type="text/css" href="your/styles.less" /> 
</head>
```

我刚刚描述的 Less 实现方法非常适合在浏览器中开发。又快又省事。但是——这是非常重要的一点——一旦您准备好部署在生产 web 服务器上，您应该将您的`.less`文件预编译成常规的`.css`文件以提高 Web 性能。方法是通过 Node 的命令行界面。例如，要将`styles.less`编译成一个名为`styles.css`的文件，并缩小 CSS 输出，您可以发出以下命令:

```
lessc styles.less -x styles.css
```

*   [Less.js 文档](http://lesscss.org/features/)

## [3。手写笔](http://learnboost.github.io/stylus/)

[![Stylus](img/5433c2450de8715fb7637c1f72673d70.png)](http://learnboost.github.io/stylus/)

Stylus 是 Sass 的另一个强有力的竞争对手，是 CSS 功能丰富的扩展。它有超过 60 个自定义函数，包括`saturation()`，让你用数学方法调整 CSS 颜色的饱和度。

手写笔语法还允许您省略大括号(`{}`)、冒号(`:`)和分号(`;`)，或者您可以使用普通 CSS。

下面是手写笔语法的一个示例:

```
.button
    display inline-block
    border-color saturation(#000, 50%)
    border-radius 5px
```

上面的例子将编译成这样:

```
.button {
    display: inline-block;
    border-color: #959595;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    border-radius: 5px;
}
```

*   [手写笔互动游乐场](http://learnboost.github.io/stylus/try.html)
*   [手写笔文档](http://learnboost.github.io/stylus/)

## [4。CSS-Crush](http://the-echoplex.net/csscrush/)

[![CSS-Crush](img/896e3d177a2b2fc1872a4a14b3a00aa4.png)](http://the-echoplex.net/csscrush/)

CSS-Crush 拥有现代 CSS 预处理器的所有特性:自动供应商前缀、变量、数学运算、通过嵌套规则集的属性继承等等。

CSS-Crush 的独特之处在于它是用 PHP 实现的，这也是促使你选择它作为预处理程序的原因。众所周知，PHP 是一种流行的脚本语言，几乎已经在所有 web 主机上配置，并且是 WordPress、Magento 和 Drupal 等流行网站平台上使用的本地语言。与其他预处理程序不同，CSS-Crush 不需要配置 Node.js 或 Rails 就能工作，这使它成为许多开发人员的方便选择，因为它很容易集成到现有的开发堆栈中。

要使用 CSS-Crush，您需要将其包含在适当的 PHP 文件(或模板系统)中，如下所示:

```
<?php require_once 'path/to/CssCrush.php'; ?>
```

然后，可选地，您可以配置 CSS-Crush 以您想要的方式工作。例如，假设您不想缩小您的 CSS(默认情况下，缩小是启用的)。引用`CSSCrush.php`文件后，您可以调用以下带有适当参数的函数来建立您的首选项:

```
<?php csscrush_set(minify, false); ?>
```

要定义变量，您将使用`@set`指令。假设您想要定义一些设备宽度。下面是如何使用 CSS-Crush 语法实现这一点:

```
@set {
 smartphone-width: 320px;
 tablet-width: 768px;
 laptop-width: 1680px;
 4k-monitor-width: 3840px;
}
```

声明变量后，您可以在整个 CSS 中引用它们。下面是一个媒体查询示例，它使用了前面代码示例中显示的`smartphone-width`变量:

```
@media only screen and (min-device-width: smartphone-width) {
    h1 {
        font-size: 24px;
    }
}
```

当你需要改变内容时——例如，你可能决定你现在想要`smartphone-width`变成 380px 而不是 320 px——你只需要在`@set`指令中改变一次宽度值，而不是整个 CSS 文件。

*   [CSS-Crush 文档](http://the-echoplex.net/csscrush/#api)

## [5。神话](http://www.myth.io/)

[![Myth](img/cefaaab2cfb6013bbd9c524356a42c84.png)](http://www.myth.io/)

Myth 需要 Node.js，它让您今天就可以使用未来的 CSS 语法，而不必等待 web 浏览器实现新提议的 CSS 功能，也不必等待 W3C 完成该功能的规范。这意味着当现代浏览器最终支持该特性时，您不必重写代码。

例如，`calc()`属性表达式的当前状态是模糊的。有一份报告称，它在今天使用的 78%的浏览器中得到支持，但是处于候选推荐(CR)状态的 W3C 规范[称](https://www.w3.org/TR/css3-values/)它“有风险，可能会被放弃。”那么我们能/应该用还是不用呢？答案是不确定的。

但是，有了神话，我们可以使用`calc()`而不用担心规范状态和浏览器支持。Myth 将处理所有必要的代码，以使属性表达式在浏览器中发挥作用。例如，这里有一个 W3C 有效的使用`calc()`的方法:

```
.container {
    margin: calc(7px*2); 
}
```

神话将上面的例子编译成:

```
.container {
    margin: 14px;
}
```

神话的伟大之处在于你不需要学习新的语法。它使用现有的和提议的 W3C 标准 CSS 特性。

*   [神话文献](https://github.com/segmentio/myth/blob/master/Readme.md)

## [6。返工](https://github.com/reworkcss/rework)

返工是给我们当中的铁杆，有眼光的前端工程师做的。如果你想开发自己的 CSS 预处理器，你可以使用它。神话(前面讨论过)是在返工开源项目之上开发的。

或许你不喜欢手写笔语法，或许你不喜欢为 CSS-Crush 的自定义`math()`函数提供 CSS 单位参数，或许你不喜欢 Sass 让你使用`$`符号定义变量，你宁愿使用`#`。也许您只需要几个特性，并且不想忍受现有预处理程序的特性膨胀。

Rework 是一个建立在 Node.js 上的 CSS 预处理框架。它有一个[很棒的助手插件集](https://github.com/reworkcss/rework#plugins)用于你可能想要预处理的东西。因此，在编写自己的解析函数之前，您可能想先了解一下它们，以避免重新发明轮子。

*   [返工文件](https://github.com/reworkcss/rework#rework-)

## 其他 CSS 预处理程序选项

下面是 CSS 预处理程序的一些其他的、更难理解的选项。我没有深入研究这些，所以您必须自己检查它们，看看它们是否适合您的需要。

*   Clay :使用 Haskell 创建的 CSS 预处理器
*   [DtCSS](https://code.google.com/p/dtcss/) :将 DtCSS 语法解析成 CSS 的 PHP 脚本
*   [CSS 预处理器](https://pornel.net/css):PHP 5 中实现的预处理器。这一个是旧的并且看起来不活跃。
*   [切换 CSS](http://switchcss.sourceforge.net/) :运行在 Apache 上的 CSS 预处理器(需要安装并启用 mod_python)

## 你用什么 CSS 预处理器？

您是否使用过上述选项之一？你对使用它们有什么建议吗？或者你可能知道另一个这里没有列出的。我们很乐意在评论中听到你的想法。

## 分享这篇文章