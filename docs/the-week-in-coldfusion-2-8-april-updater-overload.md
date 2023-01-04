# coldfusion 中的一周:4 月 2-8 日:更新霸王

> 原文：<https://www.sitepoint.com/the-week-in-coldfusion-2-8-april-updater-overload/>

显然，本周的大新闻是第一个 ColdFusion 更新程序 8.0.1 发布了——随后，MXNA 和 Fullasagoog 中的几乎每个博客都重新发布了该更新程序！在所有公告之间是一些探索新功能的帖子:

*   [Ray Camden 展示了添加 PDF 水印是如何变得更加容易](http://www.coldfusionjedi.com/index.cfm/2008/4/4/ColdFusion-801--Easier-to-add-PDF-Watermarks)
*   布莱恩·里纳尔迪和[巴尼·博伊斯维特](http://www.barneyb.com/barneyblog/2008/04/04/dummy-queries-in-coldfusion-801/)争论创建虚拟记录集的最佳方式
*   [约翰·贝农讨论了 EULA 的变化](http://john.beynon.org.uk/index.cfm/2008/4/4/ColdFusion-801-EULA-Changes)，这将是那些对虚拟化感兴趣的人特别感兴趣的，而[新亚特拉大](http://blog.newatlanta.com/index.cfm?mode=entry&entry=E3EC8D5E-98B9-464A-85AAF04529ECB911)的文斯·邦凡提回应了 ColdFusion 和 BloueDragon 的许可模式之间的差异
*   Adobe 的 Manjukiran Pacchhipulusu 写道[利用了新的 64 位支持](http://www.adobe.com/devnet/coldfusion/articles/64_bit.html)

幸运的是，我还设法找到了一些与更新无关的材料。《法趣》杂志的发源地 Fusion Authority 有了新的特色。许多 CFUnited 视频采访的第一个已经发布，保罗·弗农采访了新亚特兰大的文斯·邦范蒂，你猜怎么着！–蓝龙开源。在我的[新 ColdFusion-Talk 综述#1](http://www.fusionauthority.com/news/4744-coldfusion-talk-roundup-1.htm) 中，我试图强调上周 CF-Talk 邮件列表中的一些最佳线索。

简短而甜蜜的代码片断:

*   [Seth Duffey 展示如何在 ColdFusion 8 中处理多页 TIFF 图像](http://www.leavethatthingalone.com/blog/index.cfm/2008/4/6/ColdFusionAndMultiPageTiffs)
*   [Michael Sharman 展示了如何在 CF 7](http://www.chapter31.com/2008/04/07/flushing-a-cached-web-service/) 中动态刷新缓存的 web 服务(Raymond Camden 在评论中指出，现在在 CF8 中有一个支持的方法可以做同样的事情)
*   Todd Sharp 探索了一种新的方法来创建屏幕截图，用 CF/Java 将 HTML 转换成图像——目前这种方法依赖于 CF 8，但 Todd 说他希望消除这种需求
*   [Charlie Arehart 谈论防止 CFMAIL 被标记为垃圾邮件的潜在解决方案](http://carehart.org/blog/client/index.cfm/2008/4/8/cfmail_messageid_solution_for_CF6and7)

Autosuggest 是 Ajax 的本周主题:基于 CF-Talk 邮件列表上的一个关于允许每个字段有多个自动建议项目的问题， [Dominic Watson 发布了一个解决方案](http://fusion.dominicwatson.co.uk/2008/04/tidy-autosuggesting-solution.html)来解决这个问题以及更多问题，然后将它变成一个自定义标签并在 RIAForge 上发布:[更好的自动建议](http://betterautosuggest.riaforge.org/)。原帖人 Steve Good 也贴出了[一个使用 JQuery](http://goodcf.instantspot.com/blog/2008/04/03/Creating-and-Appending-a-List-Using-AutoSuggest-and-JQuery) 的替代方案。

推进 CFGRID 事业: [Brian Love 演示了如何从 CFGRID](http://blog.brianflove.com/articles/2008/04/02/set-cfwindow-title-from-cfgrid) 中动态设置 CFWINDOW 标题——这是对那些 CFGRID 驱动的 CRUD 接口的一个方便的提示。 [Angela Buraglia 展示了一些 CSS 来实现 HTML/Ajax CFGRID 中的自动换行](http://www.dwmommy.com/?day=4/5/2008&rss=true#blog623)——尽管[这个 CF-Talk 邮件列表线索表明 Angela 的代码可能需要稍微修改](http://www.houseoffusion.com/groups/cf-talk/thread.cfm/threadid:55960)才能与 IE 兼容。Todd Sharp 为可编辑的 CFGRIDs 创建了一个日历日期选择器小部件。太棒了。

SitePoint 发布了一个关于蓝龙的初学者教程，[进入龙:免费开发 ColdFusion 应用](https://www.sitepoint.com/article/develop-coldfusion-apps-free)。如果你对尝试替代的 CFML 引擎感兴趣，这里还有 Railo-[Gert Franz 讲述了如何用 IIS6](http://www.railo.ch/blog/index.cfm/2008/4/7/Configuring-IIS6-with-Railo) 配置 Railo。另一方面，如果你正在寻找重型 Adobe CF，加里·吉尔伯特让我们知道[网络主机 Intermedia.net 已经宣布他们提供 ColdFusion 8 Enterprise](http://www.garyrgilbert.com/blog/index.cfm/2008/4/3/Intermedia-Offering-CF8-Enterprise-Hosting) 主机计划。与 hostmysite 相比，他看了一下他们的计划。或者，如果您要进行大规模的自托管，Mike Brunt 已经发表了一系列关于使用 [HA 群集 ColdFusion 的文章——群集 ColdFusion 第 1 部分——安装 CF](http://www.alagad.com/go/blog-entry/ha-clustering-coldfusion-part-1-installing-cf) 。Mike 打算继续发布关于硬件和软件设置的帖子。

这一次的其他随机事件包括:

*   [Sean Corfield 发布了 ColdFusion Weekly 播客的公共日历](http://corfield.org/blog/index.cfm/do/blog.entry/entry/Want_to_know_what_the_weekly_is_up_to)的链接，其中包含即将到来的采访和话题，你可以以多种不同的格式订阅
*   ColdFusion 的新论坛软件包 CF4em 的开发人员 Bobby Hartfield 正在寻找一些人来帮助测试这些功能，然后再发布测试版
*   如果你是一个 Eclipse 用户，你可能会对 [Mylyn](http://www.eclipse.org/mylyn/) 感兴趣，根据官方网站的说法，这是一个“面向任务的 Eclipse 界面”——[Mike Henke 对它赞不绝口，以及一些资源的链接](http://www.henke.ws/machblog/index.cfm?event=showEntry&entryId=24E60092-19B9-BA51-EE9DBB894F15D61A)(在 Mike 的博客上有很多类似的材料，所以如果你是 Eclipse、Subversion 或 ANT 用户，可以去看看)

最后，从“我甚至没有意识到它在那里”的文件中，Nick Tong 本周向我指出，他的网站 [cfFrameWorks](http://cfframeworks.com/) 不仅是他保存博客的地方，也是 ColdFusion 框架的概要，其中包含每个框架的描述和链接以及单独的框架新闻提要。不错的资源尼克！我与 [Geoff Bowers 关于 FarCry CMS/Framework](https://www.sitepoint.com/article/geoff-bowers-farcry-framework) 的访谈于上周上线，我希望不久能有更多的框架开发者访谈。

这就是这周的内容。如果你有 ColdFusion 提示给我，请发电子邮件给我(kay 在 smoljak dot com)，留下评论或标记您的链接，地址为:kay.smoljak 在 del.icio.us。操作员随时准备接收您的链接！

## 分享这篇文章