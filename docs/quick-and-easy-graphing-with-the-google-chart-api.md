# 使用 Google Chart API 快速简单地绘制图形

> 原文：<https://www.sitepoint.com/quick-and-easy-graphing-with-the-google-chart-api/>

在现有的所有吸引眼球的创建网站的工具中，很少有人拥有一个好的图表的优雅和简洁。对我来说不幸的是，我已经很久没有机会在网上使用图表了，所以当最近的一个项目让我为一个 JavaScript 应用程序构建报表基础结构时，我非常兴奋地想看看现在有哪些图表构建工具可用。

尽管我很兴奋，但有一个问题，时间。我需要在几天内得到这些报告的工作原型，所以我评估无数图书馆的机会是有限的。更重要的是，我需要确保我使用的任何库都是相当健壮和轻量级的，没有隐藏的缺陷，比如浏览器兼容性问题。所以当我偶然发现[谷歌图表 API](https://code.google.com/apis/chart/) 时，我再高兴不过了。

Google Chart API 背后的前提很简单——你所需要的只是一个 URL。

就一个你问的网址？是的，有一两个警告。url 看起来有点像这样:

```
https://chart.apis.google.com/chart?cht=p3&chd=t:20,30,40&chs=275x100&chl=Puppies|Kittens|Trogdor&chco=40cae7|408cc6|7ec0b4
```

它给出了如下图表:

![Example Pie Chart](img/1bb2ea3393d6bc285f412388efc18e13.png)

乍一看，这看起来不像是最令人愉快的 URL，但是稍加挖掘，它实际上揭示了自己是一个相当优雅的解决方案。它是这样工作的:

*   查询字符串的每个参数都设置了图表中自己的特定部分。例如，它说`cht=p3`，实际上它说的是“给我一个 3D 饼图。”

*   下一部分`chd=t:20,30,40`设置数据格式并为图表提供数据。您可以生成非常个性化的图表，有许多不同类型的图表可用，从简陋的[条形图](https://code.google.com/apis/chart/types.html#bar_charts)到更复杂的[雷达图](https://code.google.com/apis/chart/types.html#radar)，甚至[地图](https://code.google.com/apis/chart/types.html#maps)。

你已经选好了一张图表，并准备好了数据。如何生成这些 URL 之一呢？

嗯，有很多流行语言的库可以执行这个任务，比如 [PHP](https://code.google.com/p/gchartphp/) 、 [Python](https://code.google.com/p/google-chartwrapper/) 、 [Rails](https://code.google.com/p/google-charts-on-rails/) 和[等等。](https://groups.google.com/group/google-chart-api/web/useful-links-to-api-libraries)就我个人而言，我最终用 JavaScript 编写了自己的定制解决方案，你可以在[City West Water-Target 155 用水计算器](https://www.citywestwater.com.au/calculator/)网站上看看。作为额外的奖励，你可以计算你每天的用水量(我得到了 134 升，哇！).

我相信你会同意这是一种创建图表和图形的超级优雅的方式，而且它也*非常快*。如果你在任何很酷的项目中使用过 Google Chart API，一定要在评论中告诉我们！

## 分享这篇文章