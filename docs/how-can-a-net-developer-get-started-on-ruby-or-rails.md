# 一个. NET 开发者如何入门 Ruby 或者 Rails？

> 原文：<https://www.sitepoint.com/how-can-a-net-developer-get-started-on-ruby-or-rails/>

常见问题。决定进入 Ruby 的 NET 开发人员有:我从哪里开始？应该先学什么:Ruby 还是 Rails？我需要安装什么？最好的入门资源是什么？我在哪里可以找到更深入的信息？

在这篇文章中，我将逐一回答这些问题。然而，请记住，这仅仅是基于我自己的经验；对我有效的不一定对你有效(好吧，这应该适用于所有的博客帖子，对吗？).

# 我先学 Ruby 还是 Rails？

在[将 Ruby、Rails、C#和 ASP.NET 放入上下文](https://www.sitepoint.com/putting-ruby-rails-c-and-asp-net-in-context/)之后，可能想到的第一个问题是:我应该先学习 Ruby(语言)，还是先学习 Rails(框架)？

我想大多数人会认为*“嗯，在学习框架之前必须先学习语言……”*。尽管有些人确实更喜欢先学习 Ruby，但 Rails 确实让你在不完全了解 Ruby 的情况下也能完成相当多的工作。那是我走的路线。

Ruby 与最流行的语言并没有太大的不同，所以如果我们以前有过编写软件的经验，我们可以猜到下面的代码是做什么的:

```
if some_condition
    variable = some_value
end
```

即使代码更多地以“Ruby 方式”编写，我们也不会有猜测的问题:

```
variable = some_value if some_condition
```

开始使用 Rails 的好处之一是看到一个正常工作的应用程序的直接回报。如果你首先开始学习 Ruby，那么你会收获对语言语法和结构的理解…我不知道你怎么想，但是后者对我来说听起来有点无聊。

也就是说，这种方法的一个缺点是 Rails 执行了大量的“魔法”来完成任务。这的确让那些想掌控一切的人感到困扰，但我不会；我宁愿看到事情被完成，即使我并不完全理解它。之后，随着我对 Rails 的工作方式越来越有经验，我开始学习[咒语](http://ducktypo.blogspot.com/2010/08/metaprogramming-spell-book.html)。

# 我需要安装什么？

如果你先学习 Rails，我假设你会运行 Windows 系统，那么安装你需要的东西的最好方法就是访问 RailsInstaller.org。下载并安装 RailsInstaller，其中包括您绝对需要的东西(比如 Ruby、Rails 和 Bundler)，以及一些您最终可能会用到的东西(比如 Git 和 Sqlite)。

当你在[RailsInstaller.org](http://www.railsinstaller.org/)网站时，我建议你花时间观看他们的“第二步”指导视频；它引导您完成安装过程，并创建您的第一个 Rails 应用程序(并将其部署到 Engine Yard！)为了确保一切顺利。

## 这是一个命令行世界！

在 Ruby 和 Rails 阵营中，几乎每个人都在命令行上花费大量时间，创建项目、生成控制器和模型、运行测试等等。我建议你使用 PowerShell，而不是旧的 DOS 命令提示符(即使只有。我已经习惯使用 PowerShell 做一些事情…它比任何 IDE 都快，我还没有看到它在我身上死机或崩溃。我还建议你试试控制台，这样你就能得到一个漂亮的选项卡式界面。

## 我们编辑文本文件！

当我们建造的时候。NET 应用程序，我们很可能编辑文本文件。即使当我们使用所谓的“设计者”(拖放我们的方式来征服世界)，在一天结束时，我们也在创建文本文件(xml、xaml、html，你能想到的)。

在 Rails 阵营中，没有人真正关心花哨的设计工具；东西直接编辑文本文件就没了。当我开始使用 Rails 时，我对自己提出了一个要求，要用几个星期的时间使用记事本(实际上是 [Notepad2](http://sourceforge.net/projects/notepad2/) )，因为我想看看我的“Visual Studio 过量使用附加组件”到底会错过什么(我是少数同时使用 [CodeRush](http://devexpress.com/coderush) *和* [Resharper](http://www.jetbrains.com/resharper/) 的怪人之一)。

几个星期后，我发现我错过了一些东西，比如重构工具、某些场景下的自动完成、轻松浏览文件和类，所以我决定尝试一下[RubyMine],这就是我从那时起一直在使用的东西。

我也开始学习 Vim，因为它很适合我以键盘为中心的方式。然而，它有一个陡峭的学习曲线，所以我正在进入它的小剂量。

“但是我希望我的视觉工作室很棒！”，我几乎能听到你的哭声。如果你在 Visual Studio 中使用 Resharper，你可能会觉得使用 RubyMine 就像在家里一样。如果你确实觉得你想要的是 Visual Studio，你可能想试试[的 Ruby in Steel](http://www.sapphiresteel.com/Products/Ruby-In-Steel/Ruby-In-Steel-Developer-Overview)；我没有亲自尝试过，所以不能和它分享什么经验。老实说，由于软件开发主要是编辑文本，我不想启动像 Visual Studio 这样笨重的工具。

# 学习资源

教程、文章、博客、书籍、播客、网络广播…有很多*材料可供您学习 Rails。以下是一些对我非常有用的例子:*

*   僵尸的轨道:我每天都用幽默和古怪的方式学习技术而不是枯燥的文学。Rails for Zombies 是学习 Rails 应用程序的一些基础部分的一种很酷的方式。在每一级，它都有一个简短的视频来解释所涉及的部分，然后它会带你到一个动手实验室，在那里你可以自己尝试，就在你的网络浏览器中，不需要安装任何东西。还有一个可下载的 PDF 文件，包含了课程中展示的幻灯片；我已经把那个文件推到了 [Evernote](http://www.evernote.com) 中，因为当我需要记住一些语法或什么的时候，我会不断地回到它那里…

*   Rails 教程:在这里你可以找到一整本教你 Rails 的书，以及内容的截屏(14 小时的精彩素材！).我拿到了捆绑包(电子书加截屏)，经历了整个过程，跟踪了每一步。我从这件事中学到了很多…

*   [Rails 3 在行动](http://www.manning.com/katz/):伟大的书！

*   [红宝石流氓](http://rubyrogues.com/)、[红宝石秀](http://rubyshow.com/)、[红宝石 5 号](http://ruby5.envylabs.com/):都是我最喜欢的播客。

*   虚拟棕色包(Virtual Brown Bag):免费的周刊，人们聚在一起分享一些小技巧。在过去的几个月里，大部分分享的内容都与 Rails 有关(你的分享也做了相当多的工作！).

希望你也一直在关注我们从。NET 系列，它已经包含了相当多的内容，您可以期待更多的内容。

## 入门指南

再说一遍，这里有很多资料，我已经提到了一些对我有帮助的资料。我建议你在 RailsInstaller 上看完前面提到的视频后，下一站应该是 [Ruby on Rails 指南](http://guides.rubyonrails.org/)，它涵盖了你需要了解的关于 Rails 的所有核心内容。这些应该够你忙一阵子了。

## 越来越深入

在过去的几个月里，我已经在我的个人博客上写了几篇文章，记录了我学习 Rails 的经历。我还保留了一个[共享笔记](http://tinyurl.com/Lassala-TheList)，里面有曾经(或者仍然)对我有帮助的资源。一定要检查这些！

# 没有什么比弄脏你的手更好的了！

我阅读了大量的书籍、文章、博客，观看了大量的视频，与比我聪明得多的人交谈……但这些都比不上亲自动手用 Rails 构建应用程序。只要想出一个简单的应用程序，并致力于用 Rails 编写它。我就是这么做的，而且效果很好。每当我陷入困境时，我会在 freenode.net 的#RubyOnRails IRC 聊天室 [StackOverflow](http://stackoverflow.com/) 上发布问题，或者骚扰我的 Rails 好友。:)

## 分享这篇文章