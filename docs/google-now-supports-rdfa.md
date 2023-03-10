# 谷歌现在支持 RDFa

> 原文：<https://www.sitepoint.com/google-now-supports-rdfa/>

万岁！我以为这一天永远不会到来……谷歌最近[宣布](https://googlewebmastercentral.blogspot.com/2009/05/introducing-rich-snippets.html)支持 [RDFa](https://google.com/support/webmasters/bin/answer.py?answer=146898) 和[微格式](https://google.com/support/webmasters/bin/answer.py?answer=146897)。这是一个好消息，他们现在和雅虎一起支持 W3C RDFa 标准来嵌入数据和内容。可以把 RDFa 看作更灵活的微格式。

谷歌称之为“丰富片段”。如果谷歌在你的页面中发现嵌入的数据，它会给你返回的结果一个更加丰富的外观。最初，Google 在其富片段中只支持两种类型的对象:人和评论。我确信 googlebot 会索引任何嵌入的元数据，但目前只会显示被标记的人和评论。

![Rich Snippet from Google](img/4a03dc78b414281137963f0266349d71.png)

这项技术的另一个令人兴奋的用途是与谷歌[定制搜索引擎](https://www.google.com/cse)一起使用。使用他们的 API，你可以更好地控制自己站点中的数据。你还可以使用任何自定义词汇，这是对谷歌搜索引擎行为的一个很好的补充。

谷歌很快指出，这不一定会影响你的搜索结果，但让他们的秘方来决定怎么做。

下面是谷歌的一个简短简介，介绍了这些附加标记的目的:

> 微格式和 RDFa 如何工作
> 
> 假设您的页面上有一篇关于一家餐馆的评论。在您的 HTML 中，您显示了餐馆的名称、地址和电话号码、提供评论的用户数量以及平均评分。人们可以阅读和理解这些信息，但对计算机来说，这些信息不过是一串无结构的文本。使用微格式或 RDFa，您可以标记每一段文本，以清楚地表明它代表某种类型的数据:例如，餐馆名称、地址或评级。这是通过提供计算机能够理解的附加 HTML 标签来实现的。这些不会影响页面的外观，但是谷歌和任何其他查看 HTML 的服务可以使用标签来更好地理解你的信息，并以有用的方式显示它——例如，在搜索结果中。
> 
> 您可以使用您喜欢的任何一种标准——微格式或 RDFa——并且您不需要理解其中一种就可以使用另一种。
> 
> [阅读更多…](https://google.com/support/webmasters/bin/answer.py?hl=en&answer=99170)

以下是谷歌理解的最新词汇:

*   [评论](https://google.com/support/webmasters/bin/answer.py?answer=146645)
*   [人](https://google.com/support/webmasters/bin/answer.py?answer=146646)
*   [产品](https://google.com/support/webmasters/bin/answer.py?answer=146750)
*   [企业和组织](https://google.com/support/webmasters/bin/answer.py?answer=146861)

很长一段时间以来，雅虎在它从网上索引的内容中支持微格式和 T2 RDFa。他们是这个领域的大推手，使用他们的 BOSS API 和 Searchmonkey，许多有趣的事情已经成为可能。

正如我们所说的，W3C 网站上正在创建一个 RDFa 教程“[面向 HTML 作者的 RDFa](https://www.w3.org/MarkUp/2009/rdfa-for-html-authors)”。它还没有完全完成，但是提供了一个很好的概述和很多很好的例子。

现在 Google 支持结构化数据，是时候学习如何使用这种东西了。因此，去进行一次[读取](https://google.com/support/webmasters/bin/answer.py?hl=en&answer=99170)并开始标记你的数据。他们有很多[的好例子](https://google.com/support/webmasters/bin/answer.py?answer=146898)(请注意，[他们的一些](https://googlewebmastercentral.blogspot.com/2009/05/introducing-rich-snippets.html?showComment=1242162540000#c4437838920656030533)标记不是[当前有效的](https://googlewebmastercentral.blogspot.com/2009/05/introducing-rich-snippets.html?showComment=1242171360000#c4545643737175339354)，所以请睁大眼睛关注更新)。即使是谷歌，也会把一些事情搞错；)

## 分享这篇文章