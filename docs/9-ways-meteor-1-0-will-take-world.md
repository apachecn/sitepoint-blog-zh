# 流星 1.0 将带你离开这个世界的 9 种方式

> 原文：<https://www.sitepoint.com/9-ways-meteor-1-0-will-take-world/>

2011 年 12 月，一个由才华横溢的软件工程师组成的小团队正式宣布了 Skybreak 的第一个预览版，这是一个纯 JavaScript web 开发框架和工具集，旨在使软件工程变得高效和易于使用。令人欣慰的是，大约一年后，这个平台变成了 Meteor，“一个用纯 JavaScript 构建网站的第二代微框架和应用服务器。”这是一个更好的名字，你不觉得吗？

那年夏天，Meteor 获得了 1120 万美元的资金，并向早期采用者保证 Meteor 开发小组(MDG)将长期参与其中。到 2013 年底，一些突出的全面生产应用赢得了广泛赞誉。难怪 Meteor 的 1.0 版本被如此热切地期待。但以防你还没有被所有的宣传说服，这里有一个流星 1.0 将带你离开这个世界的九种方式的列表！

## 1.新文档和教程，以及更新的示例应用程序

对于 1.0，Meteor 开发小组推出了一个全新的网站，其中包含下载、堆栈溢出问题、事件、专业服务等统计数据。但这些变化不仅仅是表面的(或促销的)。文档被完全修改，从完整的 API 中分离出一个简化的文档，包括描述性的部分和副标题。

![Meteor Website 1.0](img/af07804edaa3883d4f55e396ed1a9aa9.png)

对于新开发人员，Meteor 网站现在有一个关于安装 Meteor 和构建小型 todo 应用程序的简短教程。对于我们这些想了解更多的人来说，MDG 创建了一个部分，详细描述了框架的每个“子项目”,并提供了到存储库和相关文档的链接。开发者可以全面了解流星生态系统。

## 2.Atmosphere 是官方智能包目录

就在 1.0 之前，[大气](http://www.atmospherejs.com)成为流星官方智能包目录。软件包的安装使用开发者的 Meteor 用户名或组织，一个`:`和软件包本身的名称。这有助于区分同名的包，这样开发者就不必为同一个库的一个分支想出任何聪明的名字。在撰写本文时，Atmosphere 刚好缺少 3000 个智能包。现在很可能不止这些！

虽然搜索新的和改进的 Atmosphere 目录轻而易举，但那些喜欢命令行的人会很高兴地知道，您可以使用`meteor search`命令搜索智能包，并使用`meteor show`命令获得关于特定包的更多详细信息。

![Meteor Search](img/e044075413a6d710a4bc6e0d51a358a8.png)

## 3.实时 CSS 注入

如果你热切关注 1.0 之前的 Meteor buzz，你应该知道 Meteor 使用 Blaze 和 Tracker(以前的 Deps)来创建一个可从客户端和服务器访问的实时同步数据库。这太棒了，但是这个功能从一开始就是 Meteor 的一部分。

然而，我认为发布 0.9.0 版本时有一点需要重申。实时 CSS 注入:

> 在开发过程中，当您更改应用程序的 CSS 但不更改任何其他文件时，CSS 将在所有打开的浏览器选项卡中更新，而不会刷新页面。即使您使用的是 LESS 或 Sass 这样的 CSS 预处理程序，这也是可行的。

诚然，不到一秒钟等待服务器重启以检查应用程序的设计并不是一个巨大的不便，但至少现在你可以立即知道你的风格是否有效。这个看似很小的变化(在对 Meteor 进行重大改进的背景下)，确实大大减少了开发时间。

## 4.Velocity，官方测试框架

随着 Meteor 1.0 之前的采用速度加快，其粉丝经常抱怨明显缺乏官方测试框架。莱卡和 [RTD](http://rtd.xolv.io/) 不情愿地获得了一些关注，一些茉莉花、黄瓜、摩卡等的包装突然出现填补了这个空缺，但是由 [Xolv.io](http://xolv.io/open-source/) (他们之前在 RTD 工作)发布的[速度](https://github.com/meteor-velocity/velocity)给社区带来了极大的兴奋。

Velocity 为开发人员提供了混搭一些最受欢迎的测试框架的选项。其实茉莉，黄瓜，摩卡就是其中的三种！然而，测试的选项并不止于此！在您的项目目录中运行`meteor debug`命令，您将可以访问[节点检查器](https://github.com/node-inspector/node-inspector)，“一个使用 Blink 开发工具的 Node.js 应用程序的调试器接口”

## 5.创业公司雇佣流星开发者

正如我之前提到的，甚至在 1.0 之前，初创公司的创始人就把他们的希望寄托在 Meteor 上，希望它能作为一种构建易于构建和维护的 web 和移动应用程序的手段。然而，工作机会仍然很少。

随着 1.0 的推出，工作会定期发布在 [We Work Meteor](http://weworkmeteor.com) 上。

## 6.组织帐户

除了面向个人开发者的 Meteor 帐户之外，组织和公司还可以创建组帐户，允许每个人获得协作创建的智能包的联合信用。

## 7.isobuild–基于单一代码库的 Web 和移动应用

通过创建一个易于使用的实时全栈 JavaScript web 应用程序开发平台，Meteor 已经改变了游戏规则。让开发人员能够以闪电般的速度创建 web 应用程序无疑是一个不小的壮举。但是……移动呢？

这就是伊泽伯德的用武之地。Isobuild 是一个构建工具,“就像 Unix 世界中的 make、gcc 和 ld ”,它从一个单一的代码库创建多个程序，允许一个应用程序在 web、iOS 和 Android 上本地运行——或者捆绑到一个 tar 文件中。

当然，开发者将不得不做一些 T2 的工作。例如，如果您想要访问移动设备的摄像头，您首先要做的是将[摄像头智能包](https://atmospherejs.com/mdg/camera)添加到您的项目:`meteor add mdg:camera`。*唷。我知道这很难接受，但我希望你能理解我的意思！*

## 8.强烈的团体意识

为了庆祝 1.0 的发布，Meteor Development Group 与世界各地的 Meetup“队长”一起组织了一场可能是有史以来最大的开源活动之一:[全球流星日](http://meteorday.com/)！11 月 6 日，“全球同步举行了 134 场会议”，共有 4000 多名与会者。从那以后，这个社区一直在飞速发展；创建全面的、功能齐全的智能包，编写书籍和教程，教授课程，以及试验代码。

这就引出了我的下一个观点…

## 9.为了伟大的利益，学你一颗流星！

你知道像[多事之心](http://eventedmind.com)和[发现流星](http://discovermeteor.com)、[流星提示](http://meteortips.com)这样的常见嫌疑人，但还有一些新的孩子:

*   [用 MeteorJS 构建你的第一个实时网络应用](https://www.udemy.com/build-your-first-real-time-web-application-with-meteorjs/?dtcode=j0rdZRG1Ymzc)
*   [乔什·欧文的流星俱乐部](http://meteorjs.club/)
*   防弹流星
*   [流星厨师](http://themeteorchef.com/)

从各种各样的资源中学习，每种资源都有自己的风格，可以帮助您更深入地了解流星生态系统。

## 荣誉奖

尽管接下来的这两个要么没有达到 1.0，要么不打算达到 1.0，但它们在[路线图](https://trello.com/b/hjBDflxp/meteor-roadmap)上，绝对值得一提！

*   流星获得了深 d
*   [官方 Windows 支持](https://trello.com/c/ZMvnfMfI/11-official-windows-support)

## 分享这篇文章