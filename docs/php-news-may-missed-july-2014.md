# 你可能错过的 PHP 新闻——2014 年 7 月

> 原文：<https://www.sitepoint.com/php-news-may-missed-july-2014/>

少数新闻再次出现，并没有真正得到应有的关注，所以我将利用这个机会重复其中一些并解释其他的。这里的“新闻”通常不是全新的——相反，如果你对 PHP 社区和环境有一点点兴趣，它们是你应该注意的信息。

### Zend Rush

![](img/f4957013354ec57bb641b885d2577260.png)

Zend 是一家在名称中包含“Zend”的公司(框架、服务器、工作室、引擎……)，最近在产品更新方面非常积极。他们以新发布的 [Zend 认证考试](http://www.zend.com/en/services/training)开始了新的一年，继续对 Zend 服务器进行了巨大的更新，我们中报道了这一点，并通过[将 Zend Studio](http://www.zend.com/en/products/studio) 更新为新的主要版本而结束了这一年——现在[升级到了 11！我们将在另一篇文章中对此进行更深入的探讨。](https://www.youtube.com/watch?v=4xgx4k83zzc)

### HHVM 3.2

![](img/62d0742a6ea031e08f934c172a215e1b.png)

然而，另一个版本的 HHVM 出来了——代号为“Outkast ”,这一个引入了一堆错误修复，与流行的包和库更兼容，切换到 5.6 作为 PHP_VERSION(前瞻性思维多？)，fastcgi_params 在$_SERVER 中，等等。对于一个完整的变更日志，你应该在这里看到。

我们关于 HHVM 的其他文章对事情看得稍微深入一点，请看[这里](https://www.sitepoint.com/premium/books/hacking-the-hacker/read/1)和[这里](https://www.sitepoint.com/hhvm-and-wordpress/)。

### PHPNG 胜过 HHVM

![](img/4f9c0d97d0ba1ef039af7227fa11aeb2.png)

当我们谈到 HHVM 的话题时，我们曾经报道过 PHPNG，试图在内部加速和优化 PHP，并使其接近我们对 HHVM 等其他运行时的预期速度。他们似乎已经超越了最初的预期，因为最近的报告似乎表明，在某些情况下， [PHPNG 现在超过了 HHVM](http://blog.zend.com/2014/07/17/php-next-generation-outperforming-facebooks-hhvm/) 。

### 名称又能代表什么呢

似乎浪费了很多时间和精力。目前在 PHP 内部列表上有一场激烈的争论，关于是否应该跳过下一版 PHP 中的版本 6，而直接称之为 PHP 7。后者的论点包括一些相当荒谬的论点，如“没有理由不这样做”或“有些书名中有 PHP 6 的书就在那里”，但请务必阅读[菲尔·斯特金的《关于它的观点》](http://philsturgeon.uk/blog/2014/07/neverending-muppet-debate-of-php-6-v-php-7)以理解全部的荒谬之处。总而言之，在这一点上，我完全同意 Phil 的观点——只要就某件事达成一致并付诸行动，在这场辩论上已经浪费了足够的时间，最终，它将被称为什么一点也不重要。

### 杀死 PSR-0

正如我们已经提到的菲尔·斯特金，请允许我介绍他的另一篇文章。在这一集中，他主张 PSR-0 的死亡，并希望每个人都开始使用 PSR-4。我们[在过去的一篇文章中描述了这种差异](https://www.sitepoint.com/battle-autoloaders-psr-0-vs-psr-4/)，但是请仔细阅读他的文章，看看为什么不立即升级毫无意义。

### PHP 5.6 规范

脸书团队正在[创建 PHP 5.6 语言规范](http://grokbase.com/t/php/php-internals/147p423vvz/php-language-specification)。一个正式的规范，允许任何人详细了解 PHP 内部的工作原理。这将允许更快的错误修复，更好的替代运行时，更高质量的扩展，也许最重要的是，将减少各种 PHP 陷阱的模糊性，如类型转换等。

如果你想对这个话题进行更彻底的阅读和一些讨论，请参见 [reddit 主题](http://www.reddit.com/r/PHP/comments/2bfdwl/facebook_shepherding_a_php_language_spec/)——它包含了大量的信息和一些实际的解释。

### 有趣的 RFC:标量类型提示和 PHPNG 合并

![](img/7ea6054d91eaa38c7d19cc010bc5d7d0.png)

有趣的 RFC #1 是关于 PHPNG 是否应该被移到即将到来的 5.6 的主版本中，作为新版本的基础的讨论。这里的见[，这里的](http://grokbase.com/t/php/php-internals/147nyavwwv/rfc-move-phpng-to-master)见[。虽然我肯定是生活在前沿，但我不认为社区是，我们肯定不需要另一个双爪锤来实现一些没有真正被广泛测试的东西。在稳定性和当前扩展支持方面进行实验将是一个非常冒险的尝试，我更希望基于 PHPNG 的 PHP 是一个我们可以下载和部署的独立分支，就像 HHVM 一样。等到 PHP 6/7 合并——不用急。](https://wiki.php.net/rfc/phpng)

有趣的 RFC #2 处理标量类型提示以及如何最好地实现它。它最初是由安东尼·费拉拉创作的，所以你知道它很好——读一读。标量类型提示是专业 PHP 社区渴望已久的东西，也是 HHVM 和泽菲尔默认提供给我们的东西，所以这个特性自然也成为了 PHP 的基础。

### 这个社区很慷慨

![](img/c85367f0418ff40820c607d0c25d9ee1.png)

我们在上一期时事通讯中提到了这一点，但如果你错过了，它帮助乔·沃特金斯摆脱了真正的困境。Joe 从事 PHP 内部工作，并且在很大程度上负责编写 PHP 代码时每天使用的函数。他是 PHPDBG 的领头人，他开发了 pthreads，维护了 APCU 等等——如果没有他，PHP 看起来会完全不同。他在住房问题上遇到了一点坏运气，向社区寻求帮助——得到了慷慨的回应。他在不到 24 小时的时间里筹集到了所需的金额，人们仍在继续捐款。如果你觉得很慷慨，想感谢他作为一名 PHP 开发人员使用了他的劳动成果，为什么不凑钱呢？

### PHPLeague Google 群组/邮件列表

![](img/c78588f6c255ed54ae8e03a1eaa0d8f8.png)

[联盟](http://thephpleague.com/)现在有了一个[谷歌小组/邮件列表](https://groups.google.com/forum/#!forum/thephpleague)，所以你可以加入它来获得关于解决常见 PHP 问题的高质量方法的定期更新。当然，他们会把注意力集中在联盟套装上，但是这些套装是每个人都应该使用的，所以继续订阅来获得你的每日更新。

### PHP 常见错误

TopTal 发表了一篇关于 PHP 常见错误的相当不错的文章，我们又跟进了[七篇](https://www.sitepoint.com/7-mistakes-commonly-made-php-developers/)。这两篇文章一起讨论了悬空数组引用、误解按引用传递与按值传递、UTF8 问题和内存泄漏、忽略编码标准、使用过时的扩展、抑制错误、应用程序后端代码过于透明等等。读一读这两篇文章，看看你经常犯的这些常见错误。

## 结论

这个月到此为止。八月应该会平静得多，但是九月将会因为世界各地的 PHP 会议而变得疯狂，你可以肯定我们将会报道其中相当多的会议。你认为我们错过了什么重要新闻吗？请在下面的评论中告诉我们！

## 分享这篇文章