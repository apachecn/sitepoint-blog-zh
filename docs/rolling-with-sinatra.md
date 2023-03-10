# 与辛纳特拉一起滚动

> 原文：<https://www.sitepoint.com/rolling-with-sinatra/>

在网页开发方面，Sinatra 的灵活性惊人。与 Rails 不同，它一点也不固执己见，基本上让您做出所有的设计决策。它确实有一些约定，例如在“视图”文件夹中自动查找视图模板，但几乎所有这些默认设置都可以很容易地更改。辛纳特拉不会为你做任何决定——你实际上是从一张白纸开始。Sinatra 的维护者 Konstantin Haase 认为这是 Sinatra 最大的优点，也是它最大的缺点，因为 Sinatra 不会阻止你编写糟糕的代码。

考虑到在 Sinatra 中创建应用程序时有太多的选择可以选择，我决定四处打听一下，看看人们在使用它时是怎么做的。我问了以下问题:

*   你有固定的文件夹结构或编码模式吗？
*   你倾向于使用经典风格还是模块化风格？
*   你使用任何引导代码吗？
*   你曾经使用过内嵌视图吗？
*   还有别的吗？

我得到了一些有趣的回应，我想在这里分享一下。你也可以在这里查看整个帖子:
https://groups.google.com/forum/#!msg/sinatrrb/bfax CCK 3d 8 I/mxlv 6 ydobcaj

## 你有固定的文件夹结构或编码模式吗？

Sinatra 没有目录结构可言——你甚至没有一个应用程序文件夹，除非你自己创建。如前所述，它有一些很好的默认设置，如自动将视图模板保存在“views”目录中，将公共资源保存在“public”目录中，并使用一个名为 layout.erb 的文件作为默认布局。所有这些都可以使用 set 方法轻松地更改，如下所示:

```
set :public_folder, 'assets'
set :views, 'templates'
set :erb, :layout => :base
```

我询问的许多人倾向于使用类似 Rails 的“模型、视图和控制器”文件夹结构。他们也倾向于使用与 RubyGems 相似的结构，比如“lib，test/spec/，public”文件夹。

另一种流行的技术是使用一个名为“init.rb”的文件，它需要所有其他相关文件。这对于从控制台或在测试期间运行您的应用程序非常有用。

Sinatra 的创建者 Blake Mizerany 说，他更喜欢使用一个目录，将他的所有模块和视图保存在一个地方。

我喜欢保持我的文件夹结构非常简单，通常有一个名为 main.rb 的文件，包含大部分应用程序代码。然后，我通常会使用一个公共和视图文件夹，然后把它留在那里。任何多余的文件通常会放在根目录下。

## 你倾向于使用经典风格还是模块化风格？

Sinatra 有两种截然不同的编码风格——经典和模块化。你在网上找到的大多数例子都是经典的应用程序，下面是另一个例子:

```
require 'sinatra'

get '/hello' do
  "Hello World!"
end
```

采用模块化风格的相同应用程序如下所示:

```
require 'sinatra/base'

class Hello &lt; Sinatra::Base
  get '/hello' do
   "Hello World!"  
  end
end
```

如您所见，模块化风格应用程序的主要区别在于，所有代码都包装在一个从 Sinatra::Base 派生的子类中。然而，在一个经典的应用程序中，你只需要“sinatra ”,然后继续使用它——这是大多数在线教程中使用的风格。

大多数回答我问题的人更喜欢使用模块化风格。Josh Cheek 提到经典风格对于演示技术是有用的(这就是为什么它可能被用于大多数 web 示例的原因)。

约翰·努内马克(GitHub)说:

> 我再也不会用经典了。太污染了。

这是指全局名称空间可能被同名的方法污染。在编写小型应用程序时，这通常不是问题，但是如果您正在编写大型模块化应用程序(特别是如果不同的人在不同的模块上工作)，这可能会成为一个更大的问题。

贾森·罗杰斯还指出了一种有用的技术，可以帮助你将 URL 映射到一个特定的类:

> 如果我要将资源划分到不同的路径下(例如“/admin”、“/api”等)。)我将使用模块化方法，并将 Rack 中的各个模块映射到它们的路径名下。

这可以在 config.ru 文件中使用 Rack 的`map`方法完成，如下所示:

```
require 'sinatra/base'
require './main'

map('/admin') { run adminController }
map('/api') { run apiController }
```

就个人而言，我喜欢使用经典风格的应用程序，并认为它们非常直接，允许您快速开始编写代码。缺点很少，如果它变得更大，很容易转移到模块化风格的应用程序。然而，如果您想将您的应用程序打包成一个 gem 或扩展，那么您确实需要采用模块化风格。

我应该指出，Sinatra 的开发者仍然致力于保持两种不同风格的应用程序。

## 你使用任何引导代码吗？

Rails 有许多代码生成器，可以让您快速启动并运行各种引导代码。我想知道在辛纳特拉，人们是否使用过类似的东西来启动他们的项目。

Geoffrey Grosenbach 使用了一点引导代码来保存一遍又一遍地设置相同的东西:

> 有时我从一个现有的简单 Git repo 开始，特别是如果我要使用需要一些设置的主干或其他框架。

其他人最初喜欢包括 Knockout.js 和 Twitter Bootstrap 之类的东西(可能是为了使前端开发更容易)。

用户 dimreme 在 Sinatra 的 Google Groups 页面上提到了一个正在开发的代码生成工具，听起来很有趣。最好能看看这方面是否取得了任何进展。

在我自己的 Sinatra 项目中，我不倾向于使用任何 bootstap 或生成器代码，因为开始一个项目太容易了。虽然，我考虑过把一个极简的文件结构放在一起，包括一些我通常使用的基本 CSS 和布局。

有许多类似的项目可用，如[Sinatra-Bootstrap-Starter](https://bitbucket.org/aleksbasara/sinatra-bootstrap-starter/src)，尽管这些项目开始从你那里拿走一些设计决策——开发你自己的适合你的项目总是更好！

## 你曾经使用过内嵌视图吗？

内联视图允许您将所有视图代码保存在与应用程序相同的文件中。这里有一个简单的例子:

```
require 'sinatra'

get '/hello/:name' do
  @name = params[:name]
  erb :hello
end

__END__

@@hello
<h1>Hello!</h1>
```

在这个例子中，名为“hello”的视图被放在了`__END__`声明之后。所有视图都以“@@”开头，后跟模板名称。

大多数人不使用它们，尽管一个明显的例外是 Blake Mizerany，他喜欢在小应用程序中使用它们:

> 当只有几个并且很小的时候，我会使用内联模板

就我个人而言，我经常喜欢使用内嵌视图，我认为这是 Sinatra 最酷的功能之一。当我摆弄一些代码或开始一个项目时，我真的很喜欢我可以从一个文件中创建一些东西。事实上，Avdi Grimm 设法创建了一个 Sinatra 应用程序，在同一个文件中包含所有内容，包括测试！(http://devver . WordPress . com/2009/05/13/single-file-Sinatra-apps-with-specs-baked-in/)

## 还有别的吗？

很多人用不同的方式使用 Sinatra，最主要的观点是他们想选择自己的做事方式。

Geoffrey Grosenbach 喜欢 Sinatra 更多地揭示事物的工作原理，从而帮助你更好地学习这些技能:

> 学习和使用 Sinatra 帮助我更好地掌握了其他任务(如设置测试)。

他还认为额外的努力会带来更快的开发时间:

> 尽管有一些工作，我喜欢和辛纳特拉一起工作的速度。

他还接着说:

> 我很少使用 Rails 生成器，而经常使用 NoSQL 数据库，所以 Sinatra 对于我想编写的大多数应用程序来说是完美的。

里克·奥尔森(GitHub)，喜欢用

> 很多胡子和架子测试

这与其他人使用的不同，但利用 Sinatra 的灵活性非常容易做到。

对于一些人提出的 Sinatra 缺乏功能性的观点，贾森·罗杰斯反驳道:

> 我想说这就是宝石的用途。辛纳屈的一大好处就是没有主见。

他还指出，即使在这个小样本中，人们选择使用 Sinatra 的方式也是不同的，这意味着每个人都不可能对一刀切的方法感到满意。这触及了辛纳特拉的核心——让你选择自己的做事方式，对于喜欢以特定方式做事的控制狂来说是完美的！

soldier.coder 用了一个很好的比喻来解释为什么你可能想要使用 Sinatra 提供的选择你自己的宝石的细粒度解决方案:

> 为什么不直接用 rails 呢？Rails 就像是在你真正需要特种部队或海豹突击队的时候，把一艘驱逐舰带到了海盗/人质的境地。大型应用？大型应用程序是一种误导，因为它实际上依赖于如何组织的分解。大型整体式与大型模块化有很大不同。将软件写成服务鼓励关注点的分离。辛纳特拉似乎是这种分离的理想之地。

这是一个很好的观点。Sinatra 的模块化风格，积极鼓励你编写模块化代码，可以在大型应用程序中拼凑起来。这样做有很多好处——你可以在其他应用程序中重复使用模块，你可以删除一个不再需要的模块，不同的团队可以独立地处理单独的模块。

Blake Mizerany 还指出了提前计划的好处:

> 总的来说，我喜欢对我正在做的事情进行大量的预先考虑；这让我可以让事情变得简单。

提前做一些规划，你就可以以一种简单的设计方式来设置你的项目，Sinatra 会让你这么做的。

我认为我收到的所有反馈都有助于突出 Sinatra 如此灵活的原因:如果你想快速启动并运行某些东西，那么你可以立即启动一个带有内嵌视图的经典应用程序。如果你想构建一个大的应用程序，那么你可以用你自己定制的文件结构去模块化，并分离你所有的关注点。

在我的新书《快速启动 Sinatra》中，大部分话题都有更深入的探讨。

我希望这篇文章能让你领略到与 Sinatra 合作的许多不同方式。你呢？如果你用过 Sinatra，你怎么滚？如果你还没有尝试过辛纳屈，那么这篇文章有助于展示辛纳屈的能力吗？

别忘了， [Jump Start Sinatra](https://www.sitepoint.com/jumpstart/ "Jump Start") 很快就要出了。去报名吧，准备好了会通知你的！

## 分享这篇文章