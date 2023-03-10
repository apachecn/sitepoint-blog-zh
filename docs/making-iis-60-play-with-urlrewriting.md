# 让 IIS 6.0 玩 URL 重写

> 原文：<https://www.sitepoint.com/making-iis-60-play-with-urlrewriting/>

在 [SitePoint 上经常出现的一个话题。NET 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=141)的主题是“我如何在？网？”一旦进入 ASP.NET 管道，这个问题的答案就相对简单了。不幸的是，考虑到当前版本的 IIS，到达管道比它应该的要复杂得多。IIS 确实更喜欢处理已知的扩展，而不是花哨的映射。解决这一限制的文档很少。

**获取 IIS 映射它不想映射的内容**

在 [IIS 7 的新特性中，最重要的是](http://weblogs.asp.net/scottgu/archive/2006/04/20/Cool-new-IIS7-Features-and-APIs.aspx)能够在任何到达服务器的请求上注入 HttpHandlers 和 HttpModules。这是头条新闻的原因——让 IIS 6 正确映射重写的 URL 本身就是一个技巧。IIS 6.0 和更早的版本非常非常希望从请求中提取扩展，并将其映射到适当的处理程序进行处理。所以，如果你想让你的网址像 http://www.example.com/products/fluffy-bunny-slippers/的[一样，你必须做一点工程。选项包括:](http://www.example.com/products/fluffy-bunny-slippers/)

1.  **Let IIS do what IIS wants to do.**

    有时候，最好屈从于底层技术想要的工作方式。所以只要改变你的页面，让它看起来更像页面而不是文件夹——比如 http://www.example.com/products/fluffy-bunny-slippers.aspx。这个 url 几乎和前面的例子一样容易破解，但需要的工程设计却少得多。您的应用程序不需要在 IIS 级别上重新配置就可以正确映射。这种策略也适用于共享主机的情况，在这种情况下，您无法在 IIS 中进行重大的配置更改。

2.  **Use an ISAPI Module or Filter**

    ISAPI 模块和过滤器位于 IIS 中，在请求到达 ASP 之前处理它们。NET 的管道。这两个选项是相当便宜的 IISRewrite 和免费的 ISAPI 重写过滤器。两者都使用了与 mod_rewrite 相似但不完全相同的语法。这可能是最干净的解决方案，但它也需要一个宿主环境，允许对 IIS 进行重大的配置更改。

3.  **Use a Wildcard mapping.**

    IIS 6.0 支持通配符映射，其中给定网站的所有请求都被映射到给定的处理程序。虽然这听起来像一个银弹，但它有很多缺点。可能最大的问题是对静态资源的请求将通过 ASP.NET 管道进行修补。此外，如果您使用任何 estoric 内容类型，您将必须确保在您的应用程序或服务器的 web.config 中为它们分配一个适当的处理程序。

4.  **Use a custom ASP.NET 404 page.**

    如果您的 404 页面是一个 ASP.NET 页面，那么它将运行整个渲染管道，包括调用您的自定义 HttpModules。所以，任何不存在的文件仍然会让你的程序插入几个字。虽然相对来说有点笨拙，但这是一种有效的策略，适用于既不能修改 IIS 的重要部分，也不能仅仅使用。aspx urls。不幸的是，大多数共享主机不允许使用上面的两个或三个选项，所以这种最小公分母在当今的应用程序中经常使用。

现在我们有了困难的部分——让 IIS 向 ASP 提供请求。NET——处理好之后，我们就可以开始实际重写 URL 的工作了。幸运的是，这相对容易，而且明显比上述方法更明智。与其重复互联网的智慧，我不如给你一些关键的链接:

1.  [Scott Guthrie 的](http://weblogs.asp.net/ScottGu)关于[网址改写的精彩文章。网](http://weblogs.asp.net/scottgu/archive/2007/02/26/tip-trick-url-rewriting-with-asp-net.aspx)。特别注意最后几点；它们涵盖了中的几个大问题。NET url 重写。
2.  [urlrewhitingnet。UrlRewrite](http://www.urlrewriting.net/en/Default.aspx) 是一个相当不错的。NET 2.0 库来处理您的 url 重写需求的机制。
3.  UrlRewriter.Net 是另一个相当不错的开源软件。NET 1.1/2.0 库，用于处理您的 url 重写需求的机制。
4.  斯科特·沃特玛西斯克，那个带你来的人。文本和[社区服务器](http://communityserver.org/)，张贴这个非常有帮助的职位，对一个相当重大的错误。NET 的本地 url 重写。
5.  HttpContext 的 MSDN 文档。重写路径，这一努力中的“关键”方法。

所以，控制 IIS，让他们的 URL 更加人性化！

[![kick it on DotNetKicks.com](img/ab5d7ba11c1f52d9eaaa274f178371ac.png)](https://www.dotnetkicks.com/kick/?url=https://www.sitepoint.com/making-iis-60-play-with-urlrewriting)

## 分享这篇文章