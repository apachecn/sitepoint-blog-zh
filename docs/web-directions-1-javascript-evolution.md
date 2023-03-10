# Web 方向反思，第 1 部分:JavaScript 进化和澳大利亚入侵

> 原文：<https://www.sitepoint.com/web-directions-1-javascript-evolution/>

上个月，我有幸在略显阴郁的伦敦参加了 [Web Directions @media](http://atmedia.webdirections.org/) 。该活动吸引了网络世界的所有大佬，两天都为开发人员和设计人员安排了精彩的会议。

我从哪里开始？不可能面面俱到，但我希望这一系列的帖子能让你对这次活动有所了解，并包含一些有趣的信息片段。

## 澳大利亚人入侵了

我打赌墨尔本是空的:大多数人都在英国的酒吧工作或者开网络会议！但是祝贺玛克辛·谢林和约翰·奥尔索普组织了网络方向@媒体。一切都很顺利……除了 200 人同时发微博时常见的 wi-fi 故障。

## Brendan Eich:成熟的 JavaScript

![Brendan Eich](img/eb4ad0016242399649c7bdb7f4d7b69b.png)
**以为你懂 JavaScript？见见布兰登·艾希。**

Brendan 是 Mozilla 公司的首席技术官和 JavaScript 的发明者，JavaScript 是世界上使用最广泛的编程语言，也是许多网络职业的基础。布兰登在他一小时的主题演讲中加入了很多内容。可能太多了——就在你觉得“哇，太酷了”的时候，他开始了下一步。

JavaScript 诞生于 1995 年 5 月，原名为*摩卡*。它被设计得很简单，或者肯定比每次您需要翻转效果时编写和编译 Java 小程序更简单。对于 Brendan 来说，最好的部分是一流的函数、闭包和原型继承。他为`eval`、全局对象和`with`语句道歉！

Brendan 透露了 ECMAScript 第五版和 Harmony 的几个特性，预计将于 2013 年推出。最显著的特征…嗯，那些我能潦草写下来的…

### 让—一个更好的“var”

新的 let 声明定义了块范围的变量。例如:

```
 let x = 99;

if (true) {
	let x = 1;
	alert(x); // output 1
}

alert(x); // output 99 
```

强大的东西，虽然它肯定会导致一些调试噩梦！

### 增强功能参数

JavaScript 将支持 PHP 和 C# 4.0 的默认参数，例如

```
 function add(x = 1, y = 2) {
	return x + y;
}

alert( add() ); // output 3 
```

还可以使用稍微奇怪的'…args '语法来定义可选参数。该函数将访问一个名为 args[]的真实数组，而不是普通的 JavaScript arguments 对象，它看起来像一个数组，但不是！

### 较短的函数

你厌倦了输入“函数”吗？应该缩短吗？布兰登认为是这样的，并提出了几个选择。这值得它自己的帖子和明天的 SitePoint 投票…

### 速度测试和 IE9

布兰登透露，arewefastyet.com 将提供 JavaScript 速度测试，使用真实世界的活动场景，而不是标准的基准。它主要用于评估 Firefox 的 JavaScript 引擎的速度，目前显示*“否”*。

最后，他让我们恳求微软为 Windows XP 生产一个版本的 IE9。如果你现在认为 IE6 不好，想象一下在 2015 年必须支持 IE8 会是什么样子。

## 汤姆·休斯-克劳奇:服务器端 JavaScript 简介

![Brendan Eich](img/37e2cf93c231d87985d664b712029641.png)
**是的，汤姆在现实生活中就是这样迷离的！**

汤姆以骑独角鲸的独角兽为主题，做了一个有趣而有见地的演讲。他对 SSJS 的伟大非常有信心，在整个演讲中他都用了漫画！尽管我的眼睛在流血，我还是设法写了一些笔记…

JavaScript 是网络的通用语言。不幸的是，我们中的许多人不得不在 JavaScript 和 PHP、C#或 Java 等服务器端语言之间分配时间。汤姆确信事情不需要那样；如果我们沉浸在一门语言中，我们都能成为更好的开发者。

服务器端 JavaScript 的优势包括:

*   没有句法上的分裂
*   渐进式增强是“免费的”——相同的表单验证代码将在服务器和客户端上工作
*   JavaScript 正变得越来越受欢迎:它被视为一流的工作，Rhino、SpiderMonkey 和 V8 等运行时软件正在迅速发展。

Tom 对 Node.js 的称赞最多:超快的网络服务器，在 V8 JavaScript 引擎上运行事件驱动的 I/O 框架。他演示了将 YUI3 日历组件转换成在服务器端运行是多么容易。令人印象深刻。

汤姆的 [SSJS 幻灯片可以在网上获得](http://www.slideshare.net/sh1mmer/server-side-javascript-you-aint-seen-nothing-yet)，你也可以[阅读他的 SitePoint 访谈](https://www.sitepoint.com/web-directions-media-tom-hughes-croucher-interview/)。

在接下来的网络方向反思文章中:John Resig 和 Jonathan Stark 关于移动网络开发…

## 分享这篇文章