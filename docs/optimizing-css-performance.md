# 优化 CSS 性能的 20 个技巧

> 原文：<https://www.sitepoint.com/optimizing-css-performance/>

在这篇文章中，我们看看 20 种优化你的 CSS 的方法，让它加载更快，操作更简单，效率更高。

根据最新的 [HTTP Archive 报告](https://httparchive.org/reports/page-weight?start=latest)，网络仍然是一个臃肿的混乱，神话般的中值网站需要 1,700Kb 的数据，分为 80 个 HTTP 请求，需要 17 秒才能完全加载到移动设备上。

[减轻页面重量的完整指南](https://www.sitepoint.com/complete-guide-reducing-page-weight/)提供了一系列建议。在本文中，我们将集中讨论 CSS。诚然，CSS 很少是最糟糕的罪魁祸首，一个典型的网站使用 40KB 的五个样式表。也就是说，你仍然可以进行优化，改变我们使用 CSS 的方式来提高网站的性能。

## 1.学会使用分析工具

除非您知道错误在哪里，否则您无法解决性能问题。浏览器开发工具是最好的起点:在 macOS 上运行 Safari 时，从菜单启动或点击`F12`、`Ctrl` + `Shift` + `I`或`Cmd` + `Alt` + `I`。

所有的浏览器都提供了相似的功能，而且这些工具在性能很差的页面上会打开很慢！但是，最有用的选项卡包括以下内容…

**网络**选项卡显示下载时资产的瀑布图。为了获得最佳效果，请禁用缓存，并考虑降低网络速度。查找下载速度慢的文件或阻止其他文件。在下载和解析 CSS 和 JavaScript 文件时，浏览器通常会阻止浏览器渲染。

**性能**选项卡分析浏览器进程。开始记录，运行一个活动，如页面重新加载，然后停止记录以查看结果。寻找:

1.  过多的*布局/重排*动作，浏览器被迫重新计算页面元素的位置和大小。
2.  昂贵的*绘制*像素改变的动作。
3.  *合成*将页面的绘制部分放在一起显示在屏幕上的动作。这通常是占用处理器最少的操作。

基于 Chrome 的浏览器提供了一个**审计**标签，运行[谷歌的灯塔工具](https://developers.google.com/web/tools/lighthouse/)。它经常被进步的 Web 应用程序开发人员使用，但也提供 CSS 性能建议。

### 在线选项

或者，使用不受设备和网络速度和能力影响的在线分析工具。大多数人可以在世界各地的其他地方进行测试:

*   [Pingdom 网站速度测试](https://tools.pingdom.com/)
*   [GTmetrix](https://gtmetrix.com/)
*   [谷歌页面速度洞察](https://developers.google.com/speed/pagespeed/insights/)
*   [网页测试](https://www.webpagetest.org/)

## 2.先取得重大胜利

CSS 不太可能是性能问题的直接原因。但是，它可能会加载可以在几分钟内优化的重要资产。示例:

*   在服务器上激活 HTTP/2 和 GZIP 压缩
*   使用内容交付网络(CDN)增加同时 HTTP 连接的数量，并将文件复制到世界各地的其他位置
*   删除不用的文件。

图片通常是页面庞大的最大原因，然而许多网站未能有效优化:

1.  调整位图图像的大小。入门级智能手机将拍摄数百万像素的图像，这些图像无法在最大的高清屏幕上完整显示。很少有网站要求图像宽度超过 1600 像素。
2.  确保使用适当的文件格式。通常情况下，JPG 最适合照片，SVG 最适合矢量图像，PNG 最适合其他图像。你可以通过实验找到最合适的类型。
3.  使用图像工具通过条带化元数据和提高压缩系数来减小文件大小。

也就是说，要知道图像数据的 *x* Kb 是**而不是**等同于 CSS 代码的 *x* Kb。二进制图像是并行下载的，只需要很少的处理就可以放到页面上。CSS 会阻止呈现，并且必须被解析为对象模型，然后浏览器才能继续。

## 3.用 CSS 效果替换图像

很少需要使用背景图片来显示边框、阴影、圆形边缘、渐变和一些几何形状。使用 CSS 代码定义“图像”使用的带宽要少得多，并且以后更容易修改或制作动画。

## 4.删除不必要的字体

谷歌字体(Google Fonts)等服务使得在任何页面上添加自定义字体变得很容易。不幸的是，一两行代码可以检索数百千字节的字体数据。建议:

1.  只使用你需要的字体。
2.  仅加载您需要的粗细和样式—例如，roman，400 粗细，无斜体。
3.  尽可能限制字符集。Google fonts 允许你通过给字体 URL 添加一个`&text=`值来选择某些字符——比如在 Open Sans 中显示“SitePoint”的`fonts.googleapis.com/css?family=Open+Sans&text=SitePon`。
4.  考虑一下[可变字体](https://v-fonts.com/)，它通过插值定义多种粗细和样式，因此文件更小。目前对 T2 的支持仅限于 Chrome、Edge 和 Safari 的一些版本，但应该会迅速增长。参见[如何使用可变字体](https://www.sitepoint.com/variable-fonts-introduction/)。
5.  考虑操作系统字体。你的 500Kb 网络字体可能是品牌上的，但是如果你换成常用的 Helvetica 字体或 Arial 字体，会有人注意到吗？许多网站使用自定义的网络字体，所以标准的操作系统字体比以前少多了！

## 5.避免`@import`

at 规则允许任何 CSS 文件包含在另一个文件中。例如:

```
/* main.css */
@import url("base.css");
@import url("layout.css");
@import url("carousel.css"); 
```

这似乎是加载较小组件和字体的合理方式。*又不是*。规则可以嵌套，因此浏览器必须连续加载和解析每个文件。

HTML 中的多个`<link>`标签将并行加载 CSS 文件，这将大大提高效率——尤其是在使用 HTTP/2:

```
<link rel="stylesheet" href="base.css">
<link rel="stylesheet" href="layout.css">
<link rel="stylesheet" href="carousel.css"> 
```

也就是说，可能有更好的选择…

## 6.连接和缩小

大多数构建工具允许你将所有部分合并成一个大的 CSS 文件，去掉不必要的空格、注释和字符。

使用 [HTTP/2](https://www.sitepoint.com/what-is-http2/) 不太需要串联，它通过管道传输和多路复用请求。在某些情况下，如果您有较小的、经常变化的 CSS 资产，单独的文件可能是有益的。然而，大多数网站可能会从发送一个被浏览器立即缓存的文件中受益。

当你启用 GZIP 时，缩小可能不会带来很大的好处。也就是说，没有真正的负面影响。

最后，您可以考虑在声明中对属性进行一致排序的构建过程。当常用字符串在整个文件中使用时，GZIP 可以最大限度地压缩。

## 7.使用现代布局技术

多年来，使用 CSS `float`来布局页面是必要的。这项技术是个骗局。它需要大量的代码和空白/填充调整，以确保布局的工作。即使这样，如果不添加媒体查询，浮动也会在较小的屏幕上中断。

现代替代品:

*   [CSS Flexbox](https://www.sitepoint.com/flexbox-css-flexible-box-layout/) 用于一维布局，可以根据每个块的宽度换行到下一行。Flexbox 是菜单、图库、卡片等的理想选择。
*   [CSS 网格](https://www.sitepoint.com/introduction-css-grid-layout-module/)用于具有显式行和列的二维布局。网格是页面布局的理想选择。

这两个选项开发起来更简单，使用的代码更少，可以适应任何屏幕大小，并且渲染速度比 floats 更快，因为浏览器本身就可以确定最佳布局。

## 8.减少 CSS 代码

最可靠和最快的代码是您永远不需要编写的代码！样式表越小，下载和解析的速度就越快。

所有开发人员一开始都有良好的意图，但是随着时间的推移，CSS 会随着功能数量的增加而膨胀。保留旧的、不必要的代码比删除它们并冒着破坏的风险更容易。需要考虑的一些建议:

*   警惕大型 CSS 框架。你不太可能使用大部分的样式，所以只在你需要的时候添加模块。
*   将 CSS 组织成更小的文件(片段),有明确的职责。如果 CSS 在`widgets/_carousel.css`中有明确的定义，那么移除 carousel 小部件会更容易。
*   考虑命名方法，如 [BEM](http://getbem.com/) 来帮助分立元件的开发。
*   避免深度嵌套的 Sass/预处理器声明。扩展后的代码可能会变得出乎意料的大。
*   避免使用`!important`来覆盖级联。
*   避免 HTML 中的内嵌样式。

像 [UnCSS](https://github.com/uncss/uncss) 这样的工具可以通过分析你的 HTML 来帮助删除冗余代码，但是要警惕 JavaScript 交互导致的 CSS 状态。

## 9.抓住瀑布！

CSS-in-JS 的兴起使得开发者可以避开 CSS 全局名称空间。通常，随机生成的类名是在构建时创建的，因此组件不可能发生冲突。

如果 CSS-in-JS 改善了你的生活，那么继续使用它。然而，理解 [CSS 级联](https://developer.mozilla.org/en-US/docs/Web/CSS/Cascade)的好处是值得的，而不是在每个项目中与它作对。例如，您可以设置默认的字体、颜色、大小、表格和表单字段，这些都可以在一个地方通用于每个元素。很少需要在每个组件中声明每个样式。

## 10.简化选择器

即使是最复杂的 CSS 选择器也需要几毫秒来解析 T1，但是降低复杂度将会减小文件大小并有助于浏览器解析。你真的需要这种选择器吗？！

```
body > main.main > section.first h2:nth-of-type(odd) + p::first-line > a[href$=".pdf"] 
```

同样，要小心 Sass 等预处理程序中的深度嵌套，因为在那里可能会无意中创建复杂的选择器。

## 11.警惕昂贵的房产

有些属性的渲染速度比其他属性慢。为了增加简洁性，尝试在所有元素上放置方框阴影！

```
*, ::before, ::after {
  box-shadow: 5px 5px 5px rgba(0,0,0,0.5);
} 
```

浏览器的性能会有所不同，但一般来说，任何导致绘制前重新计算的操作在性能方面的代价都会更高:

*   `border-radius`
*   `box-shadow`
*   `opacity`
*   `transform`
*   `filter`
*   `position: fixed`

## 12.采用 CSS 动画

原生 CSS [过渡](https://developer.mozilla.org/en-US/docs/Web/CSS/transition)和[动画](https://developer.mozilla.org/en-US/docs/Web/CSS/animation)总是比修改相同属性的 JavaScript 驱动的效果更快。CSS 动画不能在 IE9 或更低版本的浏览器上运行，但是这些用户永远不会知道他们错过了什么。

也就是说，避免为了动画而动画。微妙的效果可以增强用户体验，而不会对性能产生负面影响。过多的动画可能会降低浏览器的速度，并导致某些用户晕车。

## 13.避免动画昂贵的属性

动画元素的尺寸或位置可以导致整个页面在每一帧上重新布局。如果动画只影响*合成*阶段，性能可以得到提高。最有效的动画使用:

*   `opacity`与/或
*   `transform`平移(移动)、缩放或旋转元素(不改变元素使用的原始空间)。

浏览器通常使用硬件加速的 GPU 来渲染这些效果。如果两者都不理想，考虑用`position: absolute`将元素从页面流中取出，这样它就可以在自己的层中被动画化。

## 14.指示哪些元素将被激活

[will-change 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)允许 CSS 作者指示一个元素将被动画化，这样浏览器可以提前进行性能优化。例如，声明一个元素将应用一个`transform`:

```
.myelement {
  will-change: transform;
} 
```

可以定义任意数量的逗号分隔的属性。然而:

*   使用`will-change`作为解决性能问题的最后手段
*   不要将它应用于太多的元素
*   给它足够的时间工作:也就是说，不要立即开始动画。

## 15.采用 SVG 图像

可缩放矢量图形(SVG)通常用于徽标、图表、图标和更简单的图表。SVG 不像 JPG 和 PNG 位图那样定义每个像素的颜色，而是在 XML 中定义线条、矩形和圆形等形状。例如:

```
<svg xmlns="https://www.w3.org/2000/svg" viewBox="0 0 800 600">
  <circle cx="400" cy="300" r="50" stroke-width="5" stroke="#f00" fill="#ff0" />
<svg> 
```

简单的 SVG 比等效的位图小，可以无限缩放而不会丢失清晰度。

SVG 可以作为背景图像直接内嵌在 CSS 代码中。这对于较小的、可重复使用的图标来说是理想的，可以避免额外的 HTTP 请求。例如:

```
.mysvgbackground {
  background: url('data:image/svg+xml;utf8,<svg  viewBox="0 0 800 600"><circle cx="400" cy="300" r="50" stroke-width="5" stroke="#f00" fill="#ff0" /></svg>') center center no-repeat;
} 
```

## 16.用 CSS 样式化 SVG

更常见的是，SVG 直接嵌入在 HTML 文档中:

```
<body>
  <svg class="mysvg" xmlns="https://www.w3.org/2000/svg" viewBox="0 0 800 600">
    <circle cx="400" cy="300" r="50" />
  <svg>
</body> 
```

这将 SVG 节点直接添加到 DOM 中。因此，可以使用 CSS 应用所有 SVG 样式属性:

```
circle {
  stroke-width: 1em;
}

.mysvg {
  stroke-width: 5px;
  stroke: #f00;
  fill: #ff0;
} 
```

嵌入的 SVG 代码量减少了，CSS 样式可以根据需要重用或制作动画。

注意，在一个`<img>`标签中使用 SVG 或者作为 CSS 背景图片意味着它们与 DOM 分离，CSS 样式没有任何效果。

## 17.避免 Base64 位图图像

标准位图 jpg、png 和 gif 可以在数据 URI 中编码为 base64 字符串。例如:

```
.myimg {
  background-image: url('data:image/png;base64,ABCDEFetc+etc+etc');
} 
```

不幸的是:

*   base64 编码通常比其等效的二进制编码大 30%
*   浏览器必须先解析字符串，然后才能使用它
*   更改图像会使整个(缓存的)CSS 文件失效。

虽然发出的 HTTP 请求越来越少，但它很少带来明显的好处——尤其是在 HTTP/2 连接上。通常，避免内联位图，除非图像不太可能经常改变，并且生成的 base64 字符串不太可能超过几百个字符。

## 18.考虑关键的 CSS

那些使用谷歌页面分析工具的人会经常看到对*“内联关键 CSS”*或*“减少渲染阻塞样式表”*的建议。加载 CSS 文件会阻止渲染，因此可以通过以下步骤提高性能:

1.  提取用于呈现折叠上方元素的样式。像[critical ss](https://github.com/filamentgroup/criticalCSS)这样的工具会有所帮助。
2.  将它们添加到 HTML `<head>`中的一个`<style>`元素中。
3.  使用 JavaScript 异步加载主 CSS 文件(可能在页面加载之后)。

这项技术无疑提高了性能，并可能有益于具有一致界面的渐进式网络或单页应用程序。其他网站/应用的收益可能不太明显:

*   要识别“折叠”是不可能的，而且它在每个设备上都是变化的。
*   大多数网站都有不同的页面布局。每一个都可能需要不同的关键 CSS，所以构建工具变得必不可少。
*   动态的、JavaScript 驱动的事件可以做出重要的 CSS 工具无法识别的改变。
*   这项技术主要有利于用户的第一次页面加载。CSS 被缓存用于后续页面，所以额外的内联样式会增加页面权重。

也就是说，谷歌会喜欢你的网站，并把它推上每个搜索词的榜首。(SEO“专家”可以引用我的话。其他人都会知道这是胡说八道。)

## 19.考虑渐进式渲染

**渐进式渲染**是一种为单独的组件定义单独的样式表的技术，而不是使用单个站点范围的 CSS 文件。在 HTML 中引用组件之前，会立即加载每个组件:

```
<head>

  <!-- core styles used across components -->
  <link rel='stylesheet' href='base.css' />

</head>
<body>

  <!-- header component -->
  <link rel='stylesheet' href='header.css' />
  <header>...</header>

  <!-- primary content -->
  <link rel='stylesheet' href='content.css' />
  <main>

    <!-- form styling -->
    <link rel='stylesheet' href='form.css' />
    <form>...</form>

  </main>

  <!-- header component -->
  <link rel='stylesheet' href='footer.css' />
  <footer>...</footer>

</body> 
```

每个`<link>`仍然阻止渲染，但是时间更短，因为文件更小了。页面可以更快地使用，因为每个组件都是按顺序呈现的；可以在加载剩余内容的同时查看页面顶部。

这种技术在 Firefox、Edge 和 IE 上都有效。Chrome 和 Safari *“优化”*通过加载所有 CSS 文件并在出现这种情况时显示白屏的体验——但这并不比在`<head>`中加载每个文件更糟糕。

渐进式渲染可以使大型网站受益，在这些网站中，各个页面是由不同的组件构成的。

## 20.学会爱上 CSS

最重要的提示:*理解你的样式表！*

从 StackOverflow 或 BootStrap 添加大量的 CSS 可能会很快产生结果，但是它也会用无用的垃圾来膨胀你的代码库。进一步的定制变得非常困难，应用程序将永远不会高效。

CSS 易学难精通。如果你想创建有效的客户端代码，你就不能回避这项技术。了解一点 CSS 基础知识可以彻底改变您的工作流程，增强您的应用程序，并显著提高性能。

我错过了你最喜欢的 CSS 性能技巧了吗？

## 分享这篇文章