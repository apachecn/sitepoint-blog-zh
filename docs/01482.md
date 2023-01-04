# AnyChart 入门— 10 个实用示例

> 原文：<https://www.sitepoint.com/getting-started-anychart-examples/>

如果你的网站是数据密集型的，那么你需要使数据易于可视化，通常是通过 JavaScript 图表库。然而，这样的库多如牛毛，它们都有稍微不同的功能，所以你怎么知道哪一个最适合你呢？

在这篇文章中，我将通过 10 个时尚而又易于实现的例子来展示 [AnyChart](https://www.anychart.com) 。我将展示 AnyChart 如何非常适合您的数据可视化需求，以及它可以轻松处理几乎任何格式的数据。

> 您可以在 CodePen 上的 [AnyChart 集合中找到本文中的所有 CodePen 示例。](http://codepen.io/collection/AkLENg/)

## 为什么任何图表都与众不同

以下几点说明了为什么 AnyChart 是图表领域的重要竞争者。他们远离营销，只是简单明了的事实。

### 任何图表都是公认的

AnyChart 是一个[商业图书馆](https://www.anychart.com/buy)，但它对任何非盈利用途都是免费的。这是一款非常成熟的产品，已经在[市场上销售了 10 多年](https://en.wikipedia.org/wiki/AnyChart)。AnyChart 最初基于 Flash，后来转移到纯 JavaScript，支持 SVG/VML 渲染。

AnyChart API 非常灵活，允许你在运行时动态地改变图表的任何方面。

### AnyChart 是一个产品系列

AnyChart 通常表示为一组 JS 图表库，或者——如果您愿意的话——一个产品系列。它包括以下内容:

*   [AnyChart](https://www.anychart.com/products/anychart/overview/) —设计用于创建所有基本类型的交互式图表
*   [AnyStock](https://www.anychart.com/products/anystock/overview/) —旨在可视化基于日期/时间的大型数据集
*   [AnyMap](https://www.anychart.com/products/anymap/overview/) —用于地理地图和[座位地图](http://www.anychart.com/products/anymap/gallery/Seat_Maps/)
*   [任意甘特图](https://www.anychart.com/products/anygantt/overview/) —用于项目和资源管理解决方案(甘特图、资源图、PERT 图)

然而，这些库可以被视为一个大的 JavaScript (HTML5)图表库。它们都共享相同的 API，所有的图表都以几乎相同的方式配置，它们共享共同的[主题](https://docs.anychart.com/latest/Appearance_Settings/Themes)、[设置](https://docs.anychart.com)和[加载数据的方式](https://docs.anychart.com/Working_with_Data/Overview)。

### 任何图表都是打开的

今年早些时候，AnyChart [为这些库开放了源代码](https://www.anychart.com/blog/2017/02/28/anychart-javascript-charting-libraries-7-13-0-released-source-code-opened/)。这里需要指出的是，AnyChart 并没有完全开源——没有引入 Apache、MIT 或任何其他类似的许可。但是如果你正在为一个长期项目选择一个库，这仍然是一个好消息。此外，AnyChart 的渲染基于完全开源的 JavaScript 库 [GraphicsJS](http://www.graphicsjs.org) ，由 AnyChart 维护，但对任何社区请求和修改开放。你可以在[这篇 SitePoint 文章](https://www.sitepoint.com/introducing-graphicsjs-a-powerful-lightweight-graphics-library)中了解更多关于 GraphicsJS 的信息。

作为 AnyChart 的 R&D 负责人，我可以花一整天来谈论这个图书馆，但现在是时候进入正题了。

## 启动和运行:快速启动 AnyChart

要开始在 HTML 页面中使用 AnyChart，您只需要做三件简单的事情。前两个包括一个到库的 JavaScript 文件的链接，并提供一个块级 HTML 元素。下面是一个您可以使用的 HTML 模板示例:

```
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <style> html, body, #container {
        width: 100%;
        height: 100%;
      } </style>
    <title>AnyChart Basic Example</title>
  </head>
  <body>
    <div id="container"></div>

    <script src="https://cdn.anychart.com/js/latest/anychart-bundle.min.js"></script>
    <script> // AnyChart code here </script>
  </body>
</html> 
```

第三步是添加 JavaScript 代码，创建一个简单的交互式单系列柱形图:

```
anychart.onDocumentLoad(function() {
  // create chart and set data
  var chart = anychart.column([
    ["Winter", 2],
    ["Spring", 7],
    ["Summer", 6],
    ["Fall", 10]
  ]);
  // set chart title
  chart.title("AnyChart Basic Sample");
  // set chart container and draw
  chart.container("container").draw();
}); 
```

这就是全部了！

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [AnyChart 基本样本](https://codepen.io/SitePoint/pen/zwpOGM/)。