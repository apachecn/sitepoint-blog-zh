# 灰尘选择器版本 2.0

> 原文：<https://www.sitepoint.com/dust-me-selectors-version-20/>

在我们秘密的地下实验室辛苦工作了几个月之后， [Dust-Me 选择器 2.0 版本](https://www.sitepoint.com/dustmeselectors/)终于发布了！

如果你是众多对[原始版本](https://www.sitepoint.com/find-unused-css-selectors-with-dust-me-selectors/)提出建议或评论的人中的一员，你会很高兴地得知新版本包含了**你所要求的一切**以及更多。

将所有这些整合在一起是一个相当大的挑战，特别是因为 Mozilla 的文档是如此的…呃…不稳定(事实上，对于一些功能，我不得不查看 Firefox 的源代码来弄清楚它是如何完成的！)但是没有什么比锻炼头脑的挑战更好的了，我想你会同意最终的结果是非常值得进行艰苦的斗争的。

总结一下新特性:

*   新的爬行功能——它可以爬行一个站点地图 <abbr title="eXtensible Markup Language">XML</abbr> 文件，或者一个 <abbr title="HyperText Markup Language">HTML</abbr> 站点地图(即它将从任何页面爬行一个链接深度)。
*   显示哪些选择器已被使用，以及未被使用。
*   数据存储已经从首选项系统中取出，放到了文件系统中——数据存储在用户可选的
    文件夹中的 <abbr title="JavaScript Object Notation">JSON</abbr> 文件中。
*   更好地支持条件注释——它现在可以识别单个注释中任意数量的样式表。
*   扩展了自动化功能，这样无论什么时候 <abbr title="Document Object Model">DOM</abbr> 以编程方式改变，它都可以自动重新解析页面。
*   综合用户喜好。
*   对羊群的支持。
*   一个漂亮的新图标，由亚历克斯·沃克提供

更多信息，文档和安装新版本， **[查看 sitepoint.com](https://www.sitepoint.com/dustmeselectors/)**上的除尘选择器

## 分享这篇文章