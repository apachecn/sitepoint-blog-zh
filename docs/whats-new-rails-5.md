# Rails 5 的新特性

> 原文：<https://www.sitepoint.com/whats-new-rails-5/>

![rail5](img/80d0fe68715261591b6df9e45ca3e762.png)

Rails 5 就在眼前(目前的目标是 2015 年秋季),并且有一些令人兴奋的功能即将推出。如果你正在运行一个 Rails 商店，你需要为这个主要版本准备你的应用程序。

别担心。一如既往，当发布日期临近时，我们 Sitepoint 将在整个迁移过程中为您提供指导。现在，让我们向您介绍即将到来的事情，以及它将如何改进您的开发过程。

## 主要改进

有一些惊人的公告将从根本上改变我们使用 Rails 的方式。像 Action Cable 和改进的 Turbolinks 这样的新功能可以立即改进我们的 web 开发工作流程。让我们详细看一下每一个。

### 合并 Rails API

在过去的几年里，多亏了 Backbone.js 和 Angular。JS，单页应用程序(spa)的数量正在上升。我构建的最后几个项目纯粹是 SPA，在这些情况下使用 Rails 是多余的。我尝试涉足 Sinatra，甚至纯机架应用程序，但最终写了太多的样板文件。与 Rails 相比，它缺少路由，而且我必须处理太多的安全漏洞。我不得不要么在社区驱动的 rails-API gems 之上编写它，要么继续使用传统的 Rails。

在 Rails 5 中，rails-api gem 将被合并到 core 中，允许使用 Rails 作为一个简单的 JSON API。就我个人而言，我觉得这是一个很好的补充。在为我的 javascript(和其他)应用程序客户端构建 API 时，我不需要做更多的工作。

### Ruby 2.2.1

Ruby 2.2 是 Ruby 社区的一大亮点。它不仅给 Ruby 带来了巨大的性能提升，还引入了一系列新的[函数](https://www.sitepoint.com/new-methods-ruby-2-2/)。此外，[垃圾收集的象征](https://www.sitepoint.com/symbol-gc-ruby-2-2/)也被广泛庆祝。传说瓦莱里娅的龙在第七王国宣布这一消息时降下玫瑰花瓣。好吧！也许我说得太远了，但是你已经了解了大概的情况。

> > ***Note*** : Before migrating to Rails 5, you need to make sure that your application can run on Ruby 2.2.1\.

由于这些增强，Ruby 2.2+是 Rails 5 的成熟候选版本。Rails 5 将**仅**在 Ruby 2.2.1 及以上版本上工作。

### Turbolinks 3

传统的 web 应用程序往往会因为页面完全重新加载而变慢。解决这个问题的一个方法是重新加载最小内容区域。Turbolinks 通过从服务器重新加载正文的内容来解决这个问题，而不是刷新整个页面。虽然这在一定程度上提高了性能，但也相对较慢。Turbolinks 3 旨在解决这一问题。

Tubolinks 3 允许您保留页面的大部分内容，并通过部分内容有选择地更新某些区域。这与 SPA 的工作方式非常相似，您可以选择从服务器完成这一切。听起来很棒，对吧？

这是一个伟大的功能，因为羊群转向单页应用程序。然而，乍一看，似乎您必须手动管理这些片段。这意味着你必须记住在特定的时间点重新加载应用程序的哪一部分。对我来说，它给我的代码增加了很多混乱，很容易出错。就个人而言，我更喜欢使用 React 之类的东西，它可以很好地处理虚拟 DOM。

> > ***Note*** : React works in such a way that it maintains a virtual DOM in which all operations take place. Then, it distinguishes the virtual DOM from the actual DOM, and only makes the required changes. It ensures that DOM is only used when absolutely needed, and changes are minimal.

话虽如此，对于不喜欢摆弄 JS 的人来说，这提供了即时的性能提升。

### 行动电缆

如今，许多项目使用 WebSockets 向客户端推送实时更新。虽然大多数客户端浏览器已经开始支持这一点，但我们仍然需要在服务器上有一个健壮的客户端来管理订阅者并适当地发送更新信号。在一些较新的框架中，这个特性是现成可用的，比如针对 Erlang 的 Phoenix。然而，Rails 社区不得不求助于第三方实现，比如 Pushr，来实现这个功能。

> > ***Note*** : For the layman, WebSockets is a W3C standard, which opens a duplex connection from your browser. The server can talk to the client (i.e. push update) as needed, and update the client's status asynchronously without reloading the entire page. For example, this is how Gmail loads new messages without reloading the entire page.

传统上，Rails 提供了构建优秀应用程序所需的所有工具。这是它如此受欢迎的一个原因，尤其是在创业社区中。缺乏 WebSocket 支持是社区中主要不满的原因之一。看起来 Rails 核心团队注意到了这一点，并提出了 Action Cable。我们将不得不等待一段时间，看看这将如何在现实生活中工作，但它仍然令人兴奋。

## 一些细节

### 倾斜内侧轨道

对于许多 Rails noob 来说，必须弄清楚何时使用`rake`以及何时使用`rails`是一个困惑的来源。现在您不需要在`rake`和`rails`命令之间切换上下文。你可以用`rails`关键字运行所有的 Rake 任务。举个例子，

```
rake db:migrate
```

现在将变成:

```
rails db:migrate
```

这在表面上看起来可能没什么，但这将使初学者的生活简单得多。Rails 5 还将添加一个`restart`命令，快速重启应用程序。

### 活动记录更改

一个几乎没被注意到的变化是对`belongs_to`的更新。当您创建一个学生`belongs_to`类关系时，可以创建一个没有相关类关系的学生。这导致大量数据不一致。在 Rails 5 中，父元素已经变成强制的了。如果您试图在此处插入一条空记录，活动记录将被丢弃。

### 控制器测试的更改

如果你在测试你的模板在你的控制器中呈现什么，你做错了。对你的模板做一个简单的改变会让你彻夜难眠，寻找罪魁祸首。这就是为什么在 Rails 5 中，`assert_template`被弃用的原因。但是，您可以继续使用`assert_select`来检查特定的 DOM 元素是否存在。

如果您一直在测试控制器方法中的实例变量，您应该注意到`assigns`也正在消失。

如果您正在编写控制器测试，新的规定是您应该关注 cookies、HTTP 返回代码和 DB 调用，如果有的话。等等，听起来像是综合测试。好吧，你让我明白了…将来控制器测试可能会被集成测试所取代。

> > ***Note:* *** See [Rails DOM Test](https://github.com/rails/rails-dom-testing) for more details and best practices.

## 包扎

随着 javascript 在今天的 web 开发中占据主导地位，web 框架正在被简化为纯粹的 API 服务。Rails 核心团队意识到了这一点，并朝着正确的方向前进。欢迎在评论区加入我们的讨论。

## 分享这篇文章