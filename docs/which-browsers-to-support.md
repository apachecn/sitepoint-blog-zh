# 支持哪些浏览器？

> 原文：<https://www.sitepoint.com/which-browsers-to-support/>

Dave Shea 的[浏览器支持 2004](http://www.mezzoblue.com/archives/2004/02/02/browser_supp/) 着眼于当今的浏览器市场，并讨论了他在不同浏览器中测试站点的方法。戴夫提出了一些很棒的建议，我不能说我不同意其中任何一条。也就是说，这里有一些我自己的想法。

首先，像[thecounter.com](http://www.thecounter.com/stats/)这样的网站可以检测浏览器的发展趋势，但是在决定支持哪些浏览器方面没有什么价值。thecounter.com 的工作原理是从所有使用免费统计计数器的网站收集统计数据。不幸的是，使用这项服务的网站强烈偏向于使用免费托管服务的人。主流和专业网站很少使用免费计数器来跟踪它们的流量，因此 thecounter.com 不太可能提供真正准确的全球趋势指标。我总是说，决定哪些浏览器值得你支持的唯一可靠的方法是看看你自己网站的统计数据。例如，我的个人博客有近 50%的访问者在使用 Mozilla 变种——相比之下，thecounter.com 的全球平均水平不到 2%。

让我们看看“支持浏览器”实际上是什么意思。根据 Dave 的定义，你支持的浏览器是你积极测试并努力确保你的网站设计如你所愿的浏览器。需要注意的是，指定一个支持的浏览器列表并不意味着故意避免对其他浏览器的支持——它只是意味着你不会亲自测试它们，如果网站设计没有完全按照计划运行，你也不会介意。

多亏了 CSS 和 web 标准，实际上有可能创建好看的网站，这些网站可以从几乎任何仍在使用的浏览器中访问(因为网站内容可以被检索)。诀窍是使用[渐进式增强](http://hotwired.lycos.com/webmonkey/03/21/index3a.html)，这是 WaSP 创始成员 Steven Champson 倡导的方法论。从页面的核心结构标记开始，使用 CSS 和 Javascript 构建额外的表示和行为信息。最终的结果是一个非常漂亮的页面，具有现代网站所期望的所有功能，即使使用最过时的技术，人们仍然可以访问它。

## 分享这篇文章