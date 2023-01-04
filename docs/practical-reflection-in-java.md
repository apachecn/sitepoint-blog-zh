# Java 中的实践反思

> 原文：<https://www.sitepoint.com/practical-reflection-in-java/>

via[Java.net](http://today.java.net/pub/a/today/2004/05/05/HardcoreJava.html)–[Java 中的实践反思](http://today.java.net/today/2004/05/05/HardcoreJava.pdf) (PDF)。这是奥莱利的[硬核 Java](http://www.oreilly.com/catalog/hardcorejv/) 中的一章。我知道这不是 PHP，但是值得一读。即使你不使用 Java，理解它是关于什么的也是值得的，特别是考虑到 [JSR 223](http://www.jcp.org/en/jsr/detail?id=223) ，PHP 和 Java 之间的互操作开始看起来像是 PHP 开发人员应该能够应付的事情。

我个人认为，探索一门语言的反射能力是现有的程序员学习新语言的一个很好的方法。例如，马克·皮尔格林在[钻研 Python](http://www.diveintopython.org/power_of_introspection/index.html) 方面做得很好。我发现这是理解 Python 的一条非常有用的捷径。

除了手册之外，我不知道网上有什么讨论 PHP 反射 API 的东西。PHP4 提供了像 [is_a()](http://www.php.net/is_a) 和 [get_class()](http://www.php.net/get_class) 这样的基础，而 PHP5 有一个新的反射 API (docs [目前在这里](http://sitten-polizei.de/php/reflection_api/docs/language.reflection.html))。对于 PHP，我猜它是从 [print_r()](http://www.php.net/print_r) 和 [var_dump()](http://www.php.net/var_dump) 开始的——这是程序员理解 PHP 数据结构的两种非常方便的方法。

术语注释:在我的心理词典中，术语“反射”和“自省”指的是同一个意思，指的是编程语言自我检查的能力。也许这需要纠正。

## 分享这篇文章