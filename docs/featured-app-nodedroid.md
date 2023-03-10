# 特色应用:NodeDroid

> 原文：<https://www.sitepoint.com/featured-app-nodedroid/>

BuildMobile 采访了 Bruce Cooper，并在后台参观了一个名为 NodeDroid 的 Android 应用程序的开发过程。在这次旅行中，我们将介绍收入模式，比较 Android 和 iOS 的发展现状，并深入探讨一些具体的代码挑战。我们还收到了第一个关于发展音轨的提示。

**BuildMobile** :你好布鲁斯。因此，为了读者的利益，你能简要地告诉我们你是谁和你做什么。

布鲁斯·库珀:我是一个相当书呆子的人。我一直热衷于编程，学习如何做事情。我是一名 IT 顾问，已经在这个行业工作了超过 15 年，但这并不允许我玩我喜欢的所有技术(大多数公司都非常保守)，所以我也做兼职项目。

为手机编程是我的副业之一，尽管我也为我的工作做过一些手机开发。我做过 iOS、移动网络(HTML5、CSS 等。)和安卓。目前，安卓是我的最爱，但那是因为我有一部安卓手机。

**BuildMobile** :那么，NodeDroid 是什么？

Bruce Cooper : NodeDroid 是我的 Android 学习应用。我刚买了一部闪亮的新手机，想知道它是如何运转的。我已经算出了 iOS，想比较一下两者。激励自己完成某件事情的最好方式是让它成为我自己会使用的东西，所以我看了看我的应用程序选择中有哪些差距。当时，没有任何好的 Android 使用计量器，所以这就是我选定的。这让我能够确保我没有为我的电话提供商(Optus)或我的家庭互联网连接查看我的数据或电话使用情况。

我还想提供一些其他人可以使用的东西，所以我确保我付出了足够的努力，使它能够在 Android 市场上正常发行。

**BuildMobile** :如果有一个应用程序，你如何辨别它是否有存在的必要，如果有，你应该对它的收入模式有什么想法？

布鲁斯·库珀(Bruce Cooper):这可能是任何人在进行应用程序开发时最难回答的问题。有很多人说“哇，应用商店是个金矿。我要去弄这个”。现实有点难。知道什么可行，什么不可行，就像做其他生意一样困难。也许更是如此，因为竞争太激烈了。

只要看看应用市场上有多少重复就知道了。我有一个朋友，他时不时给我发一些应用程序的想法。几乎在每种情况下，有人已经有了这样的东西，我只是把链接发给他。我认为成功不仅仅是拥有一个能做一些事情的应用程序。为了成功，它必须做好一些事情。

尤其是如果你想让人们为此买单的话。在这种情况下，我并没有试图从 NodeDroid 中赚钱。我只是想找点事做做。说到这里，我并不想发布一个半生不熟的东西。毕竟上面有我的名字。

收入很难。目前的想法是“免费增值”模式似乎效果最好。这个想法是你提供基本的功能，然后收取额外的费用。这也适用于广告导向的模式。

我在我的申请中加入了广告，不是因为我认为它会赚很多钱，而是因为我想看看它是如何运作的。我最终赚了一些钱，但肯定不够支付账单。

最后，我决定开源我的应用程序。我这样做是因为我希望人们帮助我使它更成功，而不是从中赚钱。

我听说的一个成功的收入模式是“量”的方法。我认为有一些开发公司正在开发一个又一个应用程序，每个程序只收取 0.99 美元。每一个都非常非常简单，所以他们可以很快扭转局面。每个应用程序并不能带来足够的收入，但放在一起你可以谋生。

**BuildMobile** :跟上 Android 的速度有多难？开发者文档的状态如何，它们是你的主要资源吗？

布鲁斯·库珀:对我来说，为 Android 编程非常容易。我是一名 Java 开发人员，绝大多数标准 Java SDK 都是为 Android 开发的。您也可以使用大多数第三方库。最后，我需要学习的只是应用程序框架(顺便说一句，这是经过深思熟虑的)，而且我已经跟上了进度。

对于 iOS 来说更难。我必须学习一门新的语言，而且我还必须自己管理内存。iOS 的 GUI 生成器比 Android 的好得多，但除此之外，我发现 Android 的开发体验更好。

有很多关于 iOS 的文档和文章，可能太多了，也太肤浅了。我常常要花一些时间才能找到我所需要的技术信息。Android 文档要稀疏得多。它只是有你需要完成工作的信息。

我怀疑一个初学编程的人会比 Android 更容易使用 iOS 文档，但对我来说，我更喜欢反过来。我想了解我在做什么，而且很多时候 iOS 文档似乎在盲目鼓吹解决方案，而不是对平台的理解。这变成了一个神奇的咒语，施咒者并不明白它是如何发生的。Android 在解释事物如何工作方面似乎要透明得多。

一个不太有经验的开发人员可能更喜欢按“那种方式”去做，但是如果你想做一些更复杂或者稍微有点不寻常的事情，那么你就需要这方面的知识。我还应该指出，世界上的 Java 开发人员比 Objective-C 开发人员多得多。

**BuildMobile** :你希望平台增加什么特性？

布鲁斯·库珀(Bruce Cooper):用 Ruby 写一个移动应用不是很好吗？举个例子，NodeDroid 的屏幕抓取很痛苦。正则表达式遍地都是，Java 没有作为原生第一类对象的正则表达式。Ruby 有，而且它更擅长文本操作。

我用 Ruby 重写了 Optus 解析器的屏幕抓取组件，可读性更好，并且只有原来的三分之一大小(代码行)。如果能把它包含在 NodeDroid 应用程序中，那就太好了。

有一个名为 [Ruboto](http://ruboto.org/) 的项目旨在让你做到这一点。目前它有一些不足，但这是值得关注的。苹果禁止使用脚本语言和第三方框架。

我认为 Android SDK 设计得非常好，它允许开发者比同等的 iOS 开发者做更多的事情。特别是，视图层次结构和使用视图让应用程序进行协作的方式近乎完美。

**BuildMobile** :构建 NodeDroid 最具挑战性的特性是什么？

布鲁斯·库珀:最难开发的部分是 Optus 网站的高效屏幕抓取。即使在最好的情况下，屏幕抓取也很复杂，但是使用传统的方法(解析 DOM，然后使用 XPath)被证明在 CPU 和内存使用方面成本太高。最后我用了很多正则表达式才把它弄起来，有用但是脆弱多了。

UI 的某些方面也被证明是困难的。当我开始的时候，我认为实现页面屏幕滑动是微不足道的。事实并非如此，也没有任何例子说明如何去做。我最终查看了 ScrollPanel 的源代码，以找出如何做到这一点。这就是开源框架的一个优势！我不应该去研究源代码，但是当我需要的时候它就在那里。

**BuildMobile** :你们是怎么解决的，事后有没有找到相关文档？

Bruce Cooper :标准的滚动面板没有内置“抓取页面”功能。所以我写了一个子类来监听触摸事件，当它检测到一个“touch up”事件时，计算出它应该在哪个页面上，然后发出第二个滚动命令，转到对应于该页面的正确位置。

最后，这是一个相当简单的解决方案，只需要 100 行代码。虽然花了一段时间才想出如何去做。不，文档中没有关于它的任何内容，因为“抓取页面”不是一个标准功能。这是我必须加进去的东西。

显然，这是一个非常标准的功能，每个应用程序都必须自己实现它。也许谷歌会在未来的版本中添加它。

**BuildMobile** :在移动开发中，谁或什么给了你灵感？

布鲁斯·库珀(Bruce Cooper):我爱我的手机。我喜欢写我用的东西。我有动力改进它，因为我正在[试用](http://en.wikipedia.org/wiki/Eating_your_own_dog_food)(我自己也在用)。如果我这样做是为了赚钱，我会采取不同的方法。

大多数应用程序开发者都是在为自己写作。这是一个混乱的市场。我们(开发者)需要明白，我们并不是市场的大多数。正常人想要简单、简洁的东西，给他们提供与他们相关的信息。这可能与书呆子想要的完全不同。这当然适用于所有的发展，不仅仅是手机，但手机比其他应用程序拥有更广泛的用户基础。

BuildMobile :你的开发环境是怎样的？

**Bruce Cooper** :我有一台 13 英寸的 Macbook pro，我把它装得满满的，尽可能多的内存，我把它插入一个坞站(没错，你可以为你的 Mac 电脑配备一个[坞站](http://www.hengedocks.com/))和一个 24 英寸的戴尔 LCD。

我使用 Eclipse 进行开发，如果我想走老路，偶尔会使用 Vim。源代码控制是通过 Git 来完成的，也就这些了。Android 的一个好处是你可以在任何你想要的平台上开发:Windows，Linux 或者 OS X。

**BuildMobile** :你给 Android 编码的配乐是什么？

布鲁斯·库珀:好问题！我喜欢独立流行音乐。不过我需要小心，因为我倾向于手拿一杯红酒在房间里跳舞，而不是在太好的情况下编码:)

今天听了雪人最新也是最后一张专辑《缺席》。还有胫骨。

布鲁斯，非常感谢你抽出时间。我毫不怀疑你的见解对读者来说是无价的。谢谢分享。最后有什么想法吗？

Bruce Cooper :我写 NodeDroid 是作为一个实验和学习练习。我对事情的进展非常满意，我希望我同时也为人们做了一些有用的事情。目前，它只对 Optus 和 Internode 用户有用，但如果人们愿意与我合作，我很乐意添加其他提供商。有兴趣的话可以在 Twitter 上联系@ brucejcooper。

**App**:[node droid-安卓市场](https://market.android.com/details?id=com.eightbitcloud.internode)
站点:[node droid-8bitcloud.com](http://8bitcloud.com/nodedroid.html)
**代码**:[node droid-GitHub](https://github.com/brucejcooper/NodeDroid)

## 分享这篇文章