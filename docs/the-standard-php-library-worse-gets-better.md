# 标准 PHP 库:越差越好

> 原文：<https://www.sitepoint.com/the-standard-php-library-worse-gets-better/>

阅读那些被 PHP 弄得焦头烂额的人的评论总是很有趣，有史以来的经典是在[和](http://c2.com/cgi/wiki?PhpDiscussion)之间的一次掷硬币；

> PHP 是一大堆垃圾。它起初看起来没有臭味的唯一原因是因为它包裹着五层创可贴。

还有[这个](http://lerdorf.com/fanmail.txt)

> 亲爱的拉斯莫斯，
> 
> 我想让你知道，我已经检查了你的“开源项目”，我觉得有必要通知你，即使你躲在“开源”的幌子下，你生产的完全是废话。

当然，这样的评论通常是因为考虑到 PHP 的数组而引起的，并且没有抓住要点，PHP 中的数据结构排序通常是这样的；

 `$sql = "SELECT * FROM users ORDER BY name DESC";`

它也忽略了 PHP 最肯定做对的事情，比如部署的容易性:“看妈妈——没有 [XML 描述符](http://fishbowl.pastiche.org/2004/08/18/ignorance_is_bliss)！”。

我以前提出过这个概念，即越差越好。再次注意[杰夫](http://www.procata.com/blog)在这里所指的[；](https://www.sitepoint.com/forums/showpost.php?p=1121502&postcount=2)

> 随着时间的推移，人们更加关注以新泽西风格编写的软件，因此它改进得更快。坏事变好事。

看看 [Marcus Boerger](http://marcus-boerger.de/) 到目前为止用[标准 PHP 库](https://www.sitepoint.com/article/php5-standard-library)所做的，对我来说是凉水:变得更好。

现在最大的问题是 SPL 还应该做些什么？

相信 Marcus 对想法和建议持开放态度(当然，如果你有能力的话，还会直接提供帮助),输入*会产生影响吗*比如[这个讨论](http://marc.theaimsgroup.com/?t=107781148200007&r=1&w=2)会导致迭代器::hasMore()的初始实现被重命名为迭代器::valid()。

讨论的最佳地点是[内部清单](http://marc.theaimsgroup.com/?l=php-dev)。

## 分享这篇文章