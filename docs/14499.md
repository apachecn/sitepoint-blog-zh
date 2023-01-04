# 思考和大脑

> 原文：<https://www.sitepoint.com/phpinking-and-the-brain/>

Via [Sebastian](http://www.sebastian-bergmann.de/blog/archives/380_TIOBE_Programming_Community_Index.html) ， [Kristian](http://blog.koehntopp.de/archives/406_Was_machen_wir_morgen_abend,_Pinky.html) : [Zend in the 小丑](http://www.kuro5hin.org/story/2004/7/14/232752/474)——对 PHP5 持怀疑态度，但最终是积极的看法(同时漫游 PHP4 和更多内容)。

在问“为什么？”当考虑 PHP5 新的、类似 Java 的能力时，作者得出了这个“邪恶的小手指和大脑”的结论；

> 可以毫不夸张地说，PHP v5 代表了一种脱离“简单服务器端脚本程序”角色的协同努力，最终目标是成为一种通用的、易于编程的、通用的、跨平台的解释语言。他们不只是借用 Java 的语法；从长远来看，他们实际上是在追逐 Java、Perl、Python、dotNET 和 VB 的利基市场…

那么，PHP 还需要什么才能长出翅膀，成为一门严肃的通用语言呢？一些我想不起来的事情；

–内置操作码缓存(如今 Zend 必须有更好的商业模式，例如商业扩展)

–sterling[抽象数据类型扩展](http://cvs.php.net/pecl/adt)成熟

–[线程默认为](http://cvs.php.net/pecl/threads)

—[wxWidgets](http://wxwidgets.org/)的包装器

–名称空间

–安全模式变得安全

–更好的垃圾收集？(这里没有证据，但坊间观点是“长时间运行”的 PHP 脚本倾向于占用内存——在使用 PHP 和 Apache 时，这不是一个典型的问题)

–类似 Python 的 [psyco](http://psyco.sourceforge.net/) 内置的东西。

到目前为止，这并不是遥不可及的，只要有足够的人力(即资金)就可以实现。

还有别的吗？

## 分享这篇文章