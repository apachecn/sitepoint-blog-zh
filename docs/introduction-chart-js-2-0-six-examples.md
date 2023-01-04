# Chart.js 2.0 简介—六个简单的例子

> 原文：<https://www.sitepoint.com/introduction-chart-js-2-0-six-examples/>

> 本文收录于我们的文集[现代 JavaScript](https://www.sitepoint.com/premium/books/modern-javascript) 。如果你想在一个地方获得所有现代 JavaScript 的速度，注册 SitePoint Premium 并下载一份。

*这篇文章由[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)和[西蒙·科灵顿](https://www.sitepoint.com/author/scodrington/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

如果你的网站是数据密集型的，那么你需要找到一种方法使数据易于可视化。毕竟，人类并不擅长理解一长串原始数字。这就是图表和图形的用武之地——它们可以让复杂的统计关系变得明显而直观，也更容易被非英语人士理解。每个人理解基本图表的速度是一样的，但对于充斥着专业术语的段落就不一样了。在有益的时候使用图表，会让你的网站更容易理解，视觉上更有吸引力。

在本文中，我将向您介绍一个名为 [Chart.js](http://www.chartjs.org/) 的 JavaScript 图表库。我将使用六个时髦的例子来演示如何使用 Chart.js 来可视化网站上的数据，以及如何配置它来满足您的需求。

## 为什么选择 Chart.js？

我选择 Chart.js 是因为它可以快速学习和利用。它在设计时考虑到了简单性，但非常具有可定制性。根据我的经验，图表库属于复杂的范围，更复杂的库提供更深层次的定制，但是有更陡峭的学习曲线。Chart.js 是学习起来最快最容易的库之一，不会严重限制您的选择。它提供了八种不同的图表类型，可以满足您几乎所有的数据可视化需求。

Chart.js 被开源社区积极地维护到一个高标准。它最近发布了 2.0 版本，对语法做了一些基本的修改，使代码更加一致，并提供移动支持。在本文中，我将使用 Chart.js 2.0 及其更新的语法。在本文的最后，在让您有机会了解 Chart.js 2.0 如何工作之后，有一节介绍了 1.0 -> 2.0 的过渡以及在线阅读旧的 Chart.js 示例时会遇到什么情况。

## 安装 Chart.js

同样，Chart.js 专注于简单。易于学习、易于利用、易于安装。如果你想深入实际代码，[请查看 GitHub 项目](https://github.com/chartjs/Chart.js)。

使用 Chart.js 只需要两样东西。

1)图书馆——对于本指南，我推荐使用 CDN，因为这是快速启动和运行的最简单方式。

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.1.4/Chart.min.js"></script> 
```

2)一个`<canvas>`元素，因为 Chart.js 利用了 HTML5 canvas。

```
</canvas><canvas id="myChart"></canvas> 
```

或者，您可以使用包管理器来下载库。有关更多信息，请参见[入门](http://www.chartjs.org/docs/#getting-started)指南。

简单，嗯？现在，事不宜迟，让我们看看 Chart.js 提供了什么。

## 折线图

这就是在 Chart.js 中创建一个最小折线图所需要的全部内容。只需在声明 HTML5 画布的之后，将它放入`<body>` **中的`<script></script>`中。**

```
var ctx = document.getElementById('myChart').getContext('2d');
var myChart = new Chart(ctx, {
  type: 'line',
  data: {
    labels: ['M', 'T', 'W', 'T', 'F', 'S', 'S'],
    datasets: [{
      label: 'apples',
      data: [12, 19, 3, 17, 6, 3, 7],
      backgroundColor: "rgba(153,255,51,0.4)"
    }, {
      label: 'oranges',
      data: [2, 29, 5, 5, 2, 3, 10],
      backgroundColor: "rgba(255,153,0,0.4)"
    }]
  }
}); 
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen[2-折线图](http://codepen.io/SitePoint/pen/WGZGNE/)。