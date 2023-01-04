# PHP 对性能的追求:从 C 到 hhvm

> 原文：<https://www.sitepoint.com/phps-quest-for-performance/>

虽然最近的核心 PHP 开发的重点是新的语言特性，但近年来越来越多的关注集中在性能上。虽然这对许多用户来说已经足够了，但随着像维基百科和脸书这样的大型网站对 PHP 的使用越来越多，在更少的服务器上满足更多请求的能力变得越来越重要。

在过去的几年里，PHP 内部团队内外都在这个领域做了一些努力。然而，要准确理解正在发生的事情需要一些历史和概念背景。

## PHP 的起源

1994 年，拉斯马斯·勒德尔夫想要一种更简单的方式向互联网用户展示他的简历，并监控其流量。当时，web 开发是通过用 C 语言编写 CGI 程序来完成的，这可能很乏味，需要许多低级概念的知识，如手动[内存管理](http://en.wikipedia.org/wiki/Memory_management)。因此，拉斯姆斯开始研究[，这就是后来的 PHP](http://www.php.net/manual/en/history.php.php) 。

要真正理解这个想法在当时是多么新颖，需要理解一些概念。你可能以前听说过计算机是基于一和零运行的，这是二进制数字系统的组成部分。计算机处理器在此基础上建立了一套数字指令，统称为[汇编语言](http://en.wikipedia.org/wiki/Assembly_language)，可以翻译成二进制。这些指令仅限于相当简单的操作，比如将两个数相加。

像 C 编译器 Rasmus 这样用来构建 PHP 的编译器在此基础上接受用高级语言编写的代码，并将其转换成特定处理器的二进制代码。这些工具使编程语言更易于阅读，从而允许用比汇编所需代码更少的代码来完成更复杂的任务，并使相同的代码为具有不同指令集的多个处理器编译成为可能。

PHP 本身有些不同:它是一种[解释语言](http://en.wikipedia.org/wiki/Interpreted_language),而不是像 c 这样的编译语言。PHP 解释器是为服务器的特定处理器编译的，而不是你的程序被编译，解释器执行你的程序。这个抽象层的优点是让你不用编译就能运行你的 PHP 代码。同样的能力被吹捧为 [Java](http://en.wikipedia.org/wiki/Java_(programming_language)) 的一个优势，它出现在一年后的 1995 年，带有短语“编写一次，在任何地方运行”

然而，解释语言并不是没有代价的。编译后的程序运行速度更快，因为它们以处理器可以直接理解的指令形式存在。解释器本质上必须将处理器硬件的功能实现为软件。例如，PHP 解释器将 PHP 代码转换成[操作码](http://en.wikipedia.org/wiki/Bytecode)，它相当于汇编，并执行它们。由于这个原因，解释器通常被称为[虚拟机](http://en.wikipedia.org/wiki/Virtual_machine#Process_virtual_machines)；就像您用来在主机操作系统中运行客户操作系统的[(系统)虚拟机](http://en.wikipedia.org/wiki/Virtual_machine#System_virtual_machines)一样，它们是抽象中的抽象，以提供便利来换取性能。以 PHP 为例，Zend 引擎(PHP 4 中 1.0，PHP 5 中 2.0)就是它的虚拟机。我们的故事从这里开始。

## 会编译吗？

一个名叫保罗·比格的人决定用 PHP 来探索这个问题。他[在 2006 年加入了 phc 项目](http://phpcompiler.org/news.html)，目标是能够将 PHP 翻译成等价的 C 代码，然后这些代码可以被编译成本机代码，等等。它的开发一直持续到今天，尽管发布并不频繁。

保罗开始努力后不久，在 2007 年初， [Roadsend](http://www.roadsend.com/home/index.php) 以 pcc 的名义开始了一个具有类似目标的项目，后来这个项目被改为 [rphp](http://code.roadsend.com/rphp) 或 Roadsend PHP。然而，在撰写本文时，项目[已经有大约一年时间没有提交了](http://code.roadsend.com/rphp/changeset/1069/)。它的[支持论坛](https://groups.google.com/forum/#!forum/rphp)仍然偶尔会看到帖子，但大多数都表示使用 Roadsend 相当麻烦。该项目的 IRC 频道也显示为不活跃。

其他项目也将某种形式的 PHP 移植到类似的平台上: [Phalanger](http://www.php-compiler.net/about) 在[上运行它。NET CLR](http://en.wikipedia.org/wiki/.NET_framework) 和 [Quercus](http://quercus.caucho.com/) 和 [Project Zero](https://www.projectzero.org/) 在 [Java JVM](http://en.wikipedia.org/wiki/Java_Virtual_Machine) 上运行它。这些项目至少在一定程度上保持活跃。但是，与 phc 和 rphp 不同，它们将源代码编译成需要运行时库才能执行的字节码，而不是处理器可以直接执行的本机代码。

## 增强 PHP 性能

在 2008 年初， [original traits RFC](https://wiki.php.net/rfc/traits) 为后来被 PHP 内部团队采用的 RFC 标准和过程铺平了道路。两年后，[PHP 内部团队的两位成员 Dmitry Stogov 和 Stanislav“Stas”Malyshev 提交了一份提案](https://wiki.php.net/rfc/performanceimprovements)，概述了 Zend 引擎高达 20%的潜在性能改进，该提案后来得以实施。同年，PHP 5.3 发布，包括从 [flex lexer](http://en.wikipedia.org/wiki/Flex_lexical_analyser) 到 [re2c](http://re2c.org/) 的切换，这也导致了轻微的性能改进。很明显，PHP 的性能已经不仅仅是一个学术问题。

## 输入 HipHop

脸书[在 2010 年 2 月中旬宣布了他们的计划](https://www.facebook.com/note.php?note_id=307069903919),将整体响应能力提高两倍。几乎在同一时间，他们[宣布发布 PHP 的 hip hop](https://www.facebook.com/note.php?note_id=280583813919)，这个项目的目标类似于 phc 和 r PHP。HipHop 将 PHP 代码转换成 C++代码，然后由 g++编译器编译。这是继对 [APC 扩展](http://pecl.php.net/package/APC)和 Zend 引擎的大量性能增强贡献之后，HipHop 似乎是脸书继续使用 PHP 和维护他们现有的大量 PHP 代码库的合乎逻辑的下一步。

实现 HipHop 需要牺牲 PHP 的一些更动态的特性，比如`eval()`支持，以及重新实现 PHP 的运行时并重写它的几个扩展。然而，它消除了开发人员使用 C 或 C++以及处理这类语言编写编译代码的繁琐工作的需要。事实上，脸书声称在他们的服务器上部署 HipHop 后，CPU 使用率降低了 50%。

该项目在最初发布时引发了 PHP 社区成员的许多意见，包括 PHPUnit 的首席开发人员塞巴斯蒂安·博格曼。 [David Coallier](http://blog.echolibre.com/2010/02/hiphop-for-php-facebook-unveils-its-magic/) ，PEAR 总裁兼 echo libre CTO；还有[布兰登·萨维奇](http://www.brandonsavage.net/hiphop-for-php-who-benefits-who-doesnt/)，Mozilla 的一名开发人员。总的来说，它的反应是积极的，项目的发展仍在继续。脸书[与 PHP 社区中的几个流行项目](https://www.facebook.com/note.php?note_id=416880943919)合作，包括 Drupal 和 WordPress，帮助他们删除使用 HipHop 不支持的动态特性的代码，并修复在 HipHop 上部署这些项目时发现的错误。他们还继续提高性能，甚至实现了更高效的内存分配。

## 新的虚拟机

最近，脸书发布了 [HipHop 虚拟机](https://www.facebook.com/notes/facebook-engineering/the-hiphop-virtual-machine/10150415177928920)，或 hhvm，它得到了来自几个来源的报道，如 [Ars Technica](http://arstechnica.com/business/news/2011/12/facebook-looks-to-fix-php-performance-with-hiphop-virtual-machine.ars) 和[读写网](http://www.readwriteweb.com/enterprise/2011/12/better-faster-stronger-php-fac.php)。hhvm 像 Zend 引擎一样模糊了编译器和虚拟机之间的界限。

hhvm 是所谓的[实时](http://en.wikipedia.org/wiki/Just-in-time_compilation)(或 JIT)编译器。hhvm 不是通过 C++编译阶段将 PHP 翻译成本机代码，而是将 PHP 翻译成一种称为 HipHop 字节码(HHBC)的中间字节码语言，可以在需要时实时翻译成本机代码。

hhvm 旨在替代将静态编译的基于 HipHop 的二进制文件部署到生产环境中。在最初发布时，hhvm 提供了 60%的总体相对性能提升。

## 收场白

推动 PHP 及其架构创新发展的是用户和社区成员。脸书只是众多为 PHP 的持续成功做出贡献的公司之一。他们不仅对最初的项目进行修改，还向社区提供他们自己的 PHP 工作，并与其他 PHP 项目合作，使他们能够利用 HipHop 这样的项目。

事实上，PHP 已经从 1995 年的卑微起点走了很长的路。虽然 HipHop 可能不适合所有项目或所有服务器，但它可以为大型项目提供性能增强，并帮助它们大幅降低操作硬件要求。这绝对是一个值得关注的项目，也是 PHP 生态系统活力的证明。

图像 via[is catal](http://www.shutterstock.com/gallery-578278p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章