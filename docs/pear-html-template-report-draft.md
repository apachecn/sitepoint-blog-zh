# PEAR::HTML _ Template _ 报表草稿

> 原文：<https://www.sitepoint.com/pear-html-template-report-draft/>

一个有趣的提议草案出现在[一揽子提议](http://pear.php.net/pepr/pepr-overview.php)中—[HTML _ Template _ Report](http://pear.php.net/pepr/pepr-proposal-show.php?id=79)。草稿状态意味着作者有兴趣获得关于提案的反馈，作为*潜在*梨子包——目前还没有确定。

我认为这是一个有趣的想法——对我来说，这是一个类似电子表格的东西——你从一些原始数据(如数据库结果集)开始，然后将其转换/聚合成人类可以使用的文档。

我认为这与我们在 Sitepoint 的高级 PHP 论坛上的一个讨论有关，这个讨论问[PHP 应用程序从根本上来说是数据驱动的吗？](https://www.sitepoint.com/forums/showthread.php?t=154931)。一个特别的[惊人的评论](https://www.sitepoint.com/forums/showpost.php?p=1121245&postcount=10)，来自[杰夫](http://www.procata.com/blog)；

> {在 PHP 中…请求的典型“流程”是 DB >查询>转换数据>渲染到浏览器。数据必须尽可能快地到达最终阶段，因为没有将数据存储在内存中的机制。}

> 把这看作是文档驱动，也许会更有意义。“文档”可以是数据。文档不一定意味着 XML/SGML 衍生物。

> 例如，在 WACT，数据空间接口表示“PHP 变量”格式的文档。PHP 的关联数组构成了一个非常灵活的“文档”在 WACT，数据库类生成数据空间。模板/视图消耗数据空间。控制器(动作)转换数据空间。

> 文档驱动架构相对于面向对象架构的优势在于组件之间的松散耦合。

> 也许它的文档驱动风格非常适合 PHP 和 web 开发。

> (哦，大型机的人做文档驱动已经很久了。他们称他们的文档为消息，称他们的软件为面向消息的中间件。)

通过以这种方式考虑“文档”,我认为有可能为公共文档“转换”构建有用的库，尽管最初的印象表明没有足够的“共同点”。

其中一些来自于我自己对[梨::日历](http://pear.php.net/Calendar)的体验，如这里提到的

> 以我自己的例子 PEAR::Calendar 为例，让我编写它的原因是无法找到一个从输出(HTML)和/或数据存储(通常是一个 MySQL 表)中抽象出来的 Calendar 类。PEAR::Calendar 计算日历数据结构(PHP 数组)，将它们封装在对象中，这些对象提供了一个 API，在生成 HTML 格式的日历时很有用(XML 类型的文档通常是按顺序呈现的)，例如[这个例子](http://pear.php.net/manual/en/package.datetime.calendar.intro-inahurry.php)。它严重依赖[装饰者](http://pear.php.net/manual/en/package.datetime.calendar.intro-decorators.php)来增加功能。有一些地方它可以更智能(比如数据结构的惰性评估，与当前的 build()方法相反)，但是，一般来说，我认为它对 PHP“有效”(我认为我的设计很幸运，尽管你可能不同意)。我不认为 PEAR::Calendar 适合于持久存储在内存中并可能成为日历应用程序焦点的环境。对于初学者来说，访问日历对象集合的 API 太有限(尽管这可能会改变)，保持日历数据结构的完整性(例如，一个月中的天数)可能是个问题。

换句话说，尽管乍一看，HTML_Template_Report 似乎试图吃掉很大一部分，但实际上，由于是面向文档的，它可以很好地为构建“报告”提供有用的基础。

同意作者的观点，名称需要更改——可能类似于“Document_Aggregate”或“Document_Report”。还有，从表面上看[代号](http://www.i-node.com.br/report/docs/index.php)，对设计有些怀疑。

主类 [Report](http://www.i-node.com.br/report/source.php?script=Report.php) 似乎做得太多了(已经有了货币和日期相关的功能——等到有人要求 i18n…)。“回调”方法可能很好地解决了这个问题，但是需要支持对象方法和本地 PHP 函数 IMO。

我也认为作者在构建输出格式的知识方面遇到了困难。为什么不把报告准备成数据结构，让用户生成输出呢？

## 分享这篇文章