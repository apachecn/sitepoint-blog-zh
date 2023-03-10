# 如何让你的用户享受等待

> 原文：<https://www.sitepoint.com/make-users-enjoy-waiting/>

等待通常是一种非常令人不快的活动:它是无益的、无聊的，甚至是紧张的。

因此，如果你要求用户等待，他们中的许多人会拒绝。根据 Forrester Research 的一项研究，几乎一半的消费者希望网站在两秒钟内加载完毕，如果超过三秒钟，40%的消费者会离开。

那么，你应该优先考虑页面加载时间吗？如果你这样做，你将不得不放弃丰富你的 UX 的许多功能，比如交互式图形、视频剪辑或高分辨率图像。

解决方法:让等待变得有趣。或者至少，让等待变得更有效率，不那么无聊，不那么有压力。这里有五个技巧可以帮助你做到这一点。

## 1.战略性地提供反馈

孩子们问“我们到了吗？”是有原因的在长途乘车过程中:当你不知道还要等多久的时候，等待会变得更加困难。

知道了这一点，您可能想在每次加载时向用户显示一个进度条。但是等一下。

根据“可用性之王”Jakob Nielsen 的说法，[有三个时间限制](https://www.nngroup.com/articles/response-times-3-important-limits/)需要注意。

*   如果在 **0.1** 秒内出现响应，用户会认为这是“即时的”，这意味着你不需要做任何事情，只需要向他们展示结果。

*   如果在 **1** 秒内出现响应，用户会注意到延迟。然而，特殊的反馈仍然是不必要的。

*   如果响应时间超过 10 秒，你就会失去用户的注意力。要么分散他们的注意力，要么告诉他们还需要多长时间，这样他们就可以开始另一项任务。

这些时间限制是众所周知的。但是，请注意，在 1 秒和 10 秒之间有一大段时间。如果一个响应需要 4 秒，你会怎么做？九点怎么样？

卡耐基梅隆大学的两名研究人员发现，当一项任务耗时不到 5 秒时，向用户显示进度条会让用户感觉任务更长。事实证明，进度条(虽然对于管理期望来说很棒)会引起对等待时间的注意。

考虑到这一点，我建议对预计 5 秒或更长的加载时间使用进度条。

## 2.更新越多越好

有趣的是，当在快速长线和慢速短线之间进行选择时，人们会选择长线。我们喜欢感觉自己在不断进步。(为了检验这个理论，问问你自己，你是愿意在走走停停的车流中行驶 30 英里，还是愿意在一条废弃的高速公路上行驶 100 英里。)

这就是为什么经常“暂停”的进度条或加载符号不如不断更新的符号更受欢迎。

比较来自[照片猴](http://www.picmonkey)和[照片猫](http://www.photocat.com)的两个进度条。

![Pic Monkey loading screen](img/2835ba834ae3d606aa43458f6d6147d3.png)

Pic Monkey 的加载栏大块更新，从大约 20%到 45%到 90%完成。

![Photo Cat's loading screen](img/ed9ee4514599608a38f00f18e7f17668.png)

虽然 Photo Cat 的图像编辑工具花了相同的时间来加载，但工具栏更新更频繁，显示至少 15%的稳定增长的百分比。这给人一种加载速度更快的错觉。

使用脉动进度条也可以达到同样的效果。当 Chris Harrison 和他的同事[研究五种不同进度条“类型”的效果时，](http://www.chrisharrison.net/index.php/Research/ProgressBars2)他们发现他们可以简单地通过使用一种频率逐渐增加的从浅蓝色到深蓝色的进度条，将加载时间的感知增加 11%。

## 3.使用提前完成

你听过这句话吗，“做得比完美更好”？嗯，绝对适用于装货。如果你把页面分成几个部分，而不是在页面完全加载后一次完成，你可以减少用户的被动等待时间(也就是说，他们坐在那里无所事事的时间)。)

谷歌在其电子邮件应用 Inbox 中使用了这一技术。页面的框架立即显示出来:一两秒钟后，你的消息出现；一两秒钟后，你的谷歌聊天记录就会显示出来。

![Google Inbox](img/8bca1561628bbe913a216e6604fe9944.png)

谷歌收件箱:完成比完美更好

用户几乎没有注意到丢失了什么，因为他或她太忙于查看那里的*是什么*。

这种策略也适用于较小的元素。

当您将媒体上传到脸书时，加载过程由灰色的图片框(也称为填充内容)表示。一旦该过程完成，该帧将被实际文件替换。

![Facebook uploading process](img/462592ca23e97fb6cc7b969010103d8f.png)

这种技术有效地减少了一半的等待时间，因为你不是在等待整个文件完成，而是在等待帧。一旦你看到这一点，等待是如此短暂，它不会注册。

## 4.让它变得有趣

这就是你有机会变通规则的地方。你能使你的加载页面或符号越有趣、越吸引人，它就越令人分心。

我最喜欢的一个例子来自于[非常充实的](http://www.prettyloaded.com)。您会看到一个相当标准的百分比栏，但每个百分比都有描述:

 ![enter image description here](img/e6d4b1ffccbdcc12a9743b49b8f8aded.png)
就在你达到 100%之前，屏幕闪烁“TTYL”

[Lordz 舞蹈学院](http://lordz.ch/)的加载屏幕几乎全是黄色。你的眼睛会立刻被中间那个跳舞的小剪影吸引住——并一直停留在那里，因为看着他移动令人着迷。

![Lordz animated GIF loading screen](img/a5beacd96d9b1a585d6095d63f7872b1.png)

事实上，插图太酷了，当主屏幕最终加载时，我几乎感到失望。

世界野生动物基金会的一个项目“奔跑 4 只老虎”也有一个催眠屏幕。

随着装载的进行，一张老虎的脸被画了出来。当你达到 100%的时候，完整的图画就出现了。

![Run 4 Tiger](img/c9c59eeebf32dc00dcec71b067a8cebe.png)

这些例子不仅漂亮，而且很有意义。当设计你的加载页面时，考虑用户心理和最佳实践是很重要的，但是如果你想让你的加载屏幕感觉独特和特别，重复你的品牌或产品。

## 5.整合经验

要真正让等待变得愉快，让你的加载屏幕“流动”到应用程序的其余部分。这意味着它应该感觉像是一种延伸的体验，而不是两种离散的、独立的体验。

看看波兰城市比亚韦斯托克的旅游网站。
加载屏幕包含一个加载百分比(技术#2)和一个骑着自行车的男人的插图(技术#4)。

![ Tourism website for Bialystok](img/d84d2aee2387c2c607813eea4a16aba9.png)

当真正的网站出现时，你会看到同一个人骑着自行车——现在在你的屏幕上更加悠闲地行进。这种视觉上的延续，每次都让我会心一笑。

![ Tourism website for Bialystok fully loaded](img/e2aac0c716306c80d90d830d7899fe0c.png)

又装了..

数字艺术杂志《幻觉》使用了同样的基本概念。

它有一个低对比度，静态加载屏幕…

![Illusion digital arts magazine, ](img/47da46f661be55b055d16abbe21bcc1f.png)

这转换到具有 GIF 英雄图像的高对比度加载屏幕。

![Illusion loaded](img/6f4b8d1910a7d768b7ba211ff84d32eb.png)

尽管效果不同，请注意标志的位置、大小和风格是如何保持一致的。这个设计选择让我想起了阴阳符号。

我们可以在国家地理的 [EAT: The Story of Food](http://www.natgeoeat.com/) 网站上看到整合技术的第三个应用。

如果你认为加载屏幕是一个独立的，它不会很好。一个不明物体在黑屏中间旋转。

![National Geographic's EAT: The Story of Food](img/829e7894931774e382216abe51db3d3d.png)

然而，当网站加载时，对象不会消失。它旋转到中心，显示出自己是一个创新的“菜单”(字面上和比喻上都是如此)。

![National Geographic's EAT: The Story of Food loaded](img/50263454361351fc718cebcf21306780.png)

每一部分代表了人类饮食的不同组成部分，如糖、谷物等。当你点击其中一个时，同样的装载符号会出现在缩图中。

![When you click on one, the same loading symbol shows up in miniature. ](img/c5aae1d376cb5b5d88af8cc13ff3d2dd.png)

要获得更多的加载灵感，请查看 [4 个在设计中使用动画的技巧](https://www.sitepoint.com/4-tips-for-using-animation-in-design/)和 [12 个创造性的、巧妙的加载屏幕](https://www.sitepoint.com/12-creative-clever-preloader-designs/)。

## 分享这篇文章