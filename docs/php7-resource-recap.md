# PHP7 资源概述

> 原文：<https://www.sitepoint.com/php7-resource-recap/>

PHP 7 即将问世。RFC 正在实现和完善，项目正在测试，库正在升级。扩展正在被修改，这个词正在传播。剩下的就是让共享主机跟上升级的潮流——这可以说是改善 PHP 全球状态最困难的部分。在本文中，我们将看看一些最重要的 PHP 7 相关资源和技巧，您应该在准备新版本时仔细阅读。

![Php7 logo by Vincent Pontier](img/6fe21036b0b67e14a6bf320af6edf953.png)

*形象通过[【文森特庞蒂埃】](https://twitter.com/Elroubio/status/583414360350949376)*

## 拉斯莫斯盒子

几个月前，拉斯马斯·勒德尔夫创建并上传了一个流浪者盒子，用于在 PHP 7 上测试。这个盒子可以从 Github 下载[。如果说明令人困惑(不应该)，请看](https://github.com/rlerdorf/php7dev)[罗布·艾伦的帖子](http://akrabat.com/building-and-testing-php7/)。这个盒子在几分钟内启动，不仅支持 PHP 7，还可以非常容易地更新到 PHP 7 的最新版本，以及其他几十个版本。

这个盒子有一个`newphp`命令，可以立即用另一个预编译的二进制文件替换当前使用的 PHP 版本。例如，在框中输入`newphp 55`将从 PHP 7 切换到 PHP 5.5。，允许您在另一个版本中快速运行代码。此外，每个版本都有自己的 PHP 扩展文件夹，因此您可以轻松地编译和安装新的扩展——它们都位于正确的位置，不会与其他 PHP 二进制文件冲突。

在某种程度上，这类似于 [DUnit](https://github.com/Vectorface/dunit) 使用 Docker 容器来启动不同的 PHP 版本供您测试。

要了解更多信息和一些来自 Rasmus 本人的各种 CMSes 的有趣基准，请参见他的这个演讲幻灯片。

## 其他盒子和测试

你应该在 PHP 7 上测试你的代码。早期的基准测试[很有希望](http://www.intracto.com/nl/blog/running-symfony2-on-php7)，许多[正在跳上船](http://www.reddit.com/r/PHP/comments/32tms8/php7_and_your_open_source_applications/)。有些人甚至测试了[他们的现实世界应用](http://www.reddit.com/r/PHP/comments/305ck6/real_world_php_70_benchmarks/)，并得到了一些令人印象深刻的结果。

使用上面的 Rasmus' box，或者如果你正在使用 [PuPHPet](https://www.sitepoint.com/build-virtual-machines-easily-puphpet/) ，他们最近[增加了 PHP7 支持](http://blog.puphpet.com/blog/2015/04/22/php7-support/)。有一些警告，但总的来说，它应该工作。Vaprobash 和 Phansible 还没有出现，但还是有希望的。

如果你想开发自己的，这本适用于 Ubuntu / Debian 的可翻译剧本可能会有所帮助，或者你可以使用完整版本，然后[按照这些说明](http://www.freeklijten.nl/home/2015/03/12/Testing-and-improving-PHP-extensions-for-PHP-7)去做——这篇文章还提供了为实际的 PHP 版本编写测试的技巧，所以你可以为语言本身做出贡献！

如果你不想启动虚拟机，你可以使用 Zend 的一些官方每夜构建版本(T1)(也通过 Docker 支持 T2)，但是在 Travis 中也很容易做到，Travis 的构建版本中现在包含了 PHP 7。事实上，[的前肢/骨骼](https://github.com/thephpleague/skeleton)已经内置了这个。请记住——如果您使用 Travis 的结果来[power specialist 的代码分析](https://www.sitepoint.com/inspecting-php-code-quality-scrutinizer/)——您应该[允许 PHP 7](https://github.com/thephpleague/skeleton/blob/064acec279efc2966754ae02784bcd905e2f1a55/.travis.yml#L12) 上的失败，甚至可以确保 PHP 7 的报告根本不会被提交。

在 Travis 上测试 PHP7 不仅可以确保您的代码在最新的夜间运行，而不会强迫您手动更新您的 PHP7 版本，还可以让您获得一些街头信誉来炫耀——“我的代码已经可以使用 PHP7 了！”。

## 更新扩展

然而，不仅仅是库、包和应用程序需要测试。就像[三月时事通讯](http://sitepointnewsletters.createsend1.com/t/ViewEmail/y/5293F4CD070ACA27)说的那样(你可以通过[这种形式](https://www.sitepoint.com/newsletter/)，顺便说一句，订阅它)，延伸是在前线。PHP7 的新扩展 API 与旧版本有很大不同，一些扩展需要大量重写才能保持兼容。

考虑到这一点， [gophp7-ext](http://gophp7.org/gophp7-ext/) 页面的建立是为了方便大多数扩展开发人员的转换。

希望这将使像 [Zephir](http://zephir-lang.com/) 和新发布的 [Phalcon 2](http://phalconphp.com/en/) 这样的项目尽快变得 7 友好(正如 [Phalcon 团队所说](http://blog.phalconphp.com/post/116664774400/phalcon-2-released)，他们已经在考虑必要的 PHP7 升级)。

如果你开发 PHP 扩展(也许你是在看了一些[我们的教程](https://www.sitepoint.com/developing-php-extensions-c-php-cpp-advanced/)后开始的？)或者认识这样做的人——把他们指向 GoPHP7-ext 页面，让他们把代码提高到标准水平。如果你能让他们写下这个过程，记录需要做的改变，请给他们指出[我的方式](mailto:bruno.skvorc@sitepoint.com)，我们将公布他们的发现(并为此付给他们丰厚的报酬)，这样其他人可以从学习这个过程中受益。

## 重要阅读

最后，让我们列出一些你应该浏览的最重要的阅读资源，以了解即将到来的变化。

*   Phil Sturgeon 发布了关于 PHP7 特性冻结和当前状态的帖子。它列出了一些更重要的 RFC，但是你应该看看下面 Davey 的帖子，以获得更详细的概述。

*   [期待什么](https://blog.engineyard.com/2015/what-to-expect-php-7)是你应该读的第一篇帖子。这是 Davey Shafik 的两部分系列，以务实的方式解释了即将到来的变化。它将列出所有重要的被接受的 RFC，并显示每个 RFC 的演示代码，因此您可以通过实际例子来了解它们如何影响您的工作流。

*   来自 Zend 的 PHP7 概览是另一个很好的概述。它列出了所有 RFC 及其对开发人员生活的潜在影响，以及 BC 中断的可能性(有一些在边缘案例中，但没有一个是确定的)。

*   [比较 PHP7 和 Hack 类型的系统](http://www.dmiller.io/blog/2015/4/26/comparing-the-php7-and-hack-type-systems)在 PHP7 和 Hack 中使用类型提示实现了相同的功能(现在他们开始使用 PHP)。这些差异可能会让你感到惊讶，也可能是值得记住的事情。这个帖子已经在 Reddit 上被[讨论了。](http://www.reddit.com/r/PHP/comments/33z69v/comparing_the_php_7_and_hack_type_systems/)

*   如果你对严格类型感到困惑，[这篇相关的文章将对其进行分解](https://medium.com/the-white-hat-elephpant/php7-more-strict-but-only-if-you-want-it-to-be-78d6690f2090)。

*   [PHP7 的革命——返回类型和移除的工件](https://www.sitepoint.com/php-7-revolution-return-types-removed-artifacts/)是我们自己对 PHP7 的初步观察，从很久以前开始，那时发布的消息还在热卖。(链接于 2015 年 5 月 14 日添加)

## 性能统计

*   Zend 发布了这张关于性能的[令人印象深刻的信息图](http://www.zend.com/en/resources/php7_infographic)。但是，它没有基准源代码或环境细节，所以要有所保留。(链接于 2015 年 5 月 14 日添加)

## 包扎

如您所见，有很多内容需要浏览，而版本 7 还有几个月的时间。我们将定期用新资源更新该列表，所以请确保您不时查看——新添加的帖子将被放在各自列表的底部，并标有添加日期。

你知道我们错过了什么有价值的资源吗？想为我们写点什么吗？让我们知道！

## 分享这篇文章