# 独家专访 JSNES 创始人本·菲尔斯曼

> 原文：<https://www.sitepoint.com/ben-firshman-jsnes-interview/>

Ben Firshman 是 JSNES 的幕后天才，这是一款用纯 JavaScript 编写的可玩的全功能任天堂娱乐系统模拟器。克雷格为 SitePoint 独家采访了本。

克雷格·巴克勒:你好，本。告诉我们一些关于你自己和你所做的事情。

*Ben Firshman:* 目前，我在华威的[大学学习计算机科学](http://www.warwick.ac.uk/)，但是就工作而言，我主要是一名 Django/Python Web 开发人员。去年，我为谷歌的代码之夏做 Django，今年夏天，我一直在为伦敦的全球电台做 Django 开发。

***CB:* 华威是一所很棒的大学！** *(猜猜克雷格去了哪里？！)* **你开发网页，尤其是 JavaScript 有多久了？**

我第一次接触 Web 开发是在很小的时候，大概 8、9 岁的时候，在我的 ISP 提供的主机上尝试 MS Frontpage。我是一个好奇的孩子，需要了解事物是如何工作的，所以深入研究 WYSIWYG 编辑器的安全性和源代码是一个自然的过程。在简单的基础 BASIC 和 C 语言之后，我回忆起在我十几岁的时候开始用 PHP 进行后端开发。

就 JavaScript 开发而言，这是我在从事其他工作的同时发展起来的一项技能。唯一的例外是我在 Ajax 刚刚起步时编写的一个小型聊天应用程序。当时令人兴奋的是，它不需要刷新 iframe 来更新；我希望我能找到代码并确定它的日期！除此之外，我只在我的前端添加了几勺 JavaScript 糖。JSNES 对我来说是一次巨大的学习经历。

***CB:* 你开发 JSNES 的灵感是从哪里来的？**

我无意中发现了[马特·威斯克的作品](http://matt.west.co.tt/spectrum/jsspeccy/)，它激发了我的想象力。我有被过于雄心勃勃的项目困住的倾向，JavaScript 模拟器就是其中之一。

我在童年时玩过一段时间的 NES，基于对其速度与频谱关系的一些严格分析(他们或多或少是同龄人)，我认为这将是平台的最佳选择。事实上，有大量的模拟器可以从中获得灵感。

***CB:* 你使用了什么特别的开发工具或环境吗？**

没什么特别的:我信赖的 TextMate 和 Firefox 3。Firebug 被证明是调试和分析的无价工具；当时没有任何东西能接近它的力量。新版本的 Safari 有一些闪亮的新开发工具，但它们没有达到 Firebug 在功能方面设定的标准。

***CB:* 开发了多长时间？**

去年我花了几个月思考这个想法，并浏览了各种 NES 模拟器的源代码。一旦我找到一个合适的端口( [vNES](http://www.virtualnes.com/) )，我就在一两天内打下了一个工作模拟器的基础。毕竟，早期阶段只是一个巨大的选择性复制和粘贴练习。从那时起，它花了 9 个月的时间修复和调整错误，最终在微小的修复使它能够在 Chrome 上全速运行。

***CB:* 这是一个雄心勃勃的项目——有什么困难的技术障碍或浏览器不兼容需要克服吗？**

*BF:* 我没有撞上什么大的跨栏。最初的版本只适用于 Firefox 3.5，但 Safari 4 和 Chrome 的新版本解决了兼容性问题。尽管这些浏览器的早期版本支持 canvas 元素，但它们不允许使用 imageData 对象直接操作像素。

Java 和 JavaScript 之间只有一些引起问题的细微差别，比如两个整数的除法在 JavaScript 中返回一个浮点数。

***CB:* JSNES 在谷歌 Chrome 上的运行速度几乎快了 10 倍，Mozilla 和 WebKit 团队都在调查这个问题。你认为 Chrome 为什么会快这么多？**

*BF:* 很有意思的是，三个独立的问题原本在 Chrome、Safari 和 Firefox 上造成了糟糕的性能。

1.  Firefox 有一个复杂的问题需要解决。Firefox 的 TraceMonkey 引擎，顾名思义，使用一种称为跟踪的技术，通过试图找出可能的执行路径来编译代码块。仿真器在可跟踪的块(如循环)中进行大量分支，并创建大量可能的路径。因此，跟踪是不切实际的，所以它的运行没有任何优化的好处。如果你对这个问题感兴趣，有几个错误值得关注(Bugzilla [509986](https://bugzilla.mozilla.org/show_bug.cgi?id=509986) 和 [516264](https://bugzilla.mozilla.org/show_bug.cgi?id=516264) )。
2.  Chrome 的问题没那么严重。出于某种原因，访问 imageData 对象来直接操作像素是非常低效的。简单地最小化访问次数导致了性能的飞跃。
3.  Safari 的性能问题现已修复，但最初的原因是一个完全不同的问题。模拟器中的一些类有大量的属性(例如 CPU 和 PPU)，这导致表示它的字典结构没有被缓存。访问那些常用类的属性是低效的，但是最近在 WebKit ( [changeset 48573](http://trac.webkit.org/changeset/48573) )中已经解决了这个问题。

要回答这个问题，Chrome 的 JavaScript 引擎(在某种程度上也是 Safari 的)在运行这类代码时效率要高得多。但它最初的运行速度与 Firefox 和 Safari 相同，因为它有一个较慢的 canvas 元素。

***CB:* JSNES 引起了很多人的关注。这对你来说是积极的还是消极的经历？谷歌感谢你提升 Chrome 的知名度了吗？！**

成千上万双眼睛关注我的作品是一种不寻常的体验，而且反响非常积极。特别是，许多人已经提交了用于提高性能的补丁。我非常感谢他们所有人！

谢天谢地，我还没有收到任何撤下通知。令人欣慰的是，过时系统的模拟器正被大公司广泛接受。

有趣的是，我没有收到谷歌的消息。但是 Mozilla 和 WebKit 团队一直在尝试解决性能问题。我认为一个尖端的网络应用程序在他们的浏览器上运行的很差，而其他的却不行，这对他们来说是个坏消息。

***CB:* 你对 JSNES 有进一步的计划吗？**

我的下一个大目标是获得良好的工作。这是一个相当大的挑战，因为 JavaScript 没有直接的声音支持，但我正在取得进展。如果它成功了，我希望它能打开更多有趣的网络音频应用的大门。

在开发的早期，我也尝试过用鼠标支持猎鸭，但是我从来没有成功过。我很想修复它，尽管它让游戏变得毫无挑战性！

随着漏洞得到修复，还有许多性能改进和更多游戏正在进行中。

CB: 你还在做其他令人兴奋的项目吗？也许是 JSPS2？！

我还没有什么具体的想法，但是我只能说 [WebGL](http://en.wikipedia.org/wiki/WebGL) 和 [Web workers](http://www.whatwg.org/specs/web-workers/current-work/) 将会把 JavaScript 应用带到一个全新的水平。

***CB:* 非常感谢你的时间和翔实的答案，本。我们都会饶有兴趣地关注 JSNES 和你的下一个项目。**

如果你想跟踪本的进步，他的个人主页是[http://benfirshman.com/](http://benfirshman.com/)或者你可以在[http://twitter.com/bfirsh](http://twitter.com/bfirsh)关注他。

另请参见:

*   [js page](http://benfirshman.com/projects/jsnes/)
*   JSNES:一个用 JavaScript 写的 NES 模拟器

## 分享这篇文章