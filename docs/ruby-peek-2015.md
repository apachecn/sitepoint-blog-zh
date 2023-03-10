# 2015 年的 Ruby 一瞥

> 原文：<https://www.sitepoint.com/ruby-peek-2015/>

![2015 new year](img/a71fbe9325952f0693932fc6ffb8b7f8.png)

随着 2014 年接近尾声，我想我应该快速浏览一下 2015 年在 Ruby 的土地上会有什么。很明显，2015 年如何开始将取决于 2014 年的最后一句话，所以我们将从今年的一些讨论开始，并尝试推断未来。此外，我将重点介绍 Ruby 和 Rails，而不是其他任何东西，主要是因为这是我所知道的。如果你对 Rails 之外的 Ruby 框架有什么预测，我相信读者会很乐意听到的。

## 红宝石

Ruby 在圣诞节当天发布了 2.2 版，结束了 2014 年。相对于 2.1 的主要改进是另一个垃圾收集特性。符号现在将被垃圾收集，这将立即降低所有 Ruby 应用程序的内存使用，尤其是 Rails(我们将在一分钟后讨论)。我找不到 MRI Ruby 的明确路线图，但我在 http://bugs.ruby-lang.org 的[找到了](http://bugs.ruby-lang.org)[这个链接](https://bugs.ruby-lang.org/versions/31)，这是 Ruby 2.3.0 的 bug/特性列表的开始。列出的唯一一项是对大小写转换函数仅限于 ASCII 字符的修复。我的谢洛克推理技巧让我相信 2.3.0 将更像是一个 bug 发布，让我对 MRI 的“下一个重大特性/改进”感到困惑。有什么想法吗？

### JRuby

另一方面，JRuby 正准备迎接(我希望是)一个巨大的 2015 年。JRuby 团队在 2014 年结束时发布了大量的版本(目前是 1.7.18)，JRuby 9000 也取得了巨大的进步。JRuby 9000 是 JRuby 的下一个主要版本，它旨在缩小 MRI 2.1(可能是 2.2…？)和 JRuby。但是等等，还有更多:

*   一种新的优化编译器
*   支持真正的本机 IO 和流程逻辑
*   最终使字符代码转换符合 MRI

我从 RubyConf 2014 的 Charles Nutter(JRuby 先生)和 Thomas Enebo 的视频中窃取了这些要点。

我每天都在使用 JRuby，我可以说它可能是 Ruby 的首要实现。我觉得很多 Ruby 爱好者害怕 Java 和 JRuby，其实不应该。我不是 Java-head，我做得很好。

### Rubinius ( **用户建议**)

我最初找不到很多关于 Rubinius 的信息，但是这篇文章的一位评论者(感谢 bennyklotz)发现了这篇关于 Rubinius 核心团队的文章。它还链接了许多关于 Rubinius 3.0 的文章。有几篇文章详细介绍了 Rubinius 3.0 的发展方向，所以如果你对 Rubinius 感兴趣(我们都应该感兴趣)，可以喝杯咖啡通读一下。

## 轨道

对于 Rails，2014 年随着 4.2 最终版的发布而结束。[这篇文章](http://weblog.rubyonrails.org/2014/12/19/Rails-4-2-final/)详细描述了这些变化，但这里有一个味道:

*   **active job**——我们都知道并且喜爱的后台作业抽象。凯尔·塞维斯[在我们 2014 年更受欢迎的 SitePoint Ruby 帖子中深入探讨了 ActiveJob](https://www.sitepoint.com/new-rails-shiny-activejob/) 。
*   **充足的记录**——每个人都喜欢的爱猫、双关语、星期五拥抱者亚伦·帕特森改进了 ActiveRecord。他[在这里解释这一切](http://tenderlovemaking.com/2014/02/19/adequaterecord-pro-like-activerecord.html)。
*   **Web 控制台**——根植于 [better_errors](https://github.com/charliesome/better_errors) gem 的理念，Web 控制台给你一个 Rails 异常页面上的 REPL。它增加了在 Rails 应用程序的任何页面上放置控制台的功能，所以这很棒。

从现在开始，Rails 核心团队专注于 Rails 5.0。在这一点上，我们所知道的是，5.0 将专门针对 Ruby 2.2(以及以后的版本)。这意味着 2.2 中的 GC 改进将在 Rails 5.0 中实现。Rails 可以是内存猪，所以我已经喜欢上 5.0 的发展方向了。

## 西纳特拉

Sinatra 仍然在运行 1.4 版本，但是 2.0 版本的传言已经出现。在[的这个回答](https://groups.google.com/d/msg/sinatrarb/gzwxA45n1KE/v2dZl43QtusJ)中，框架创建者康斯坦丁·哈斯列出了他希望在 2.0 中成为什么。这是一个相当大的名单，爬上总重写。如果你想接触一个被广泛使用的 Ruby gem 并为开源做贡献，这是一个很好的机会。

## 其他东西

*   Torquebox 即将发布 4.0 版本，这是一次重大更新。Torquebox 将被分解成许多宝石，并将像其他 Ruby web 服务器一样工作。Torquebox 是生产 Ruby 应用程序的一个很好的选择，4.0 应该会让它更容易使用。
*   一个新的 Ruby webserver 接口？Rack 在 2014 年宣布了它自己的生命周期结束，所以 2015 年可能会带来简单的维护和(希望)下一个 Ruby 中间件规范的诞生。上面链接的 Google Group 帖子谈到了为什么核心团队觉得 Rack 2.0 永远不会存在(或者至少不会作为现有核心团队的产品)所以给个阅读。此外，看看[沃德罗普](https://github.com/Wardrop/Rack-Next)(又名，机架-下一个)，也许会参与到接下来的事情。**用户建议:** Aaron Patterson 也加入了 [the_metal](https://github.com/tenderlove/the_metal) 来围绕 Rack 2.0 创造话题。有意思。
*   **用户建议**:Lotus 框架一整年都在蓄势待发，2015 年还会继续。Lotus 是一个 web 框架，它“力求简单、更少的 DSL、最少的约定、更多的对象、零猴子补丁和 MVC 层之间的关注点分离。”如果您想脱离轨道，Lotus 是一个很好的选择。(2022 年更新:Lotus 不再提供。)
*   **USER SUGGESTION**: Nick Sutterer (@apotonick), author of Cells, Roar, and other well-known Ruby/Rails gems, has created a new approach to Rails applications called [Trailblazer](https://github.com/apotonick/trailblazer "Trailblazer"). From the Github page:

    > 开拓者是铁轨顶部的一个薄层。它温和地加强了封装，一种直观的代码结构，并给你一个面向对象的架构。

    我对尼克写的关于开拓者的书做了一个早期评论，看起来非常好。我打赌它会在 2015 年掀起波澜。

## 会议

和往常一样，Ruby 的新年意味着一系列新的 Ruby 会议。最大的是:

*   11 月 15 日至 17 日在圣安东尼奥举行
*   4 月 21 日至 23 日，亚特兰大

两个网站都没有更新以反映新的位置，但你现在可以预订机票。对于其他 Ruby 会议，请查看[rubyconferences.org](http://rubyconferences.org/)。我 2015 年的目标之一是参加更多的会议，你也应该参加。

## 你好 2015！

新的一年总是带来新的兴奋。Ruby 肯定会有很多积极的变化，因为它仍然是编程领域最好的语言之一。我确信我忘记了一些重要的事情，或者可能弄错了一两个细节，所以请在评论中随时纠正/添加这些内容。

新年快乐！

## 分享这篇文章