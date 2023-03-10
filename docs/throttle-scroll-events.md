# 快速提示:如何抑制滚动事件

> 原文：<https://www.sitepoint.com/throttle-scroll-events/>

*这篇文章由[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic)和[朱利安·莫茨](https://www.sitepoint.com/author/jmotz/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

监听滚动事件的危险之一是性能下降。用户每滚动*次，浏览器就会执行一次回调，这可能是每秒许多次。如果回调执行大量重画，这对最终用户来说是个坏消息。重画是昂贵的，特别是当你重画视图的大部分时，比如有滚动事件时。*

下面的例子说明了这个问题:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[未调节滚动事件](https://codepen.io/SitePoint/pen/WxkjQY/)。