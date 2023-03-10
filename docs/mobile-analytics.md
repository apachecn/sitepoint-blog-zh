# 谷歌分析跟踪移动网站

> 原文：<https://www.sitepoint.com/mobile-analytics/>

如今，移动网站在企业中变得非常流行。你甚至可以说，它正在赶上社交媒体，成为商界的“新流行语”。我的文章将提供一个技巧来确保您的新 jQuery 移动站点正常运行并获得您想要的结果。

谷歌分析跟踪移动网站不同于跟踪你的网站。毕竟，移动网站的全部目的是为移动用户提供快速、方便的访问。99.9%的流量是寻找你的电话号码或你的位置，客户要么已经做了谷歌搜索来找到你，要么他们是直接访问。

因此，让我们使用谷歌分析追踪电话号码和地图的点击量。我们假设您已经在头中插入了默认的 Javascript。这是他们给你的代码:

```
_gaq.push(['_setAccount', 'UA-XXXXXX-1']);
```

对于这个例子，我们的企业在坦帕湾地区有多个位置，我们希望跟踪哪个城市正在接收移动访问者的点进。这些数据可能对未来的移动广告很重要，但主要是为了让我们知道这是我们的客户正在使用的一项附加服务。

[![](img/a4c468fe5d4c24670407e90a1454c4c8.png "city-screenshot")](https://www.sitepoint.com/wp-content/uploads/2012/01/city-screenshot.jpg)

我们的位置由可折叠的 div 分隔，带有一个使用“电话:813-XXX-XXXX”的基本 html a href 链接，因此浏览器知道将该链接呈现为电话呼叫。我们将每个城市放在一个可折叠的 div 中，以减少意外电话，打电话确实需要额外的点击，但这比意外收到一个会扭曲我们数据的电话要好。下面是我们整个 DIV 的示例代码，包括我们的 Google Analytics 事件跟踪代码。

```
<div data-theme="a" data-collapsed="true" data-role="collapsible" class="ui-collapsible-contain">
    <h3>Tampa</h3>
    <p aria-hidden="true">
	<a href="tel:813-XXX-XXXX" onClick="_gaq.push(['_trackEvent', 'Phone', 'Clicked', 'Tampa Phone Call']);">Call 813-XXX-XXXX</a><br />
	XXX E Highway 92<br />
	Tampa, FL
    </p>
    </div>
```

让我们分析一下这个事件的参数。“电话”是我们的品类，“点击”是我们的行动，“坦帕电话”是我们的标签。当然，这些参数可以随意更改，但是我建议只在需要时更改标签。另一个类别的例子是“地图”或“方向”,如果你发布了一个到谷歌地图页面的链接。

现在让我们看看这在谷歌分析中是如何翻译的。在“内容”下，您会看到“事件”。不需要浏览整个 Google Analytics 课程，只需单击“热门事件”并将“查看”更改为“事件标签”，您应该会看到类似以下内容:

[![](img/86b4ee580e73ea0ecfeab0948246e351.png "ga-results")](https://www.sitepoint.com/wp-content/uploads/2012/01/ga-results.jpg)

## 摘要

现在，我们可以知道有多少客户想要深入到特定的城市电话号码，而不是拨打我们移动主页上的主要 800 号码。我希望这能帮助你增加你的移动客户跟踪。

## 分享这篇文章