# 动态网站搜索引擎优化技巧和提示

> 原文：<https://www.sitepoint.com/dynamic-site-seo-tips-hints/>

动态网站需要高度专业化的搜索引擎营销策略，这些策略不同于静态网站。除非进行适当的优化，否则动态网站仍然很难被索引。虽然搜索引擎说他们现在索引动态网站，他们这样做，很多时候没有一点帮助就不会发生。当然，页面的定位完全是另一个问题。

有许多策略可以用来将你的动态 URL 转换成搜索引擎友好的 URL。在我们开始之前，让我们看看电子商务网站和其他大型网站使用的动态数据库是如何创建的，以及为什么它们很难被索引。

##### 是什么让动态网站隐藏起来？

动态页面是使用 ASP、Cold Fusion、Perl 等技术动态创建的。这些页面对于访问网站的用户来说很好用，但是对于搜索引擎爬虫来说就不太好用了。

为什么？因为动态生成的页面实际上并不存在，直到用户选择了生成它们的变量。搜索引擎蜘蛛不能选择变量，所以页面不能生成，也不能被索引。

最大的问题是像 Google 这样的爬虫不能读取 URL 的整个动态数据库，它要么包含一个查询字符串(？)或其他数据库字符(#&*！%)已知是蜘蛛陷阱。因为搜索爬虫在深入读取动态数据库方面存在问题，所以它们被编程为检测和忽略许多动态 URL。

我们最近增加了一个客户的搜索引擎潜力，从 6 页增加到 659 页。考虑到谷歌最初只看到半打页面，我们认为数百个优化的页面将显著提高我们客户的搜索引擎可见性。

##### 使动态网站可见

有一些动态页面优化技术可以用来促进动态站点的索引。首先想到是利用静态页面。还有许多方法可以将动态 URL 转换为搜索引擎友好的 URL。另一个获得更广泛关注的好方法是使用付费收录和可信 feed 程序，保证动态网站的索引或特定数量的点击率。

***静态页面***

将动态页面的链接放在静态页面上，并根据每个搜索引擎的指导原则手动将静态页面提交给搜索引擎。这可以通过显示动态页面链接的目录页面轻松完成。虽然爬网程序不能索引整个动态页面，但它们会索引大部分内容。

***【ASP】***

来自 Exception Digital Enterprise Solutions 的 XQASP 是一个将动态 ASP 页面转换为搜索引擎兼容格式的优秀工具。

例如，以下 URL 同时包含“？”和“&”，使其不可索引:

http://www.planet-source-code.com/vb/scripts/ShowCode.asp?lngWId=3&txtCodeId=769

下面，它已被搜索引擎友好(所有"？以及用替换字符替换的“&”和“=”字符):

http://www.planet-source-code.com/xq/ASP/txtCodeId.769/lngWId.3/qx/vb/scripts/ShowCode.htm

一旦你转换了 URL，不要忘记使用搜索引擎优化技术来修改 HTML 标签和标签中的内容，然后根据每个搜索引擎的提交指南提交所有页面。ColdFusion。这可能是一个简单的解决办法。在服务器上重新配置 ColdFusion，以便“？”在查询中，字符串被替换为将值传递给 URL 的“/”。当然，你仍然需要优化你的页面，让你的站点在爬虫访问的时候快速响应。

***CGI/Perl***

```
Path_Info and Script_Name are environment variables in a dynamic application containing the complete URL address, including query string information. The solution is to write a script that removes all the information before the query string, thereby making the remaining information equal to a variable, and then using that variable in your URL address. Again, optimization is required to show up in the top editorial listings.

 ***阿帕奇软件*** 

Apache 服务器有一个重写模块(`mod_rewrite`)，可用于 Apache 1.2 及更高版本，可以动态转换请求的 URL。您可以将包含查询字符串的 URL 重写为可以被搜索引擎索引的 URL。默认情况下，Apache 没有提供该模块，所以请从您的 Web 主机上了解它是否适用于您的服务器。

 *大多数主要的搜索引擎现在都提供付费收录和基于更新索引或每次点击费用的可信 feed 程序。提供此类程序的引擎包括 AltaVista、AskJeeves、FAST、Inktomi、LookSmart、Lycos 和 Teoma。

单靠这些程序不足以做好搜索引擎定位。要让您的动态站点通过 XML feed 进行索引，首先要确保使用专业的优化技术对站点进行了适当的优化。

优秀的搜索引擎优化(SEO)承包商可以访问基于 Web 的自动提要。它们提供创建和管理应用程序，为多个搜索引擎包含程序生成 XML 优化的提要。专业的 SEO 技术人员可以映射任何大型电子商务网站的整个目录，生成自动化的 XML 优化提要。

这个 XML 过程的关键是动态站点内容和各种搜索引擎数据库之间的关键字匹配。使用特殊的过滤器和参数，这个过程会生成数千个带有页面特定元信息的关键字。结果是每个产品页面在目标搜索引擎上的独特表示，以及您的动态站点、服务、产品等的更准确表示。

##### 搜索引擎可见性的关键

互联网用户用令人难以置信的战略关键词组合进行搜索。各种搜索级别的用户必须先找到你，才能找到你的竞争对手。这就是为什么关键词分析和研究对你的 SEO 活动的成功如此重要。专业的优化技术，如文案、编码和编辑链接，对主要搜索门户网站的排名也很重要。* 
```* 

## **分享这篇文章**