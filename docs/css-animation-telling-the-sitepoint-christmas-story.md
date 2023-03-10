# 使用 CSS 动画讲述 SitePoint 圣诞故事

> 原文：<https://www.sitepoint.com/css-animation-telling-the-sitepoint-christmas-story/>

如果你一直在关注我们的 [2012 年 SitePoint 圣诞特卖](https://xmas.sitepoint.com/)，你可能已经注意到了在后台展开的 winterland 故事(仅桌面版)。到目前为止，我们已经经历了从[滑冰运动员](https://xmas.sitepoint.com/story/css#day/2)到[跳舞熊](https://xmas.sitepoint.com/story/css#day/14)到[跃起的鱼](https://xmas.sitepoint.com/story/css#day/11)到[冒烟的火车头](https://xmas.sitepoint.com/story/css#day/9)的一切——还会有更多。

[![4 screenshots from Xmas.sitepoint.com](img/c3a7dd0bd643d457cc1cbccefc4435a6.png)](https://www.sitepoint.com/wp-content/uploads/2012/12/blog.jpg)

今天，我们推出了一个新的“故事模式”,适合那些只想看故事的人。使用以下 URL 查看当天的故事，不包括当天的正常销售页面。

**链接:[https://xmas.sitepoint.com/story/css](https://xmas.sitepoint.com/story/css)**

您也可以通过添加“#day/”和日期编号来直接链接到特定的日期。例如，这个链接会直接把你带到第 14 天:[https://xmas.sitepoint.com/story/css#day/14](https://xmas.sitepoint.com/story/css#day/14)

**2012 年设计的内幕**

虽然过去几年几乎都是两个人组建团队(向 Dan 大喊)，但今年我们有了更多的资源。

令人敬畏的概念，布局和插图是大脑的孩子(是一个东西吗？？)的[哈雷亚历山大](https://twitter.com/the_new_harley "Harley Alexander")。哈雷的原始设计包括一个半球切换器，让你切换到阳光明媚，温暖的南半球圣诞节视图。溜冰者变成了溜冰者，打雪仗变成了打水仗。

遗憾的是，我们不得不早早放弃这个想法。随着圣诞节的临近，发货日期真的没有商量的余地，也没有聪明的编码技巧可以让你绕过做两倍的插图的问题——尽管那样会很有趣。

正如他在这篇文章中解释的，Michael Sauter 构建了我们的后端。伊恩·科尔曼让所有的部分一起工作，而裘德·艾克尔管理团队和项目。

我个人的贡献是动画。我喜欢尝试这种东西，圣诞特卖是产生一些新想法的完美场所。不，它不能在非当前浏览器上工作。是的，这也是事实，有些场景可能会推动你的 CPU 一点点。没关系。

我的挑战是:

1.  推动纯 CSS3 动画在不依赖 JavaScript 的情况下可以做什么
2.  尽可能多地寻找不同的 CSS 动画风格和技术方法

到目前为止，这些规则只有一些小的例外。例如:

1)用 CSS 定位狗橇(位置:固定),并使用 *CSS 转换*将其移动到位。然而，我们需要增加和减少一个类，使狗小跑。
2)你还会注意到，在第 8、9 和 10 天的火车动画中，火车一离开大烟囱，烟雾就从火车上消失了——就像真的烟雾一样！我们需要一点 JS 来计算烟囱相对于轨道的位置，然后相对于轨道上的位置而不是火车上的位置来放置每个新的烟雾。

本质上，我们使用 JavaScript 作为动画开关，而不是动画引擎。

无论如何，如果你对 CSS 的本质感兴趣，来我们的圣诞 CSS 播客小组打个招呼吧。我会在那里等着，回答问题，并从总体上得到你对什么最有效的反馈。

在新的一年里，我还会写一些更长的动画如何张贴，所以请留意这些。

与此同时，检查每一个新的一天，让我们知道你的想法。

## 分享这篇文章