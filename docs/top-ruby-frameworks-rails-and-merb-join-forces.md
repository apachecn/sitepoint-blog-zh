# 顶级 Ruby 框架 Rails 和 Merb 联手

> 原文：<https://www.sitepoint.com/top-ruby-frameworks-rails-and-merb-join-forces/>

向任何在 LAMP stack 上开发，或者使用 ASP、CFM、Python 或除了 Ruby 之外的任何东西的读者道歉——这篇文章可能不会引起你的兴趣。但是两个本质上相互竞争的框架合并并联合起来是相当罕见的。然而，这就是今天发生的事情，当流行的 [Ruby on Rails](http://www.rubyonrails.org) 框架宣布它将与不太流行，但正在崛起的 [Merb](http://merbivore.com/) 框架联手。

在过去的四年中，被大肆宣传的 Rails 框架在聚光灯下享受了大量的时间，并为一些最常用的网站和应用程序提供了支持，包括 Twitter、、Kongregate、A List Apart、Scribd 和 Xing。但是 Rails 并不适合所有人。许多人喜欢它做事的方式，但不喜欢它试图为每件事找到答案的方式。Rails 是一个全栈框架，它宁愿你使用内置的部分来完成事情——例如，AJAX 的原型，ORM 的活动记录，或者测试的测试/单元。

另一方面，Merb 是一个受 Rails 启发的框架，致力于关注核心功能，并依赖插件完成其他所有事情。这使得 Merb 在使用什么技术来构建应用程序时更加不可知。Rails 开发人员总是能够使用其他框架进行开发——例如，用于 JavaScript 的 jQuery 或用于测试的 rSpec 但这通常被认为有点麻烦。你不能轻易地剥离掉你不喜欢的部分，所以你必须把你想用的东西堆在上面，并依靠插件来使它们都工作。

现在，Rails 和 Merb 的核心团队正在合并，努力将两个框架整合到 Rails 3 中，Rails 3 将于 5 月在拉斯维加斯的 RailsConf 上发布测试版。Rails 创始人 David Heinemeier Hansson 在一篇关于今天合并的博客文章中写道:“我们都意识到，为了共同的利益而合作要比在围栏的每一边复制东西更有成效。”。“Merb 和 Rails 在设计和感性方面已经有了如此多的共同点，联手似乎是显而易见的出路。我们所需要的只是坐下来聊聊天，解决问题，所以我们就这么做了。”

Rails 和 Merb 团队都确保这对于不希望改变的开发人员来说并不意味着太大的改变。Merb 团队计划努力为 Merb 用户创建一个升级到 Rails 3 的简单途径，当前的 Rails 用户当然可以忽略这些变化。不过，合并的最大影响可能是 Rails 将变得更加模块化。默认选项仍然存在，但是在不损害框架其他部分的情况下替换它们会容易得多。

Rails 3 将拥有“选择加入或退出特定组件的能力”。Merb 核心团队成员 Yehuda Katz 表示:[我们将致力于减少铁轨间的耦合，并使更换部分铁轨时不干扰其他部分成为可能。](http://yehudakatz.com/2008/12/23/rails-and-merb-merge/)

Rails 也应该变得更快——Merb 超过 Rails 的速度一直是年轻框架的支持者们吹嘘的东西。Rails 3 还将为扩展提供一个新的严格的 API。“这将允许用户和插件开发者有一个更清晰、更稳定的 API 来构建。Katz 说:“这也将显著减少不同版本之间的插件破损。

对于 Rails 和 Merb 框架的开发者以及 ruby 爱好者来说，这是一个重要的日子。但对于整个 web 开发社区来说，这也是一件相当重要的事情。这两个流行框架的结合可以给 Ruby 和 Ruby on Rails 一个巨大的推动。有趣的是，两个相互竞争的框架走到了一起，消除了它们之间的细微差异，朝着一个更积极的目标努力。

*记得查看 [SitePoint 的 Ruby 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=227),获取任何 Ruby 相关的帮助和讨论。任何对学习 Rails 感兴趣的人都被鼓励去买一份 SitePoint 的 [Simple Rails 2](https://www.sitepoint.com/books/rails2/) 。*

## 分享这篇文章