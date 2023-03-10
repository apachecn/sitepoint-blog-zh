# 支持 PHP 的咆哮

> 原文：<https://www.sitepoint.com/a-pro-php-rant/>

好的——接下来是[这里的](https://www.sitepoint.com/tim-bray-on-php/)和[蒂姆](http://www.tbray.org/ongoing/When/200x/2006/02/17/PHP)后来的评论(事实上更多的是乔纳斯·毛鲁斯的咆哮)；

> 有人给我发一些好的支持 PHP 的言论怎么样？

因此，这里有一些亲 PHP 的咆哮…

**这是执行模型**

把 PHP 作为一个 Apache 模块，两件大事是*它能工作*和*它是可伸缩的*。更重要的是，没有人真正有一个执行模型可以与之相比，除了微软的 ASP 3.0，他们已经放弃了。在你暴跳如雷之前，想想这个。

很久以前就试图解释这些基础知识了[这里](https://www.sitepoint.com/lazy-php-part-1/)——重要的是从中得到的东西(与 mod_perl / mod_python / mod_*甚至“X”应用服务器相比。)是不是*解释器在每次请求后都返回到一个新鲜的状态*(没有全局变量在附近徘徊或其他)。PHP 真的是[无共享](http://www.oracle.com/technology/pub/articles/php_experts/rasmus_php.html)。你想要缩放？试试这里的。

与此同时，在这些长尾热情的日子里，除了 PHP，你不会听到太多关于什么时候事情很糟糕的消息。具体来说，除非你提供免费短信，否则不要把你的 FastCGI 带给我，这样我就可以在它崩溃时提醒自己。我拥有/运行着一些基于 PHP 的小型网站，我已经几个月没看这些网站了，但是当我下次去那里的时候，它们仍然神奇地运行着——不管是不是印象深刻，PHP 一直在运行——重启 Apache 或者重新启动，不需要系统管理员的努力就可以恢复。

更具体地说，PHP 测试最多的执行模型是 mod_php 和 CGI，其中没有“长时间运行的脚本”,也不需要线程。PHP 针对该环境进行了优化。相比之下，Perl、Python 和 Ruby 是通用语言，针对不同的需求进行了优化。与 PHP 相比，web 只是他们支持的另一个平台，而 PHP 的主要平台是 web。这可以用配置设置来表达，比如 [max_input_time](http://www.php.net/manual/en/ref.info.php#ini.max-input-time) 和[post _ max _ size](http://www.php.net/manual/en/ini.core.php#ini.post-max-size)——对于 PHP，这些问题已经有人在思考了。

**出色的数据库支持**

让我们从通常的唠叨开始 PHP 没有一个用于数据库抽象的公共 API。不管怎么说，这一直都是假的——SQL 本身很少具有可移植性，并且针对特定数据库编写应用程序需要特定的工作，所以你的 API 恰好与其他数据库的 API 相同在很大程度上是不相关的。也就是说，如果你用另一个数据库开始另一个项目，一个通用的 API 确实使学习曲线变得更容易，但 PHP 的首要任务是公开“特定于供应商”的特性，如 [pg_send_query](http://www.php.net/pg-send-query.php) 或 [mysql_unbuffered_query](http://www.php.net/mysql_unbuffered_query) 。换句话说，大多数 PHP 的 DB APIs 都与供应商的客户端 API 有一对一的关系，这样做的好处是您不会丢失特性，也不必为之而战。

这并不是说你不能在 PHP 中拥有你的数据库抽象蛋糕——我最信任的是[ADOdb](http://adodb.sourceforge.net)—一个原生 PHP 实现(附带说明——Python 开发者可能有兴趣知道 John 也维护了一个[ADOdb 的 Python 版本](http://phplens.com/lens/adodb/adodb-py-docs.htm)。还有正在实现的 [PDO](http://www.php.net/pdo) ，以及(我认为)与 Perl 的 DBI 类似的东西。还应该指出 [PEAR::DB](http://pear.php.net/package/DB) 、 [PEAR::MDB2](http://pear.php.net/package/MDB2) 和[克里奥尔语](http://creole.phpdb.org/trac/)。不要让我开始 ORM…

没有说的是 PHP *可能*现在拥有 Perl、Python 和 Ruby 中任何一种的最好的(最稳定和特性最完整的)全面数据库支持，从一个角度来看，考虑到 PHP 用户的数量，这是有意义的。PHP 的数据库支持优于 Python 或 Ruby，这可能不是什么新闻，但与 Perl 相比，我最近惊讶地发现 DBI 缺乏对 Oracle collections 的支持，而 [PHP 有](http://www.php.net/manual/en/function.oci-new-collection.php)。也许这只是一个怪胎，但也可能不是——有人有经验可以分享吗？

**PHP 数组**

现在，计算机科学家通常讨厌 PHP 的数组，它们既被索引又被散列。事实上，它们不仅对于初学者来说很容易上手，对于 web 问题来说也很方便(例如，非常适合 ad-hoc XML ),对于简单的迭代来说，用通用的语法和行为来统一散列和索引数组是很好的。当然，它们并不支持计算机科学家可能需要的所有东西，比如 Python

```
 points = {}
points[(1,2)] = 2
points[(2,4)] = 4 

```

…但是在网络上，谁在乎呢？你几乎不需要这样的东西，在你需要的地方，JPGraph 让生活变得足够简单。与此同时，相比之下，表现[还不错。](https://www.sitepoint.com/forums/showpost.php?p=2188994&postcount=1)

BTW，如果你想要计算机科学和 PHP，看看([当前最喜欢的](https://www.sitepoint.com/j4p5-javascript-for-php5/)离奇的 PHP 项目) [J4P5](http://j4p5.sourceforge.net/) (一个用 PHP 编写的 Javascript intepreter)。如果你感兴趣的话，我可以想出无数类似的项目。

更一般地说，有一个虚拟的网络环(如何 90 年代的)的 PHP 仇恨从[这里](http://tnx.nl/php)开始。你会发现那里 90%的批评都是无关紧要的(如果我愿意的话，有一天我会详细地做这件事)——这些要么不是 web 开发的问题，要么随着 PHP(过山车)的学习曲线而消失(例如，如果你正在编写类，那么全局名称空间中无穷无尽的函数是无关紧要的)。有一天，我也可能会对为什么 PHP 如此糟糕发表自己的看法(从[比雅尼·斯特劳斯特鲁普](http://en.wikiquote.org/wiki/Bjarne_Stroustrup)和“只有两种语言:人们抱怨的语言和没人用的语言”的意义上来说)，我希望这是一个非常不同的故事。今天你得原谅我只关注好的方面。

**SPL 延伸段**

从数组开始，[如果你是程序员，这个](http://www.php.net/spl)是使用 PHP5 的一个重要原因。它让许多与网络相关的问题变得非常容易解决*和*给你带来语法上的快乐。点延伸阅读[这里](http://wiki.cc/php/SPL)、[这里](http://www.phpriot.com/d/articles/php/oop/oop-with-spl-php-5-1/)、[这里](https://www.sitepoint.com/php5-standard-library/)、[这里](http://www.devshed.com/c/a/PHP/The-Standard-PHP-Library-Part-1/)和[这里](http://www.devshed.com/c/a/PHP/The-Standard-PHP-Library-Part-2/)。

**PHP 5(.1) XML 支持**

一个是 Tim–[PHP](https://www.sitepoint.com/premium/library)5(. 1)中的 XML 支持非常出色，这要感谢 [libxml2](http://xmlsoft.org/) 。这不仅仅是因为核心解析器很好，而是因为它在 PHP 中有多个 API，即 [SAX](http://www.php.net/xmlreader) 和更快的【invert】[XML 拉取阅读器](http://www.php.net/xmlreader)、 [SimpleXML](http://www.php.net/simplexml) 和 [DOM](http://www.php.net/dom) ，以及 XSLT 等支持组件。当然还有 Python，Perl，Ruby 等等。我也有 libxml2 包装器，但这仍然是 PHP 的优势，而且 libxml2 已经成为 PHP XML 功能的核心，而不是另一个替代品。

**说有用的东西…有用**

为了他们所信仰的特定范式，程序员痴迷于为自己建造更好的笼子。这没什么错，但通往那里的路上到处都是坏掉的或部分完工的东西。对于你在网络上需要的东西，PHP 已经存在了。这并不是说 PHP 中没有一堆不稳定的代码——只是那些你通常会用到的东西可以工作。

换句话说，PHP 已经变得可以预测，至少对我和任何愿意学习的人来说是这样。这意味着，例如，如果我需要给出项目评估，我知道什么时候我可以遵守它们。我也发现教别人很容易——如果我能直接和他们一起工作，花大约一个星期就能让一个程序员写出有用的 PHP。我宁愿用(稍微多一点的)冗长的语法和按时交付来换取 15 分钟的语法荣耀，然后是 10 小时的 bug 搜索和另外 15 小时的解决方案。幸运的是，并不是每个人都认为这种方式或进展会停止。

**Unicode 和 ICU**

谈到 PHP 已经拥有的东西，值得注意的是 PHP6 正在使用 [ICU](http://en.wikipedia.org/wiki/International_Components_for_Unicode) 作为其“核心”的一部分——参见这里的、这里的和这里的。PHP6 已经“存在”在 CVS 下，如果它只限于 Unicode 的变化，它比 PHP5 更上一层楼，而且迟早会出现。你可能会认为 PHP 比 Perl 和 Python 有更好的 Unicode 支持。

**卡在小盒子里**

这近乎圣战，我不想要。最近，语言之争又回来了，如果你认真对待它，你可能会认为 Perl、Python 和 PHP 都死了，只有 Ruby。对我来说，这只是 Ruby(正确地)与其他人一起争夺空间，最好放在这里[看。](http://lesscode.org/2005/12/22/the-philosophers-song/)

这些天在 Perl、Python 和 PHP 中快乐着，看着人们哭着说“这是 rox，其他的都是 sux”介于有趣和沮丧之间。语法只是故事的一部分。对我来说，库是更大的部分——如果你不能用它做任何事情或者需要花时间重新发明轮子，那么伟大的语法还有什么用？

例如，Perl 就像命令行上舒适的拖鞋——让我远离 [Getopt::Long](http://cpan.uwinnipeg.ca/module/Getopt::Long) 和 [POD::Usage](http://cpan.uwinnipeg.ca/module/Pod::Usage) ,尽情尖叫。重要的小事。并且发现 Perl 能够以与 SQL*Loader 相当的速度在数据库中推送大块数据。

同时认真用过一次[wxPython](http://www.wxpython.org/)([预定即将](http://www.manning.com/books/rappin)！)–其他动态语言都无法与之相比–是的，有 wxPerl、wx ruby(* *咳** wxHaskell)甚至 wxPHP，但相比之下它们还不成熟–查看“/src/python/wxPythonDemos/ide”下的 [Activegrid 的](http://sourceforge.net/projects/activegrid) IDE 下载–这些天我们都可以编写自己的 IDE 了…Python 有极好的 [Windows 支持](http://sourceforge.net/projects/pywin32)的肮脏秘密。如果我有时间的话，我想带着这个 shell 名称空间扩展演示程序和 Python 包装器为 scp 做一个 GMail 驱动程序。

关键是人们会跳到一门语言上，然后宣称，因为它能做好一件事，它是所有事情的唯一工具。考虑到 wxPython 已经存在，花费时间在 wxRuby / wxPerl 或其他上真的很可惜。更遗憾的是，我们已经为网络发明了无尽的模板引擎——不仅仅是浪费了开发时间——还浪费了*用户*识别和学习使用它们的时间(这些东西驱使他们去微软)。这不仅仅是重新发明——而是我们甚至没有真正分享想法或看到它们传播—[杰夫](http://www.procata.com/blog) [钉模板](http://www.phpwact.org/pattern/template_view)很久以前，但今天你会发现 Ruby 开发人员讨论花括号与模板属性的利弊。

具体到 PHP，如果你承认有些事情它确实做得很好，特别是执行模型，那么它就变成了一个如何在所有领域中获得最佳效果的问题。在我看来(基于我对它的满意),这是 Perl 在你的系统管理员后端，移动数据等等。，Python 帮助用户[发布他们的 MS Office](http://www.linbox.com/en/converter) 东西，PHP 向全世界提供服务。

沿着这条线，我们看到了 [Fuse](http://fuse.sourceforge.net/) 和 [Python](http://richard.jones.name/google-hacks/gmail-filesystem/gmail-filesystem.html) 和 [Perl](http://cpan.uwinnipeg.ca/htdocs/Fuse-DBI/Fuse/DBI.html) 绑定。我现在有一个非常具体的兴趣，我想它会告诉我这些东西有多稳定，但是，如果一切顺利的话，我认为有可能通过文件系统将 PHP 与 Perl 和 Python 结合起来，也许与 libxml2 结合起来。

总之:PHP 的 [Schwarz](http://greg.chiaraquartet.net/archives/116-My-language-is-bigger-than-your-language-so-nyah-nyah.html) 更大！

## 分享这篇文章