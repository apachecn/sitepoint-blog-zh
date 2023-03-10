# duo lingo for PHP——像 Mimo 这样的应用程序能教多少 PHP？

> 原文：<https://www.sitepoint.com/duolingo-php-much-php-can-apps-like-mimo-teach/>

[Mimo](https://getmimo.com/) 是一款号称“在旅途中”教授编码的应用。作为 Duolingo 的长期用户，我可以理解这种方法——特别是当我越来越有兴趣在未来几周内实现某种程度的基本 Swift 读写能力时。

为了客观地评估这款应用教授人们新事物的能力，我决定看看它提供了什么，看看我是否可以向刚开始学习一门新语言的人推荐一些东西。

![Mimo homepage screenshot at getmimo.com](img/fe414538c036b0bfabcb8080232a6756.png)

## 模型

该应用程序在每门课程的前一两节课是免费的，在这一点上，你要么在社交媒体上传播消息，要么付费解锁下一节课。然而，在那之后的下一个必须付费。

![The pay to unlock screen in Mimo](img/d4e7d92765b7d24aa393db44d0fcb4db.png)

像 Duolingo 一样，Mimo 使用条纹来游戏化学习，记录你连续使用它的天数。为了进一步游戏化，该应用程序提供了一系列徽章和成就供收集，尽管真正的杀手级功能是类似 Duolingo 的“关注用户”方法，该方法还允许在某些课程幻灯片上进行直接交流。

![Achievements in Mimo](img/ddef26798cc9155b5fa15caa855ec87e.png)

显然，在 Mimo 中有相当多的收集和实现工作要做！

该应用程序允许用户设置自己想要的[学习速度](https://www.sitepoint.com/how-to-learn-quickly/)，但这一切只是规定了应用程序将提醒你使用它的频率——在课程长度或内容方面，它对应用程序没有其他影响。

![Reminder frequency menu](img/c109faab9a8ec1a579b2037d6e580296.png)

这是一个悲哀的世界，每天学习 20 分钟被认为是疯狂的，不是吗？

在撰写本文时，高级接入费用高达每年 54.99 英镑，或每月 4.58 英镑，但只能按年支付。这款应用的开发者似乎很清楚这样一个事实，即大多数人在一个月后就会放弃手机上的学习和自我提升应用。

![The Upgrade to Premium screen in Mimo](img/dcc6c91ecf2ee501d87baf4407ba7887.png)

最大的问题是，对于免费提供的东西，很难证明花这么多钱是合理的。

## 课程

该应用程序提供个人语言课程 PHP，HTML，Swift，“黑客”等。还包括按顺序排列的几门课程，目的是教授更大的整体。例如,“制作网站”课程包含以下子课程:编程、编程 2、HTML、CSS、JavaScript 等。

![The course track selection screen](img/dd236e201bf1ea3c6c90dc9acdbfb79f.png)

![Page 1 of the Make a Website track](img/5b6d739217bda226d8be1c32fd51c47f.png)

![Page 2 of the Make a Website track](img/5a7814c0989632b7d1fc8f10d8d5edd0.png)

然而，这门课程并没有激起人们的兴趣——它所涵盖的是一个非常基本的、与语言无关的变量概念，然后付费内容就开始了。43 节课中有 2 节是免费的——几乎没有足够的内容让你做出明智的购买决定。此外，尽管看起来内容很多(43 节课)，但应该注意的是，每节课通常是 10-15 分钟。

个别课程深入到相同的东西(至少是免费的内容)，但根据所学的语言进行解释。所以当开始 PHP 课程时，你学习 PHP 变量…

![PHP variables introduction](img/102be7e157086c44012fd116a2ea0bc1.png)

当你做 Swift 课程的时候，你也做同样的事情。

![Swift variables introduction](img/b6961a84313f39fbeb596f9ef6a16637.png)

同样，在这一点上几乎没有理由购买溢价。然而，这种方法看起来确实合乎逻辑——如果它真的旨在成为编程的双语言，那么用另一种你不熟悉的语言来呈现你已经熟悉的相同概念是有意义的。有什么比用一种新的语言解决你已经解决的同样的问题更好的学习方法呢？只是这些并不是正在解决的实际问题。

我在这里看到的最大问题是*环境设置*(可以预见，鉴于我非常关心这个主题，我[写了一本书](http://bit.ly/phpenv-sp))。该应用程序立即进入变量和其他编程概念，但是一个刚开始使用任何一种语言的人完全不知道如何真正开始*。没有关于如何安装运行时、编译器或 IDE 的说明，也没有可能需要什么操作系统或环境的指示。我可以很容易地想象一个用户经历了所有这些课程，然后坐在电脑后面完全卡住了。这类似于有人通过在移动设备上玩 RPG 来学习击剑——你的虚拟角色将挥动剑，但一旦你完成游戏，你就不再有能力使用它了。*

## 课程质量

除了“所谓的”一词的重复和课程中的一些错别字之外，内容写得很好，容易理解，通常对新手非常友好。

![The introduction screen of the PHP course](img/d80c34063cf750ededc5705e3e3d65e6.png)

![First screen of the PHP course](img/e40b8a3fd3eea26982a1da52bcce8323.png)

在应用程序的免费部分，有四种简单的教学方法:

*   猜测
*   扣除
*   用心学习
*   谜语

猜测将让用户猜测一个问题的答案，而无需事先准备。错误的猜测将不允许用户进一步前进，允许无限次的重试，而正确的猜测将在一定程度上解释解决方案。

![User is expected to pick a term they hadn't heard of before](img/9fef466f8dc07efc0576bd00d558a1b9.png)

![A new term out of nowhere is defined](img/73560fc1074f7267f930005bb8dd2efa.png)

**推论**通常会让用户准备好用他们在上一张幻灯片或之前几张幻灯片中给出的答案来回答问题。

![A deduction about implicit data types in Swift](img/6c8daf1d86cfa1811d8660db6c011455.png)

用心学习只会把定义、理论和解释扔给用户，希望他们能记住。

![PHP Theory](img/d1f93c2c0eb6a54aeecb36a328402f93.png)

![More PHP Theory](img/d3318d5866ffe46034b43ef8d78ae8aa.png)

![PHP being a server side language, slide explained](img/9403c891c79666a74716eb5fdc30db32.png)

![Guesswork slide, offering choices](img/8f43aca3837992beab979dd4afd7738e.png)

![Guesswork slide, wrong choice selected](img/1345e48ddd36ebeed5710fb7c6fe1353.png)

![Guesswork slide, right choice selected](img/ff63ec518f9193d3b6c27e9b04570439.png)

谜语需要对代码序列进行重新排序，直到它们符合逻辑，看起来像有效的代码。

![A re-ordering of PHP and HTML code](img/6f2a404734bfeec9de216e78b8df089b.png)

![A re-ordering of Swift code](img/cbdf17a84b9806b55a268da14ac2832e.png)

![Re-ordering PHP code](img/cbbae599f4057edd47339283c38f384f.png)

这些方法在理论上很好，但我仍然建议 Mimo 添加一些手动输入的代码来开发人们的肌肉记忆。我还建议在每节课的最后增加一个盒装的有限 REPL，人们可以在那里测试他们到目前为止学到了什么。

## 结论

虽然这个概念很有趣，但我不认为像 Mimo 这样的应用程序可以达到任何有意义的编程水平。为了学习如何编程，一个人需要开发打字的肌肉记忆。一个人必须发展寻找问题解决方案的能力(称为 StackOverflow-fu)和在编程语言手册中找到重要信息的能力。人们需要与相关语言的社区进行互动，并从需要手动调整以适用于手头问题的示例中学习。

Mimo 不具备这些重要因素——它实际上是一种“用心学习”的应用，为您提供的理论多于实际应用。就其本身而言，它可能有助于介绍您在实际项目中可能遇到的术语，但其实际价值非常值得怀疑。

诚然，我可能会错过一些高端的杀手级功能，但 55 GPB 一年，只需每年支付，感觉像是一个应用程序游客陷阱，特别是考虑到应用程序的免费部分明显缺乏质量。

作为一名教育工作者，我在向人们推荐第三方(非 SitePoint)内容提供商时从来没有遇到过问题——我的愿望是看到人们受到良好的培训，眼前的利润不是优先考虑的。这块馅饼对我们所有人来说都足够大，从长远来看，一个训练有素的开发人员社区对每个人都有好处——许多人开始申请成为我们的作者，然后被介绍到一些技能磨练资源，几年后回来，作为有能力的贡献者受到欢迎。

然而，Mimo 不是这样一种资源——但它可能是。随着升级，如对事物社交方面的强调，对支持它的语言(PHP，JS，Ruby，HTML)的简单 REPL，对课程免费部分的一些改进(打字，解释，措辞-实际上是一般的文本编辑和课程设计)，不同的定价模型和更实际的方法，Mimo 可能是许多人教育计划中的一个重要因素。我被堵在队列或交通中超过 10 分钟的次数很多，能够利用这些空闲时间以这样一种容易消耗的方式在旅途中学习一些有用的东西将是无价的。

我祝 Mimo 好运，并将在应用程序的未来迭代中检查它们。

## 分享这篇文章