# ColdFusion 的一周:3 月 5 日至 11 日:蓝龙抢尽风头

> 原文：<https://www.sitepoint.com/the-week-in-coldfusion-5-11-march-bluedragon-steals-the-show/>

毫无疑问，本周最大的新闻是 New Atlanta 的[声明，他们正在开源他们的 CFML 服务器产品之一 BlueDragon 的 J2EE 版本。其他版本仍将作为商业产品。博客圈的反应各不相同，从 SitePoint 博客作者 Eric Jones 在](http://www.newatlanta.com/corporate/news/bluedragon_opensource_announce.jsp) [New Atlanta 做不可思议的事！！](https://www.sitepoint.com/the-week-in-coldfusion-12-18-march-beyond-the-new-atlanta-announcement/)在泰伦斯·瑞恩的[哈欠声中，蓝龙走向开源](http://www.numtopia.com/terry/blog/archives/2008/03/yawn_blue_dragon_goes_open_source.cfm)；一些人认为这是 ColdFusion 语言的一件大事，而其他人，如 Adobe 工程师 Damon Cooper，认为这是对新亚特兰大的失败的承认。为了更好地洞察这一举动的潜在成功和失败点，请查看肖恩·科菲尔德的[开源蓝龙](http://corfield.org/blog/index.cfm/do/blog.entry/entry/Open_Source_BlueDragon)，杰夫·鲍尔斯的[蓝龙已死！蓝龙万岁！查理·格里夫的《这里有龙》!](http://blog.daemon.com.au/go/blog-post/bluedragon-is-dead-long-live-bluedragon)如果你想知道作为一名 CFML 开发者，这一宣布会对你个人产生怎样的影响，那么来自 New Atlanta 的 Alan Williamson 试图用开源的 BlueDragon 来回答这个问题，这对你来说意味着什么呢？

这一宣布掩盖了这次本来会是相当大的新闻: [Adobe ColdFusion 8 赢得了 Web 开发类别的 Jolt 奖](http://www.drdobbs.com/blog/portal/archives/2008/03/jolt_award_winn.html)。这是第 18 届 [Jolt 产品优秀奖](http://www.joltawards.com/)，由软件行业专家组成的评审团对过去一年中“震撼”行业的产品、书籍和网站进行评选。对于 Adobe 来说，这是一个相当不错的成就，继他们的[入围 Codie 奖](http://www.siia.net/codies/2008/)(获奖者将于 5 月宣布)之后。

本周片段:

*   ColdFusion Weekly Podcast 仍然表现强劲——这一次是 3.03 版的[,这是 CF_Roundtable Edition](http://coldfusionweekly.libsyn.com/index.php?post_id=316473#) 的首次发布，CF 小组讨论了 Flex 3 和 Adobe AIR 的发布。
*   面向商业和专业用户的社交网络应用 LinkedIn 的用户现在可以加入 ColdFusion 开发人员组、Flex 开发人员组和 AIR 开发人员组。[查看本·福塔的帖子了解详情](http://www.forta.com/blog/index.cfm/2008/3/6/LinkedIn-Users-Join-The-ColdFusion-And-Flex-Groups)。
*   Andy Powell 想知道[你认为 CF8 中的哪个新特性你会真正使用，但是已经失去了兴趣？](http://www.infoaccelerator.net/index.cfm?event=showEntry&entryId=9E655290-FF30-C9A3-AD594492DFCD340D)

业务谈够了，我们截点代码吧！首先，一直流行的 CF8/ExtJS 组合:斯科特·本内特首先向我们展示了如何使用 JavaScript 函数[向 CFWindow](http://www.coldfusionguy.com/ColdFusion/blog/index.cfm/2008/3/11/CFWindow-with-a-Minimize--Maximize-button) 添加最小化/最大化按钮，然后[如何在 CFGrid](http://www.coldfusionguy.com/ColdFusion/blog/index.cfm/2008/3/11/CFGrid-Disabling-the-Selected-Row-HighLighting) 中禁用选中的行高亮显示。然后安迪·贾勒特向我们展示了[如何动态隐藏 CFGrid 列](http://andyjarrett.co.uk/andy/blog/index.cfm/2008/3/3/Dynamically-hiding-a-HTML-CFGridColumn)。这些精彩的网格教程展示了扩展内置功能是多么容易，ExtJS CFGrid 很快成为我最喜欢的 ColdFusion 8 功能之一。如果你还没有使用 CF8，不要绝望:贾斯汀·卡特已经发布了 ColdExt 的 Alpha 3，这是一个 ExtJS 表单库,也可以与 ColdFusion MX7 一起使用。在这个最新版本中，增加了对 GridPanels 的支持！

Ben Nadel 分享了一个使用超级作用域扩展 Application.cfc 和 OnRequestStart()的简便技术。他还分享了一个简单的 ColdFusion 8 图像处理 web 服务的源代码。和往常一样，Ben 非常清楚地解释了概念，并包含了大量的示例代码，因此这两篇文章非常值得一读。

Brian Rinaldi 的每周开源更新 : [Fusebox 5.5.1 现在可以从 Fusebox 网站](http://corfield.org/blog/index.cfm/do/blog.entry/entry/Fusebox_551_is_available)下载[。这是一个较小的 bug 修复版本，但是对于那些使用 5.5 中引入的非 XML 风格的开发人员来说尤其重要。](http://fusebox.org/go/fusebox-downloads/core-files)

最后，如果你没有使用 Firefox 的 [Firebug 扩展来帮助调试 HTML、CSS 和 JavaScript](https://addons.mozilla.org/en-US/firefox/addon/1843) ，你真的需要看看它——它确实是任何处理网页的人的必备工具。[让 Firebug](http://coldfire.riaforge.org/)*更加出色的 CFML 调试扩展 ColdFire，已经更新到 1.004 版本。根据 Nathan Mische 的说法，这个新版本并没有增加太多的功能，但是为下一个支持 Firefox 3 的主要版本打下了基础。*

 *这就是这周的内容。像往常一样，如果你有我的链接，给我发邮件，在这里留下评论或标记为:kay.smoljak on del.icio.us。* 

## *分享这篇文章*