# 我们想要的圣诞礼物就是我们的 CSS …

> 原文：<https://www.sitepoint.com/all-we-want-for-christmas-is-our-css/>

毫无疑问，你一直在关注我们的 [2012 年 SitePoint 圣诞特卖](https://xmas.sitepoint.com/)——一场冬季主题的设计盛会(仅桌面版)。屏幕上每天展现的元素包括[溜冰者](https://xmas.sitepoint.com/story/css#day/2)到[跳舞的熊](https://xmas.sitepoint.com/story/css#day/14)到[跃起的鱼](https://xmas.sitepoint.com/story/css#day/11)到[冒烟的火车头](https://xmas.sitepoint.com/story/css#day/9)——还有更多。

[![4 screenshots from Xmas.sitepoint.com](img/c3a7dd0bd643d457cc1cbccefc4435a6.png)](https://www.sitepoint.com/wp-content/uploads/2012/12/blog.jpg)

现在我们在这个项目中加入了一个“圣诞故事模式”,对于那些热衷于深入故事流的人来说。

前往[https://xmas.sitepoint.com/story/css](https://xmas.sitepoint.com/story/css)看当天的故事，减去当天的正常销售页面。

您也可以通过添加“#day/”和日期编号来直接链接到特定的日期。例如，此链接会直接将您带到第 14 天:

[https://xmas.sitepoint.com/story/css#day/14](https://xmas.sitepoint.com/story/css#day/14)

## 关于设计的一切

概念、布局和插图都是他们的心血结晶(如果那是一个东西的话…是的，就是它！)的[哈雷亚历山大](https://twitter.com/the_new_harley "Harley Alexander")。[正如他在这篇文章中解释的，Michael Sauter](https://www.sitepoint.com/how-the-xmas-sales-backend-was-built/) 构建了我们的后端。伊恩·科尔曼让所有的部分一起工作，而裘德·艾克尔管理团队和项目。

这个动画是我制作的。我喜欢摆弄动画，圣诞节销售是研讨新想法的完美场所。不，它不能在非当前浏览器上工作。是的，这也是事实，有些场景可能会推动你的 CPU 一点点。

我的挑战是:

1.  依靠纯 CSS3 动画，不大量使用 JavaScript
2.  尽可能多地找到不同的 CSS 动画方法

我几乎不需要违反那些规则。例如:

1)用 CSS 定位狗橇(位置:固定),并使用 *CSS 转换*将其移动到位。然而，我不得不增加和减少一个类，使狗小跑。

2)您还会注意到，第 5、6 和 7 天的火车动画中，火车一离开烟囱，烟就会从火车上分离出来，就像真的烟一样！我需要一点 JavaScript 来计算烟囱相对于轨道的位置，然后相对于轨道上的位置而不是火车上的位置来放置每一股新的烟雾。

所以实际上，JavaScript 被用作动画开关，而不是动画引擎。

如果你对这个项目的 CSS 细节感兴趣，请加入我们圣诞 CSS 播客小组的讨论。我会在那里等着，回答问题，并得到你对什么最有效的反馈。

我也会在新的一年里在 SitePoint 上写一些更长的动画操作帖子…所以请留意这些帖子。

与此同时，检查每一个新的一天，让我们知道你的想法。

## 分享这篇文章