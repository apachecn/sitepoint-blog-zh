# 从零开始的数据库网站

> 原文：<https://www.sitepoint.com/databased-websites-scratch/>

关于建立和维护数据驱动网站的文章已经很多了。这些站点通常分为三层。术语“三层”指的是数据驱动网站中的隐喻层:

##### 内容、程序逻辑和表示

**【内容】**指的是网站中的所有文本——不是导航链接或图像，而是原始的文本信息，如新闻文章或产品描述。

**‘程序逻辑’**指的是处理用户对页面的请求，并按照一定的规则显示出来的代码，比如:如果请求来自 Linux 机器——显示这个页面。

**‘表示’**指的是处理内容格式并以适当的格式显示内容的代码，比如 HTML 或 XHTML。

例如，我的网站存储在 MySQL 数据库中。页面请求由 PHP 编程语言处理，表示由包含 HTML 的模板处理。

##### 数据驱动解决方案的优势

*   将内容从站点的表现形式中分离出来，可以让您以不同的方式显示内容(比如通过无线设备)，或者显示特定于浏览器的模板(见下文)。
*   模板可以让你在几分钟内改变整个网站的外观。如果您决定需要添加一个链接，或者您对您的版权文本进行了修改，您可以更新一个文件(而不是数百个)并立即影响整个网站。
*   任何能填写在线表格的人都可以进行维护和编辑——你不需要编辑人员了解 HTML，事实上，根本不需要任何代码。例如，如果您需要更新产品描述，您可以使用专门为此目的设计的简单表单。

您可能会注意到，我在这里没有提到任何查询或电子商务数据库功能——它们超出了本文的范围。

##### 谁需要数据库？

如果你的网站内容一个月会被更改或添加不止一次或两次，那么你应该考虑使用一个数据库来存储内容，并建立一个在线管理程序来管理它。该解决方案在开始时需要多做一点工作，但从长远来看，它将为您节省时间和金钱。

如果你打算维护一个非常小并且相当静态的站点(比如一个在线名片),那么坚持传统的静态方法——这应该足够满足你的需求了。

如果你想建立一个数据驱动的网站，或者只是想在我们已经提到的主题上找到更多的技术信息，试试这些资源:

[www.mysql.com](http://www.mysql.com/)
www.php.netwww.phpbuilder.comwww.devshed.comWebmasterbase

好了，现在你已经决定你需要一个动态的、灵活的站点结构，而数据库就是为你准备的。那么接下来呢？

##### 你需要什么

如果你要建立一个动态网站，你需要几样东西，即:

*   关系数据库管理系统(或 RDBMS)，
*   一种编程语言，可以帮助你处理数据
*   从代码中提取表示的模板系统。

我假设你已经知道 HTML，你有一些网站建设的经验，这将留给我们这三个重要的选择。

***哪些 RDBMS？***

对于当今大多数中小型网站来说，MySQL 就是答案。为什么？因为:

*   它非常强大
*   它非常快
*   这很简单
*   它有一个近乎狂热的在线社区，你可以从中获取知识和支持
*   免费的！

***哪种编程语言？***

基于我们已经选择 MySQL 作为我们的数据库，这里的答案是一个放弃的结论:PHP。虽然有许多其他方式与 MySQL 交互，但是 PHP 和 MySQL 的结合是有据可查的，成千上万的网站依靠这种技术的完美结合来有效地管理他们的数据。PHP 的一些好处包括:

*   它的简单
*   它的力量
*   它的社区支持
*   免费的！

***哪个模板系统？***

关于哪个模板系统最好，有两种观点:PHPLib 或 FastTemplate。有经验的程序员意见不一，两派都在激烈辩论。我选择 PHPLib 是因为与从原始 Perl 脚本移植而来的 FastTemplate 不同，PHPLib 完全是为 PHP 专门设计的。

所以我们的决定已经做出:PHP，MySQL 和 PHPLib 模板。

##### 现在去哪里？

如果你不知道 HTML，现在就离开这篇文章，过几个星期再回来，那时你已经掌握了手工编写 HTML 页面的窍门。如果你依赖于所见即所得的编辑器，比如 Dreamweaver，那么同样适用:如果你不习惯手工编辑代码，那么你需要学习。这不难，相信我。

为了使你的网站数据库化，学习你需要的语言的最简单的方法是一次学一门课程。我发现最好按以下顺序进行:

1.  学习 PHP
2.  学习 MySQL
3.  学习使用 PHPLib 模板系统

##### 服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

[PHP 官方主页](http://www.php.net/)

**下载量**

*   [php.net](http://www.php.net/downloads.php")—用于源代码和二进制包
*   [PHP Triad for Windows](http://www.phpgeek.com/phptriad.php) —完整的服务器环境
*   用于 Linux 机器的 RPM 包
*   或者查看您的经销商的下载页面

当然，下载 PHP 还有很多其他方法，但这些对大多数人来说都是最简单的解决方案。

**设置**

如果您决定不使用“现成的”安装，PHP 手册提供了在所有系统上安装的最佳资源。

PHP 手册是你最好的选择。或者你可以试试[凯文·杨克的教程](http://www.webmasterbase.com/article/228)，这是他新书的一部分，包括:

*   [在 Windows 上设置](http://www.webmasterbase.com/article.php?pid=29&aid=525)
*   [在 Linux 上设置](http://www.webmasterbase.com/article.php?pid=97&aid=525)
*   [常见的 PHP 安装问题](http://www.onlamp.com/pub/a/php/2001/04/26/php_admin.html)

网上有数百种教程可供选择。只需在[谷歌](http://www.google.com/)上进行搜索，选择与你的需求相关的东西。

**初学者教程**

如果你想快速入门，最好的方法是尝试几个初学者教程，然后继续阅读手册和其他在线特定主题教程。

*   [PHP 手册的初学者教程](http://www.php.net/tut.php)
*   [你的第一个 PHP 脚本](http://www.webmasterbase.com/article.php?pid=153&aid=525) (SitePoint 文章)
*   [Zend.com 的教程](http://www.zend.com/zend/tut/)

当然，还有一系列其他的，但我认为这三个可能是绝对初学者的最佳来源。

**通用教程和知识来源**

*   [手动](http://www.php.net/manual/en/)
*   Zend.com 的开发者版块
*   [PHPBuilder.com](http://phpbuilder.com/)
*   [Devshed.com 的 PHP 部分](http://www.devshed.com/Server_Side/PHP/)
*   [奥莱利的 PHP 教程](http://www.onlamp.com/php/)

我不得不再次说，有几十个在线 PHP 资源，但上面列出的代表了最好的。

**社区论坛**

这些网站是与其他开发人员交流并获得帮助的最佳场所，这些帮助是您无法找到的，或者是您无法通过手册理解的。

*   [PHP builder 论坛](http://phpbuilder.com/forum/)
*   [SitePoint 的 PHP 和 MySQL 论坛](http://www.sitepointforums.com/forumdisplay.php?s=&forumid=34)
*   [Devshed 的 PHP 论坛](http://forums.devshed.com/forumdisplay.php?s=&forumid=5)

现在你知道了！给这个页面做个书签，去找点乐子，当你自信地将 PHP 嵌入到你的 HTML 页面中时，回来开始下一部分。

##### 关系型数据库

[MySQL 官方主页](http://www.mysql.com/)

这一节明显比上一节要短，因为上面列出的许多资源也是 MySQL 上优秀的信息库。我将重复那些特别值得注意的地方，但是随着 PHP 和 MySQL 的发展，你真的应该去看看前面提到的几乎所有的网站。

**下载**

*   [MySQL.com](http://www.mysql.com/downloads/index.html)
*   [PHP Triad for Windows](http://www.phpgeek.com/phptriad.php) —完整的服务器环境
*   【Linux 机器的 RPM 包
*   或者查看您的经销商的下载页面。

**设置**

上面大多数 PHP 资源的链接也包括 MySQL 安装，作为交易的一部分。然而，这里有几个 MySQL 特有的站点:

*   [MySQL.com——在 Linux 上安装](http://www.mysql.com/doc/L/i/Linux-RPM.html)
*   [MySQL.com–在 Windows 上安装](http://www.mysql.com/doc/W/i/Windows_installation.html)

**初学者教程**

如上所述，一些更著名或更具体的资源包括:

*   [MySQL.com 的初学者教程](http://www.mysql.com/doc/T/u/Tutorial.html)
*   【MySQL 入门教程
*   使用 PHP 和 MySQL 建立一个数据库驱动的网站

**通用教程和知识来源**

*   【MySQL 官方文档
*   [Devshed 的 MySQL 版块](http://www.devshed.com/Server_Side/MySQL/)
*   [奥莱利的 MySQL 文章列表](http://www.onlamp.com/onlamp/general/mysql.csp)

**社区论坛**

有价值的资源包括:

*   [数据库对话](http://phpbuilder.com/forum/list.php3?num=5) — PHPBuilders 数据库论坛
*   [SitePoint 的 PHP 和 MySQL 论坛](http://www.sitepointforums.com/forumdisplay.php?s=&forumid=34)
*   [Devshed 的 MySQL 论坛](http://forums.devshed.com/forumdisplay.php?s=&forumid=4)

你还在等什么？去学习 MySQL 的东西，当你能用 PHP 使用和操作简单的数据库时，再回来进入下一节！

##### PHPLib 模板系统

PHPLib 官方主页

希望此时您正在愉快地嵌入 PHP 代码，将数据从 MySQL 直接提取到您的 HTML 页面中。如果你觉得这样做有趣又刺激，那就太好了！如果你已经注意到 HTML 和 PHP 代码的混合有时会变得有点混乱，那么你将会爱上 PHPLib 模板。

PHPLib 模板系统实际上是 PHP 开发人员有用工具的更大集合的一部分，这些工具都在 PHPLib 的标题下。出于本文的目的，我将简单地讨论一下可以作为独立类使用的模板类，您可以在页面顶部 **require()** 它。

**下载**

这很好也很简单，因为一个类只是一系列函数(或方法)，因此只是一个文本文件。但是，您需要下载整个 PHPLib 来解压缩该文件。点击此处下载。

一旦你下载了这个包，解压它并从 **php** 目录中获取 template.inc 文件。

**设置**

嗯，其实没有什么需要设置的，只是在你的页面顶部 **include()** 或者 **require()** 它。

**教程**

请记住，类只是一组函数，您可以像 PHP 中任何其他预建或用户定义的函数一样使用，只有少数几个教程可用。下面列出的两个都很棒，并且提供了一个很好的起点。

*   [模板，PHPLIB 方式](http://www.phpbuilder.com/columns/david20000512.php3)
*   [PHPLib 模板](http://www.devshed.com/Server_Side/PHP/PHPLib/)

**社区论坛**

你最好在[PHPbuilder.com](http://www.phpbuilder.com/forum/)上发布问题，因为有相当多的模板铁杆粉丝总是乐意在这个特定的论坛上伸出援手。记住在你的帖子上写一个清晰的主题。类似于**‘PHPLib 模板查询’**的东西应该可以做到这一点。

好了，朋友们，我希望这篇文章对你们有用，并且你们会喜欢使用 PHP、MySQL 和 PHPLib 模板类设计你们的动态页面。

## 分享这篇文章