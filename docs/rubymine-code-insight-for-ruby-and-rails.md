# Ruby mine:Ruby 和 Rails 的代码洞察

> 原文：<https://www.sitepoint.com/rubymine-code-insight-for-ruby-and-rails/>

*本文由 [JetBrains](https://www.jetbrains.com/ruby/features/ruby_ide.html) 赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

如果你是用 Ruby 或者 Rails 开发的，你可能听说过 [RubyMine](http://synd.co/2A4Tvmo) 。

RubyMine 是 JetBrains 开发的 Ruby 和 Rails 的 IDE。它已经存在了将近 10 年，拥有庞大的用户群。然而，当我们参观行业活动和展会时，我们经常会收到这样的问题:*“好吧，RubyMine 有什么是文本编辑器做不到的？”*。我绝不是想贬低自由代码编辑器的价值或用处，而是*“这么说吧，有很多！”*这是我在启动 IDE 进行快速演示时的通常反应。在这篇文章中，我想告诉你让 RubyMine 脱颖而出的关键点:**代码洞察力**。

Code Insight 包括智能代码完成、代码导航、带快速修复的特定语言检查、智能通知和编辑器内快速文档等功能。这么多术语，但是它们有什么作用，为什么你需要它们？在您切换回您的$EDITOR$并继续您的工作之前，请让我占用您五分钟的时间。

## 自动代码完成

这个特性不需要任何介绍，因为它存在于所有流行的编辑器中。但是，RubyMine 的自动完成功能并不局限于 Ruby/Rails 内置的关键字和基于文本的自动完成功能。当 IDE 在每次启动时索引整个项目时，它可以自动完成几乎任何相关的实体，而不管它是在哪里定义的。你不需要在一个单独的标签页中打开一个文件来自动完成其中的任何声明——ruby mine 已经知道了你的项目的一切！

假设您正在创建一个新表。如您所料，RubyMine 会建议相关列类型和属性的可能自动完成选项:

![RubyMine - creating a new table](img/8b16efef6a9a2496b775bb0db38aa577.png)

但是，当您正在处理一个模型时，您不希望以后能够访问这些列吗？你当然会这么做，IDE 会自动完成所有内置的 Rails 功能:

![RubyMine - IDE autocompletes](img/b57d7f22393ff72db478b05fbaaf9270.png)

自动完成方法的能力如何，早先在模型中定义的，比方说，在项目中的控制器或其他 ruby 文件中？你猜对了:

![RubyMine autocomplete methods](img/76f150c08a85034f325629adfd995498.png)

IDE 不会为您希望看到给定模型中定义的方法的列名提供自动完成建议，反之亦然。这是一个非常重要的特性，使得 RubyMine 补全从其他编辑器中脱颖而出。

您可以用同样的方式处理您的视图。我说的不仅仅是基本的 HTML 和 ERB 语法建议，它们是给定的，实际上是项目中所有指定的实体:

![RubyMine - work with your views](img/1d0265314916a0b82c20a1b44bed5d0b.png)

## 转到声明(也称为转到定义)

当我与我们的客户交谈时，我总是问他们最喜欢的 RubyMine 功能是什么。有史以来最热门的回复是*“去宣言石吧！”*。虽然我更愿意听到关于我们的 [GUI 调试器](http://synd.co/2nGERjt)或其他高级功能的成功故事，但是积极的反馈总是好的！但是我很好奇为什么这种情况一次又一次地发生，所以最终我开始比较 RubyMine 的*转到声明*和一些流行编辑器中的*转到定义*。

毫无疑问，在导航到声明时，IDE 被证明要可靠得多。只需使用⌘+Click | Ctrl+Click(或者⌘+B | Ctrl + B，如果您将插入符号放在所需的对象上)并跳转到您需要的定义:

![RubyMine - Go declarations](img/d1f763f882abf713dfc5acb03368d4f7.png)

您不仅可以轻松地跳转到项目中的类、方法或任何其他实体声明，还可以导航到 gem 及其实体的定义。需要重写一点 Ruby on Rails 来让你的项目更好吗？不要再说了:

![RubyMine - Go to declaration rewrites](img/f1cf75764c365a43123ad5a63b1213fc.png)

我试图找到一些插件，可以为我最近检查的代码编辑器做同样的事情，但它们没有多大帮助。如果你知道一些，请在评论中提出你的建议。但在那之前，我坚持我的故事:*去声明*一个人就应该让你想给 RubyMine 一个机会！

## 静态分析

我喜欢 Atom 的地方在于它的语法突出。它不会让我错过一个结束标记或`end`。我还发现了一个很棒的插件，它可以给所有 RuboCop 错误加下划线，甚至允许你自动更正文件中的错误。恭喜，Atom 和插件创建者，这太酷了！

直到最近，我不得不说这种 RuboCop 支持和我们在 RubyMine 中的一样好。但随着最新发布的 [RubyMine 2017.3](http://synd.co/2kakSov) ，你现在不仅可以用 RuboCop 修复整个文件，甚至可以选择是否要自动更正所有可能的犯罪，特定的警察部门，甚至是特定的犯罪类型。您可以直接从编辑器中获得这一切，只需按 Alt+Enter:

![RubyMine - static analysis](img/4c9df56ad708b34b9665de38e65f7224.png)

至于语法检查，RubyMine 肯定足够聪明，可以为 Ruby、Rails、JavaScript、HTML、CSS、ERB 等等提供基本的语法高亮显示。最重要的是，IDE 有许多自己有用的意图，比如能够将`do … end`块转换成`{}`，或者将语句修改成尾随条件。通过同一个 Alt+Enter 找到并应用这些:

![RubyMine - syntax checking](img/0460a9f0ab576675f76958b5c5728947.png)

顺便说一下，除了静态分析，但在意图动作方面，RubyMine 甚至提供了内置的 RegExp 检查器:

![RubyMine built in RegExp checker](img/4cc83ad5d61049bc270dff6b6b7fb7c7.png)

如您所料，可以在设置中找到并轻松启用或禁用这些和其他检查和意图。

另一个 Code Insight 好东西是*参数名提示*。正如您可能从它的名字中猜到的那样，它帮助您理解您传递给方法的参数的名称:

![RubyMine parameter name hints](img/952bc278dea28570cd62b26c64d6045e.png)

参数名称提示的另一个很好的选择是*参数*信息，它可以通过按⌘+P | Ctrl+P 来触发。例如，如果您意识到内联提示太让您恼火，并且您决定关闭它们，但是在某些时候需要检查您已经声明的参数名称，这可能会很有帮助:

![RubyMine parameter info](img/052cf58df6544c18223507b11ed8addc.png)

## 智能通知和处理措施

作为所有 JetBrains IDEs 的另一个标志性功能，RubyMine 提供了智能通知，帮助用户永远不会忘记例行任务，如配置检测到的数据源，或在向 gem 文件添加新的 gem 后运行`bundle install`。

还有更多多阶段的智能动作可以派上用场。例如，RubyMine 有一个在 Rails 实体(例如控制器)和它的测试之间快速导航的快捷方式(Shift+⌘+T | Ctrl+Shift+T)。如果这样的测试不存在，RubyMine 会建议根据你已经安装的测试框架来创建。但是如果你还没有任何测试框架呢？在这种情况下，RubyMine 会将所需的 gem 添加到 gem 文件中，为您安装它，然后导航到新创建的控制器测试:

![RubyMine shortcut from Rails entity to test](img/1ec6fbc3bbf5b50cbf96e91a82c8c30a.png)

说到 Rails 实体，您还可以使用⌃⌘↑ / Ctrl+Alt+Home 键[导航到相关的 Rails 符号](http://synd.co/2BQlW85)。

## 快速文档

最后，RubyMine 为内置的 Ruby/Rails 实体以及应用程序中声明的实体提供了编辑器内快速文档。这意味着你不需要每次偶然发现某个别名时都在网上冲浪，或者在整个应用程序中搜索你不记得的方法声明。只需将插入符号放在您想要查找的关键字上，然后按 F1。最棒的是，它还可以在自动完成列表中工作:

![RubyMine quick documentation](img/9a839ea1d3167462bde7d2fc3d4ac0b1.png)

上述功能涵盖了 RubyMine 中的一系列代码洞察功能，但 IDE 中还有更多令人喜爱的功能:安全重构、基于 GUI 的调试器和测试套件，以及一系列[导航功能](http://synd.co/2BQlW85)。查看我们的[网站](http://synd.co/2A3Yaox)，了解更多关于它们的信息。

如果你觉得这些值得一试，那就去下载 RubyMine 并免费使用一个月。如果你的整个开发团队都想尝试 RubyMine，你也可以申请延长 90 天的试用期。

最后，不要忘记检查你是否有资格获得折扣许可证:OSS 项目和学生可以申请免费的 JetBrains 许可证，创业公司可能会获得 **50%的折扣！**

快乐发展！

## 分享这篇文章