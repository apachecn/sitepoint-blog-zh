# Ruby 中的遗传算法，第二部分

> 原文：<https://www.sitepoint.com/genetic-algorithms-in-ruby-part-ii/>

![](img/f05b8ec1b879f3b84847e459e79cd0fc.png)

这是本文的第二部分。如果您还没有阅读第一篇文章，请阅读第一篇文章[。](https://www.sitepoint.com/genetic-algorithms-in-ruby-part-i/)

## 生殖

染色体是用我们刚刚添加到染色体类中的加权随机(即轮盘赌机)代码复制的。这个想法很简单；以现有的染色体池为例，用一个同样大小的新池来代替它，但是，只有一些旧生物能够繁殖并保持它们的染色体(我们可以认为旧生物正在死亡)。