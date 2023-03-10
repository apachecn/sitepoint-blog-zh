# PHP 大师:编写前沿代码

> 原文：<https://www.sitepoint.com/php-master-writing-cutting-edge-code/>

你是否已经利用了 SitePoint 为最新 PHP 书籍推出的限时特惠，Lorna Mitchell、Davey Shafik 和 Matthew Turland 合著的[PHP Master:Write the Edge Code](https://www.sitepoint.com/books/phppro1/ "Home - PHP Master: Write Cutting-Edge Code")？如果不是，你就是疯了！这本书是为了帮助你成为一名更好的 PHP 程序员而写的，非常棒。它不仅涵盖了高级 PHP 编码主题，如面向对象编程和设计模式，还涵盖了同样重要的外围主题，如安全性、性能分析和部署。如果你准备好永远挂上新手的帽子，成为一名专业级的 PHP 开发人员，这本书就是为你准备的。

从我开始翻阅它的页面的那一刻起，我就感到惊喜(事实上，当然是因为我有 PDF 版本)；讨论的主题混合得很平衡，作者的写作风格使有时困难的概念清晰易懂。作者本身就是真实世界的 PHP 开发人员，所以他们以实用主义的态度对待这些主题，并为您提供合理、实用的建议，而没有当今行业中的大肆宣传。

PHP Master 在第一章中从奠定面向对象编程的基础开始，然后在第二章中介绍通过 PDO 与关系数据库如 MySQL 的接口，在第四章中介绍几种有用的设计模式。第 3 章很好地介绍了如何使用和添加使用 RPC、SOAP 和 REST 传输信息的 API。

本书的后半部分涵盖了安全性和质量保证主题。第 5 章专门讨论安全性，并解释如何保护自己免受跨站点脚本攻击、会话固定和劫持、SQL 注入攻击等。使用 Apache 基准测试工具`ab`和 JMeter 的性能测量，以及实现缓存策略的指导，将在第 6 章中介绍。第 7 章介绍了自动化测试概念，以确保您的代码库在整个开发过程中功能正确，第 8 章讨论了用于提高代码质量和可维护性的度量标准。

这本书后面的三个附录可以被认为是很酷的附加章节。许多书只是在附录中加入了额外的琐碎信息，但是 PHP Master 中的附录提供了关于 PEAR 和 PECL(从安装包到创建自己的包和通道)、标准 PHP 库(包括注册多个自动加载器、目录迭代器和数据结构)的全面信息，以及如何继续成长为 PHP 开发人员的建议。

这本书里有一些小古怪，但它们绝不是交易的破坏者。例如，第 6 章中标有“使用和不使用 Memcached 时的性能图”的图表显示了在文件系统、MySQL 和 Memcached 中存储会话数据的性能影响，尽管从未涉及到基于 MySQL 的场景；也许它在编辑过程中被修剪了，而不是所有的引用都被删除了。另一个是`ab`被覆盖了两次，一次在第 6 章，另一次在第 7 章，当一本书有多个作者时，这个疏忽很容易犯。不过，我本以为在书付印之前，编辑或校对会发现它。其他令人敬畏的内容掩盖了我发现的几个 oopses。

我赞赏对当前议题采取的冷静态度；例如，作者的实用主义在第四章中令人耳目一新。我已经看到设计模式的流行程度上升到 fanboi 的高度，但它解释了为什么模式是有用的，但强调了为正确的工作使用正确的工具(或在这种情况下的设计模式)的重要性，如下面的引用所示:

> 您可能会在许多设置中看到这种模式，如果合适的话，现在您也可以将它构建到您自己的应用程序中。

展示好的实践并鼓励负责任地使用它们绝对是最佳实践！

我也很喜欢第 3 章中关于 web 服务的讨论和例子，也许是因为它适用于我目前正在从事的一个项目。整章都是实用的建议，例如这段关于发布 REST 服务的引用:

> 每当你发布一个 RESTful 服务时，很可能在某个地方有人会抱怨你违反了一个或多个 REST 原则——他们可能是对的！REST 是一套相当学术性的原则，并不总是适合商业应用。为了避免批评，只需将您的服务推销为 HTTP web 服务。

提醒读者重要的不是时髦的词语和教条式的争论，而是他们如何利用他们描述的技术来创造令人兴奋的应用。

使用图书评级的 Slashdot 等级，其中 1 适合用来给笼子加衬里，10 注定是经典，我给 *PHP 大师:编写前沿代码*一个非常坚实的 8.5 分。内容会随着你的成长而增加，这本书不是你周末看完就捐给图书馆的东西，因为你不需要它。书中涵盖的高级编码和外围主题将帮助你提升技能水平，成为一名出色的程序员。在这个过程中，你也可以确定你得到了优秀的、扎实的教育。如果你真的想成为一名更好的 PHP 开发人员，那么你*需要*这本书放在你的书架或电子阅读器上！

## 分享这篇文章