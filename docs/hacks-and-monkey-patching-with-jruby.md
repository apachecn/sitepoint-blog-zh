# 用 JRuby 进行黑客攻击和猴子修补

> 原文：<https://www.sitepoint.com/hacks-and-monkey-patching-with-jruby/>

如果你从未做过黑客攻击或猴子补丁，那么你很可能没有编写或部署过 JRuby 独立或 web 应用程序。好吧，这有点戏剧性，但是在过去的一年里，在使用 JRuby 的过程中，我不得不实现了相当多的补丁。今天，我想分享我遇到的一些问题，以及我是如何修补它们的。

## 什么是猴子补丁

[猴子打补丁](http://en.wikipedia.org/wiki/Monkey_patch)，大猩猩打补丁或者鸭子打补丁都是指在运行时不修改原始源代码的情况下，对代码进行扩展或者修改的同一个过程。动态语言，比如 Ruby，使得这一点特别容易实现。然而，猴斑地并不全是阳光和玫瑰花瓣。事实上，在 Avdi Grimm 年发表的一篇名为 [Monkeypatching 正在摧毁 Ruby](http://avdi.org/devblog/2008/02/23/why-monkeypatching-is-destroying-ruby/) 的博客文章中，他提出了一些相关问题，比如可持续性。

当然，一个猴子补丁有时是有用的或者完全必要的，但是为什么呢？首先，我们如何才能到达必须依赖创建这些野生补丁的地步？

## 为什么黑客和猴子补丁

如你所料，有各种各样的原因让你觉得需要修改和修补。我个人遇到了以下几类问题，这些问题促使我在去年应用了各种补丁:

*   宝石中的虫子
*   JRuby 中的 Bug

可能值得一提的是，我的工作环境使我无法像在家里(或在许多其他雇主那里)那样容易/快速地下载和使用新发布的代码(gems、platforms 等)。当我不能随心所欲地从 Github 或 Rubygems 获取某些东西时，这种约束导致我在必要时进行破解和修补以完成工作。

在接下来的几节中，我将介绍属于上述类别之一的例子。

### 宝石中的虫子

开发者都知道会有 bug，这是不可避免的。我必须承认，我对大量“开箱即用”的开源软件印象深刻。但是有时候我们会遇到一些需要解决的错误或其他问题。

最近，我遇到了一个关于 [mongo_session_store](https://github.com/brianhempel/mongo_session_store) gem 的[问题](https://github.com/brianhempel/mongo_session_store/issues/9)，它为 MongoMapper 和 Mongoid 提供了 Rails 兼容的会话存储。我遇到的具体问题是`mongo_session_store`试图加载我之前没有安装的 MongoMapper。这一点立刻引起了我的注意，因为我使用的是 Mongoid，所以我并不期望需要 MongoMapper。

我应该倒回去解释一下我是如何将问题缩小到 mongo_session_store 的。在日志中，我只是跟踪了一个异常的堆栈跟踪，它把我带到了[这里](https://github.com/brianhempel/mongo_session_store/blob/master/lib/mongo_session_store/mongo_mapper_store.rb#L1)。此时我知道问题出在哪里，但我不确定为什么。我深入研究了一下，看看代码是如何设置的，以及在`mongo_session_store`中是如何要求的。这时我发现了[mongo*session*store-rails 3](https://github.com/brianhempel/mongo_session_store/blob/master/lib/mongo_session_store-rails3.rb)模块，它利用了 Ruby 的 [autoload](http://www.rubyinside.com/ruby-techniques-revealed-autoload-1652.html) 特性。以下是大部分模块的代码: