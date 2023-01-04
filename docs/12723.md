# ColdFusion 9 的发布…

> 原文：<https://www.sitepoint.com/the-release-of-coldfusion-9/>

…好吧，反正是衬衫。如果你热切期待代号为 Centaur 的 ColdFusion 9，你现在可以得到一件非官方的 t 恤。用户组可以获得带有他们名字的批量订单。可惜它是白色的！[向本·福塔](http://www.forta.com/blog/index.cfm/2008/8/29/Get-Your-Unofficial-Centaur-TShirt)脱帽致敬。

本周最大的新闻是 Adobe 已经完成了他们的教育许可，现在学生和教育工作者可以免费使用 ColdFusion 8。美国[的政府机构目前也可以获得 ColdFusion 8](http://www.webbschofield.com/index.cfm/2008/8/28/20-off-ColdFusion-8-for-Government-Agencies) 八折优惠。

**编码**

本周的几篇安全帖子—[向史蒂夫·布莱恩特的 CF_BlogPicks](http://www.bryantwebconsulting.com/blog/index.cfm/2008/9/2/CFBlogPicks-September-2-2008) 致敬，链接如下:

*   [Joshua Cyr 提醒我们要确保不要泄露我们生产服务器上的太多信息](http://www.usefulconcept.com/index.cfm/2008/8/27/ColdFusion-Errors-and-Security)
*   Jason Dean 继续他的安全系列(第 9 部分)中的[请求伪造和 ColdFusion](http://www.12robots.com/index.cfm/2008/8/25/Request-Forgeries-and-ColdFusion--Security-Series-9)

在更一般的与代码相关的新闻中，Stephen Withington 一起列出了一个相当于 CGI 作用域变量的 Java Servlet 列表。PATH_INFO 例如，可以使用 getPageContext()。getRequest()。getPathInfo()。帖子上的评论解释了为什么您会发现 Java Servlet 替代方案更有用。

Paul Marcotte 详细解释了他的测试驱动开发方法——第 1 部分([应用程序结构和 Apache](http://www.fancybread.com/blog/index.cfm/2008/8/27/My-Approach-to-Test-Driven-Development-Part-1--Application-Structure-and-Apache) )和第 2 部分( [MXUnit、ColdSpring 和 ColdMock](http://www.fancybread.com/blog/index.cfm/2008/8/28/My-Approach-to-Test-Driven-Development-Part-2--MXUnit-Coldspring-and-ColdMock) )可用。

Ben Nadel 总是分享他的学习经验，[发布了他使用 ColdFusion 8 secure CFFTP](http://www.bennadel.com/index.cfm?dax=blog:1337.view) 的第一次体验。[布拉德·伍德解释了 de()函数实际上做了什么](http://www.codersrevolution.com/index.cfm/2008/8/28/Delay-Evaluation-What-does-the-de-function-do-anyway)。[雷蒙德·卡姆登仍然讨厌 CFFEED](http://www.coldfusionjedi.com/index.cfm/2008/8/29/CFFEED--You-have-failed-me-for-the-last-time) 。

**框架**

马赫-II 新闻:[马特·伍德沃德报道马赫-II 仪表板的 alpha 已经发布](http://www.mattwoodward.com/blog/index.cfm?event=showEntry&entryId=FBE0A217-E0F5-4442-A6F96B5852644854)。根据 Matt 的说法，dashboard“让您深入了解并控制 Mach-II 应用程序，让您重新加载应用程序或单个模块，重新加载基本或子 ColdSpring bean 工厂，管理日志记录，以及管理缓存。”

安在保险丝盒前面:[肖恩·科菲尔德报道，杰夫·皮特斯的书](http://corfield.org/blog/index.cfm/do/blog.entry/entry/How_to_Drive_Fusebox_55) [如何驾驶保险丝盒 5.5 现在可以购买](http://protonarts.com/index.cfm?fuseaction=Books.showBookDetails&ISBN=0975264761)。作为一名 Fuseboxer，我已经订购了一本——Jeff 以前的 Fusebook 书籍非常棒。

**社区**

Brian Rinaldi 上周休假一周来更新他的开源软件——我不知道你是否想念他，但我确实想念他。本周，他带着四个新项目、12 个更新和一大堆公告、教程、演示和评论回来了。

经过一段时间的中断，我又回到了我的 CF-TALK 邮件列表中。[上周的总结包括关于 SQL 注入、Web 服务、学术许可和职业建议的讨论主题](http://www.fusionauthority.com/news/4765-coldfusion-talk-roundup-aug-28-2008.htm)。

一小杯苏格兰威士忌——9 月 25 日在伦敦举行的迷你苏格兰威士忌加冰块会议——[现在有完整的节目，并开放注册](http://aweedram.com/ofScotch/)(向[安迪·艾伦](http://www.creative-restraint.co.uk/blog/index.cfm/2008/9/1/A-Wee-Dram-of-Scotch--Registration-Open)和[肖恩·科菲尔德](http://corfield.org/blog/index.cfm/do/blog.entry/entry/A_Wee_Dram__location_and_more_details)致敬)。有明星在阵容中，而且只有 10 个，我敢说它会很快销售一空。

**替代 CFML 发动机**

在慕尼黑 CFUG 与格特·弗朗兹和咪奇·斯特雷特会面后，加里·吉尔伯特发表了他对 Railo 3 的印象。Gary 对管理员、亚马逊 S3 资源和扩展框架中“严格”的变量范围设置印象深刻。

Michael Sprague 发布了 CFjqAjax 的测试版，这是一个自定义标记库，用于复制 ColdFusion 8 的 Ajax UI 控件 CFGRID、CFWINDOW 和 CFTOOLTIP 以及 jQuery 等效项。这是一个好消息，不仅对于那些喜欢使用 jQuery JavaScript 而不是 ExtJS 或 YUI 的人来说是如此，对于那些使用 Railo 和 Open BlueDragon 等 CFML 引擎的人来说也是如此。

好了，孩子们，现在就这样。有线索吗？给凯发电子邮件，在 delicious.com 留下评论或标签

## 分享这篇文章