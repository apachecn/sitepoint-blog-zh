# Ruby 微框架综述

> 原文：<https://www.sitepoint.com/ruby-microframeworks-round/>

![ruby_microframeworks](img/7a1c98e910b77446ae502fe0c316a7eb.png)

问任何人一个 Ruby 框架的名字，大多数人都会回答“Ruby on Rails”。如果你要求一个更小的框架，他们可能会回答‘Sinatra’(从技术上来说，它不是一个框架，而是一种特定领域的语言，但是不要太专业)。Rails 和 Sinatra 是 Ruby web 开发世界的宠儿。总的来说，他们似乎涵盖了所有的基础，这是我在这篇文章中得出的结论[。鉴于这两个“框架”的成功，是否还需要其他任何框架？](https://www.sitepoint.com/rails-or-sinatra-the-best-of-both-worlds/)

事实证明，实际上有相当多的 Ruby 微框架在使用(从技术上讲，其中一些也是 DSL，但是微框架这个术语似乎已经过时了)。以下是我在 GitHub 上找到的六个项目:

*   [霍比特人](https://github.com/patriciomacadden/hobbit)，由[帕特里西奥·麦克·艾登](https://github.com/patriciomacadden)创作
*   [纽约，纽约(NYNY)](http://alisnic.github.io/nyny/) ，由[安德烈·利斯尼克](https://github.com/alisnic)创作
*   [布鲁克林](https://github.com/luislavena/brooklyn)，由[路易斯·拉韦纳](https://github.com/luislavena)创作
*   [烧焦](http://scorchedrb.com/)，由[汤姆·沃德罗普](https://gist.github.com/Wardrop)创作
*   [古巴](http://cuba.is/)，由[米歇尔·马滕斯](https://github.com/soveran)创作
*   [南希](https://github.com/guilleiguaran/nancy)，由[吉列尔莫·伊瓜兰](https://github.com/guilleiguaran)创作

我的第一反应是，他们似乎占据了与 Sinatra 非常相似的空间，我认为它涵盖了 Ruby web 开发的“微框架”领域。我想知道为什么所有这些微框架都存在。有什么意义？

因此，为了找到答案，我联系了这些微框架的创建者，并问了他们以下问题:

*   是什么启发了你创建自己的 Ruby 框架？
*   你是怎么想出这个名字的？
*   Sinatra 是一个非常简约的 Ruby 框架，已经有了很大的吸引力。你的框架和辛纳特拉有什么不同？
*   你为什么没有把你的框架提供的额外特性贡献给 Sinatra，无论是作为补丁还是扩展？
*   你自己写框架有什么收获？有没有提高你的 Ruby 技能？
*   你的框架未来会怎样？

我还要求他们提供一些代码片段来展示他们的微框架是如何工作的。

在阅读了他们的回复后，很明显，许多创作者认为辛纳特拉不够小或不够专注。所有这些项目的代码基数都比 Sinatra 小得多(而且它们比 Rails 小得多！)他们中的许多人还报告在基准测试中比 Sinatra 更快。在许多情况下，这是通过更贴近 Rack 并避免 Sinatra 的许多助手提供的语法糖(如直接使用 Rack 的`render`方法)来实现的。在其他情况下(如布鲁克林)，它是通过保持比 Sinatra 窄得多的范围和避免遗产支持。其中一些，特别是古巴和焦土，是由使用不同于 Sinatra 的方法，特别是路线处理的愿望所驱动的。有趣的是，听到许多开发人员报告说，由于开发了这些项目，他们的 Rack 知识得到了提高。

以下是回复:

## 问题 1——是什么激发了你创建自己的 Ruby 框架？

《霍比特人》的创作者帕特里西奥·麦克·艾登:

> 我从去年开始给辛纳屈捐款。这促使我钻研 Rack，我想尝试做一个尽可能小、尽可能接近 Rack 的框架。
> 
> 这些是我的指导方针:
> 
> *   霍比特人一定很快。
> *   《霍比特人》的核心必须只有最基本的东西:定义路线。
> *   Hobbit 必须可以用标准的 ruby 类和模块来扩展。默认情况下，不会加载这些扩展(即渲染视图、处理会话、过滤器、异常处理)。
> *   零配置

安德烈·利斯尼克，纽约的创造者纽约:

> 我想我写它的主要原因是为了学习。我是一名 web 开发人员，我想知道所有的框架魔术是如何工作的。

路易斯·拉韦纳，布鲁克林的创造者:

> 对于我们公司的一个项目，我们必须输出一个大的直方图作为 JSON (128K)。我们注意到，即使有了高级别的缓存，我们的应用程序能够服务的请求数量也不足以满足我们的需求。
> 
> 我们发现 Sinatra 占用了很大一部分性能，甚至在生产模式下运行。
> 
> 由于应用程序的唯一目的是服务于这个 JSON，我们开始研究可能的问题，并在机架上剥离几乎所有东西。
> 
> 布鲁克林就是这样诞生的，只为一个目的。我称它为网络工具，而不是微框架，只是因为它不能像 Sinatra 那样处理一大堆事情。只是架子上一张更漂亮的脸。

《烧焦》的创作者汤姆·沃德罗普:

> 尽管我很喜欢 Sinatra，但在现实世界中使用它时，我发现它有一些令人沮丧和不必要的限制。这些主要是代码管理和可伸缩性的问题。您要么必须将整个应用程序的所有路由放在一个“控制器”中，在其中共享助手、过滤器、配置等，要么尝试将应用程序拆分成多个 mini Sinatra 应用程序，并使用 Rack 将它们安装在不同的映射下。这两种解决方案都不完美。我认为帕德里诺可能会解决其中的一些问题，但发现它同样令人沮丧，如果不是更多。
> 
> 鉴于我对自己想要什么有一个很好的想法，并且因为它足够简单(也许不是事后才知道)，我决定推出自己的产品，因为我知道我不仅会挠自己的痒，而且会挠许多其他开发人员的痒，他们在使用 Sinatra 时无意中遇到了实际问题。

米歇尔·马滕斯，古巴的缔造者:

> 我发现了一个叫做[朗姆酒](https://github.com/chneukirchen/rum)的小脚本，由 Rack 的作者 Christian Neukirchen 创作。在我看来，它有两个很棒的想法:首先，你可以基于请求的任何方面在你的应用程序中创建代码路径，而不仅仅是基于 HTTP 方法和请求 URI。这是非常强大的，因为，例如，您可能希望根据访问者是否经过身份验证来选择不同的路线，这是您无法通过单独查看 HTTP 方法和 URI 来猜测的。这只是一个例子，因为古巴的`on`结构主要表现为一个控制流关键字:`on default ...`和`on 2 + 2 ...`一样有效，所以你可以使用任何条件。
> 
> 第二个想法是，通过嵌套`on`构造，您构建的路由表现为确定的有限自动机。这意味着，例如，一旦您匹配了请求的一部分，您就不需要再次匹配它。这使得它在现实世界中非常快。
> 
> 有几个与古巴共建的开源网站，如 [Redis](http://redis.io) 和 [Punchgirls Jobs Board](https://jobs.punchgirls.com/) ，还有一些解释一些基本概念的[幻灯片](http://files.soveran.com/cuba/)。

《南希》的创作者吉列尔莫·伊瓜兰:

> 米歇尔·马滕斯和#rubysur 社区的一些其他成员推荐了一些关于理查德·加布里埃尔、T2、里奇·希基和 T4·约翰·梅达等作家的简约风格的文章、演讲和书籍，这些文章、演讲和书籍给了我灵感。Michel Martens (@soveran)和 Cyril David (@cyx)在他们的微观框架 [Cuba](http://cuba.is/) 中的工作曾经是并且继续是一个巨大的灵感。

## 问题 2——你是如何想出这个名字的？

《霍比特人》的创作者帕特里西奥·麦克·艾登:

> 我首先将它命名为“bonsai”，但是已经有一个“bonsai”rubygem 使用了这个名字。给东西命名真的很难，但幸运的是，在和我的一个朋友聊天时，我想到了霍比特人这个名字。

安德烈·利斯尼克，纽约的创造者纽约:

> 最初，NYNY 被定位为一个小辛纳屈克隆，因此得名(纽约，纽约是一首由弗兰克辛纳屈)。然而，在这一点上，NYNY 有一套不同的特点。

路易斯·拉韦纳，布鲁克林的创造者:

> 我工作的公司(第 17 区)在纽约有一个办事处，更确切地说，在布鲁克林。有一种趋势是，辛纳特拉式的框架作品将成为 NYNY 弗兰克·辛纳特拉的主题，那么为什么不是纽约的另一个区呢？；-)

《烧焦》的创作者汤姆·沃德罗普:

> 这是对枯燥的缩写词的一种玩弄。烧焦的大部分是为了给开发者提供足够的机会来减少重复。

米歇尔·马滕斯，古巴的缔造者:

> 受朗姆酒的启发，我立刻想到了古巴。古巴是一个小而高效的国家，这也是图书馆的特点。

《南希》的创作者吉列尔莫·伊瓜兰:

> 我认为南希就像辛纳屈的一个“孩子”，这就是为什么我选择南希作为名字，这是对弗兰克·辛纳屈的女儿南茜·辛纳特拉的一个参考。除此之外，我还是南茜·辛纳特拉音乐的超级粉丝。

## 问题 3——是什么让你的框架与 Sinatra 不同？

《霍比特人》的创作者帕特里西奥·麦克·艾登:

> 它比 Sinatra 更快，分配的内存也更少。如果你想看一些基准，我推荐你去看路易斯·拉韦纳的《https://github.com/luislavena/bench-micro》。

安德烈·利斯尼克，纽约的创造者纽约:

> 如果辛纳特拉非常简约，那么我会说 NYNY 非常非常简约。Sinatra 的源代码有~2k 行。相比之下，NYNY 只有 300 个。与此同时，NYNY 缺少的东西并不多，而辛纳屈提供了这些东西。NYNY 的速度也快了 25%,拥有更强大的路由器(它使用 Rails 的路由器),但缺少 Sinatra 拥有的许多便利助手。(比如 erb :something，就是 render 上面的糖)

路易斯·拉韦纳，布鲁克林的创造者:

> Sinatra 实施了一系列我认为是不好的做法。幸运的是，新版本鼓励使用`Sinatra::Base`类来构建您的应用程序。
> 
> 它还做了很多事情来解决以前版本的 Rack，或者处理某些类型请求的特定场景，而不是利用 Rack 中间件方法来构建这些。
> 
> Brooklyn 所做的甚至与 Sinatra 所提供的相差甚远，也永远不会，它生来就是为某种类型的内容(JSON)服务的，不会过多考虑观点或其他东西。
> 
> 它旨在与其他机架中间件一起安装和组成一个更大的应用程序，而不是单独使用来创建软件。

《烧焦》的创作者汤姆·沃德罗普:

> 从 DSL 的角度来看，《烧焦了》和《辛纳特拉》非常相似，所以第一眼看上去人们会奇怪为什么我好像刚刚重写了《辛纳特拉》。与 Sinatra 的主要区别是引入了控制器概念。这些控制器可以以许多创造性的方式组合和嵌套，并允许您将助手、过滤器、错误处理程序、条件、配置等扩展到相关的路由组。
> 
> 另一个不同是，烧焦的尝试甚至比辛纳特拉更简单，更可预测，如果可能的话，甚至更少固执己见。在辛纳特拉有几件事会让你大吃一惊。Sinatra 有时做的比你想象的要多一点。我想我已经实现了这些目标，这是因为我进行了更周到的设计，抵制了添加太多智能的诱惑，并且没有提供与机架重叠的功能。Sinatra 像大多数框架一样，提供了许多抽象框架的助手。随着烧焦，我想暴露开发人员机架尽可能合理。其思想是 Rack 的知识可以在框架之间移植，而 as helpers 和 DSL 则不能。如果有人已经熟悉 Rack，那么可以利用这种经验来降低对 curve 的学习曲线，反之亦然。

米歇尔·马滕斯，古巴的缔造者:

> 我过去经常使用 Sinatra，甚至在它上面创建了一个现已废弃的框架，名为 Monk。但让我搬到古巴的是它的灵活性，而且开销很小。我现在发现 Sinatra 太以路径为中心了，有了 Cuba，我可以构建许多子应用程序，它们可以基于任何标准安装在任何地方。作为副产品，嵌套的控制流思想和它拥有更少的代码和更少的职责的事实使它更具性能，可能这些原因使它更容易教授和理解。

《南希》的创作者吉列尔莫·伊瓜兰:

> 我不认为 Sinatra 是一个“非常简约”的框架，你需要 [22 页才能在论文](http://inchworms.net/blog/2013-07-01-sticking-it/)中打印完整的代码，甚至[的主要维护者也会迷失在代码](http://inchworms.net/blog/2013-07-03-so-lost/)中！！相比之下，你可以打印完整的南希代码在短短 1 页。
> 
> 与 Sinatra 和其他极简框架相比，Nancy 非常小，根据基准测试，它比 Sinatra 快 2 倍。

## 问题 4——你为什么没有把你的框架提供的额外特性贡献给 Sinatra？

《霍比特人》的创作者帕特里西奥·麦克·艾登:

> 我已经给辛纳屈捐了。《霍比特人》的大部分功能都是受西纳特拉的启发，但《霍比特人》的目的不同，大部分都与西纳特拉不兼容。

安德烈·利斯尼克，纽约的创造者纽约:

> NYNY 拥有的所有功能都已经在 Sinatra 中了(除了 NYNY 使用的 Rails 路由器)，或者在 contrib 包的核心中。我只是用更简单的方式实现了它们。(同样，目标是通过重新发明轮子来学习)

路易斯·拉韦纳，布鲁克林的创造者:

> Sinatra 是一个大约 1700 SLoC 的单一文件，它定义了几个类并将它们连接在一起。试图删除辛纳特拉的一些遗产或改变一些东西，这些东西可能不会被接受，因为这种变化的规模。

《烧焦》的创作者汤姆·沃德罗普:

> 因为烧焦的主要特征是建筑。对 Sinatra 应用这样的改变会破坏向后兼容性，尽管我喜欢把烧焦作为 Sinatra 2.0 的一个合适的起点。

米歇尔·马滕斯，古巴的缔造者:

> 辛纳特拉试图做一件事，并把它做好，它采取了非常具体的方法。它采取的方法非常好，而古巴提供了一种不同的方法。我反对用同一个工具添加特性来解决不同问题的想法。无论古巴做什么，都可能被烹饪成辛纳特拉，反之亦然，但结果将是一个更加复杂和脆弱的工具。

《南希》的创作者吉列尔莫·伊瓜兰:

> 南希没有任何辛纳特拉没有提供的额外功能，南希有辛纳特拉功能的“子集”。

## 问题 5——你从编写自己的框架中获得了什么？有没有提高你的 Ruby 技能？

《霍比特人》的创作者帕特里西奥·麦克·艾登:

> 是的，绝对的。编写自己的库并为别人的库做贡献肯定会提高你的技能。就我而言，我从路易斯·拉韦纳那里学到了很多，他回顾了《霍比特人》，给我指出了很多东西。

安德烈·利斯尼克，纽约的创造者纽约:

> 关于 Rack 内部工作的大量知识，以及一个我可以自信地展示的作为我编程技巧的例子的小宝石。(我为此付出了很多努力和心血)

路易斯·拉韦纳，布鲁克林的创造者:

> 我学到了很多关于机架框架和中间件组成的知识。我了解了类变量继承和对象分配的减少。最后但同样重要的是，我了解了重用同一个对象实例来服务多个请求所导致的线程安全。

《烧焦》的创作者汤姆·沃德罗普:

> 首先，它让你意识到一个人必须为相对简单的框架和库付出的工作量。现在，我也更加熟悉 Rack 及其局限性。这无疑引发了我对潜在的 Rack 2.0 的兴趣。我想我的红宝石技能也无意中得到了提升。我在做什么项目并不重要，我总是喜欢探索尽可能多的创造性解决方案，试图找到完美的解决方案，碰到语言的限制，这在 Ruby 中很少，因此我喜欢它。

米歇尔·马滕斯，古巴的缔造者:

> 我获得了试验朗姆想法的机会。古巴的第一个版本是朗姆酒，经过一些修正和一些补充，把它包装成一颗宝石。后来一些同事开始使用它并喜欢它，然后一些人开始用它做客户工作，这证明其他人也对 Rum 的方法感兴趣。
> 
> 写古巴并不具有挑战性，因为背后的想法非常简单。我不认为编写 Cuba 或阅读其源代码真的能提高某人的 Ruby 技能。

《南希》的创作者吉列尔莫·伊瓜兰:

> 我显著提高了我的 Ruby 技能。多亏了这个项目，我学到了很多关于 Rack 的知识，这对我理解像 Rails 这样更大的框架很有帮助，并且成为了 Rails 核心团队的[成员。](http://rubyonrails.org/core)

## 问题 6——你的框架有什么样的未来？

《霍比特人》的创作者帕特里西奥·麦克·艾登:

> 我真的不知道。我希望人们像我开发和使用它一样使用它并享受它。
> 
> 我也写过 [Hat](https://github.com/patriciomacadden/hat) ，一个霍比特人的应用模板:。这是在霍比特人之上的一个更完整的框架。

安德烈·利斯尼克，纽约的创造者纽约:

> 此刻，我觉得 NYNY 是相当完整的。我在工作和业余时间都用它来做业余爱好项目。哲学是 NYNY 应该保持简单易懂。想到的任何新奇的东西我都实现为 NYNY 的扩展。(如链轮集成)

路易斯·拉韦纳，布鲁克林的创造者:

> 我认为它不会超过现在的水平。它达到了它的目的，做了它的目标。在某些性能路径上，它可能会变得更好一些(试图减少对象分配和 URL 匹配技术)

《烧焦》的创作者汤姆·沃德罗普:

> 没有理由说它不能达到 Sinatra 的流行程度，尽管它需要大量的文档来满足那些对 Ruby 和其他 Ruby web 框架缺乏经验的人的需求。我很难想象有人会喜欢辛纳屈而不被烤焦。目前，烧焦的目标是更有经验的 Ruby web 开发人员；那些已经熟悉 Rack 和 Sinatra 的人。
> 
> 至于以后的发展。希望很快发布一个 v1.0。我仍然需要在一些现实世界的应用程序中测试这个框架，以确保我没有遗漏任何明显的东西。contrib 库也在计划之中，尽管可能要到 1.0 版才会发布。我可能会暂时将任何 contrib-esque extras 捆绑到核心中，例如我的 content_for for ERB 模板的简化实现，我必须为一个从 Padrino 移植到烧焦的项目创建它。

米歇尔·马滕斯，古巴的缔造者:

> 这是一个小工具，它很稳定，现在还没有什么工作要做。我们现在是 3.1.1 版本，大约一个月前发布的，但是和之前的版本相比只有一个微小的变化。在大约两年的时间里，它没有任何重大变化，这表明它运行良好。

《南希》的创作者吉列尔莫·伊瓜兰:

> 该框架最近获得了一些关注和关注，有人有兴趣将其作为今年 Rails Girls 代码之夏的项目之一，希望我能在这个夏天指导一群学生改进它！！！

# 代码示例

我想看一些使用这些微框架的例子，所以我要求每个创建者提供两个代码片段，实现与我编写的 Sinatra 应用程序相同的结果。

## 片段 1–你好

第一个代码示例是一个简单的“Hello World”程序，它从 URL 获取一个命名参数。我在 Sinatra 中使用了一个助手和内嵌视图。有趣的是，这些框架中只有一个(NYNY)提供了内嵌视图，并且它们都使用了基于类的应用程序风格，这与 Sinatra 的模块化风格相似。当然与 Sinatra 有很多重叠，许多微框架在某些情况下使用了类似的思想。例如，Hobbit 和 charged 都将所有类方法视为助手:

```
require 'sinatra'

helpers do
def greeting name
"Howdy #{name}"
end
end

get '/:name' do
@message = greeting params[:name]
erb :hello
end

__END__
@@hello
<h1><%= @message %></h1>
```

## 穴居矮人

```
require 'hobbit'
require 'hobbit/contrib'

class App < Hobbit::Base
include Hobbit::Render

# all methods in this class are helpers!
def greeting(name)
"Howdy #{name}"
end

get '/:name' do
@message = greeting request.params[:name]
render 'hello', {}, layout: false
end
end

run App.new
```

views/hello.erb:

```
<h1><%= @message %></h1>
```

## NYNY

```
require 'nyny'
TEMPLATE = Tilt::ERBTemplate.new { DATA.read }

class App < NYNY::App
helpers do
def greeting name
"Howdy #{name}"
end
end

get '/:name' do
@message = greeting params[:name]
TEMPLATE.render(self)
end
end

App.run!

__END__
<h1><%= @message %></h1>
```

## 布鲁克林区

```
require "brooklyn"

class HelloApp < Brooklyn::App
helpers do
def greeting(name)
"Howdy #{name}"
end
end

get "/:name" do
"<h1>#{greeting(params[:name])}</h1>"
end
end
```

## 烧焦

```
require 'scorched'

class App < Scorched::Controller
get '/:name' do
@message = greeting(captures[:name])
render :hello
end

def greeting(name)
"Howdy #{name}"
end
end
```

views/hello.erb:

```
<h1><%= @message %></h1>
```

## 古巴

```
require "cuba"
require "hache"

def greeting(name)
"Howdy #{Hache.h(name)}"
end

Cuba.define do
on get, :name do |name|
res.write sprintf('<h1>%s</h1>', greeting(name))
end
end
```

## 南茜

```
require 'nancy'
require 'nancy/render'

module Helpers
def greeting name
"Howdy #{name}"
end
end

class App < Nancy::Base
include Nancy::Render
include Helpers

get '/:name' do
@message = greeting params['name']
render 'hello.erb'
end
end
```

你好。erb

```
<h1><%= @message %></h1>
```

## 代码片段 2–待办事项列表

第二个代码片段是一个使用数据库后端的 RESTful To Do List 应用程序的例子(我使用了 DataMapper，不可否认它有点旧了，但是它很好用！).这是我在辛纳屈的例子:

```
require 'sinatra'
require 'datamapper-core'

DataMapper.setup(:default, ENV['DATABASE_URL'] || "sqlite3://#{Dir.pwd}/development.db")
class Task
include DataMapper::Resource
property :id, Serial
property :name, String, :required => true
property :completed_at, DateTime
end

get '/tasks' do
@tasks = Task.all
erb :index

get '/task/:id' do
@task = Task.get(params[:id])
erb :task
end

post '/task' do

Task.create(:name => params[:name])
redirect '/tasks'

end

put '/task/:id' do
task = Task.get(params[:id])
task.completed_at = params[:completed] ? Time.now : nil task.name = (params[:name])

redirect '/tasks'

end

delete '/task/:id' do
Task.get(params[:id]).destroy
redirect '/tasks'
end
```

这是来自微框架的例子。有趣的是，有些作者使用了不同的数据库适配器。同样，他们中的许多人也使用额外的宝石或插件来实现 Sinatra 开箱即用的一些结果，这符合他们的极简主义野心。其中一些框架甚至有自己的“贡献”宝石，可以根据需要添加额外的功能(Sinatra 也是这样做的)。

## 穴居矮人

```
require 'hobbit'
require 'hobbit/contrib'
require 'datamapper-core'

DataMapper.setup(:default, ENV['DATABASE_URL'] || "sqlite3://#{Dir.pwd}/development.db")

class Task
include DataMapper::Resource
property :id, Serial
property :name, String, :required => true
property :completed_at, DateTime
end

class App < Hobbit::Base
include Hobbit::Render

use Rack::MethodOverride

get '/tasks' do
@tasks = Task.all
render 'index'
end

get '/task/:id' do
@task = Task.get(request.params[:id])
render 'task'
end

post '/task' do
Task.create(:name => request.params[:name])
redirect '/tasks'
end

put '/task/:id' do
task = Task.get(request.params[:id])
task.completed_at = request.params[:completed] ? Time.now : nil task.name = (request.params[:name])
response.redirect '/tasks'
end

delete '/task/:id' do
Task.get(request.params[:id]).destroy
response.redirect '/tasks'
end
end

run App.new
```

## NYNY

```
require 'nyny'
require 'data_mapper'

DataMapper.setup(:default, ENV['DATABASE_URL'] || "sqlite3://#{Dir.pwd}/development.db")
class Task
include DataMapper::Resource
property :id, Serial
property :name, String, :required => true
property :completed_at, DateTime
end

class App < NYNY::App
get '/tasks' do
@tasks = Task.all
render 'index.erb'
end

get '/task/:id' do
@task = Task.get(params[:id])
render 'index.erb'
end

post '/task' do
Task.create(:name => params[:name])
redirect '/tasks'
end

put '/task/:id' do
task = Task.get(params[:id])
task.completed_at = params[:completed] ? Time.now : nil
task.name = (params[:name])
task.save!
redirect '/tasks'
end

delete '/task/:id' do
Task.get(params[:id]).destroy
redirect '/tasks'
end
end

App.run!
```

## 布鲁克林区

Luis Lavena 表示，Brooklyn 不是为构建这样的应用程序而设计的，并建议使用 Hobbit。

## 烧焦

```
require 'scorched'
require 'sequel'

Sequel::Model.db = Sequel.sqlite("development.db")

class Tasks < Sequel::Model
db.create_table? :tasks do
primary_key :id
String :name
DateTime :completed_at
end
end

class App < Scorched::Controller
controller '/tasks' do
get '/' do
@tasks = Tasks.all
render :index
end

get '/:id' do
render :task, locals: {task: task}
end

post '/' do
id = Tasks.insert(:name => request.POST['name'])
redirect "/tasks/#{id}"
end

put '/:id' do
task.update(completed_at: (request.POST['completed'] ? Time.now : nil), name: request.POST['name'])
redirect "/tasks/#{captures[:id]}"
end

delete '/:id' do
task.delete
redirect "/tasks"
end

def task(id = captures[:id])
Tasks[id] || halt(404)
end
end
end
```

## 古巴

```
require "cuba"
require "cuba/contrib"
require "mote"
require "ohm"

# Plugins
Cuba.plugin Cuba::Mote
Cuba.plugin Cuba::TextHelpers

# Middlewares
Cuba.use Rack::MethodOverride

# Ohm is an object-hash mapper for Redis.
class Task < Ohm::Model
attribute :name
attribute :completed_at
end

Cuba.define do

# Here we match the path "tasks" no matter which HTTP method is
# used. Later on we will inspect the method to decide what to do.
on "tasks" do

# This matcher (a symbol) captures a segment in the requested
# path. In our case, it will match the id of the task.
on :id do |id|

# As the `on` blocks are closures, we can fetch the task here
# and use it in the subsequent blocks.
task = Task[id]

# This block will match only if we found a task.
on task do

# If the request method was a GET...
on get do
res.write view("task", task: task)
end

# If the request method was a DELETE...
on delete do
task.delete

res.redirect "/tasks"
end

# If the request method was a PUT...
on put do

# Check if we got the `completed` parameter.
on param(:completed) do
task.update(completed_at: Time.now)
res.redirect "/tasks"
end

# Check if we got the `name` parameter.
on param(:name) do |name|
task.update(name: name)
res.redirect "/tasks"
end

# If no block matched so far, it was a bad request.
on default do
res.status = 400
res.write "Bad request"
end
end
end

# If we didn't find a task, we return 404.
on default do
res.status = 404
res.write "Not Found"
end
end

# This matches "/" from the place the route is mounted. In this
# case, as this block is nested inside "tasks", it will match if
# nothing else comes after "/tasks/".
on root do

# If the request method was a GET...
on get do
res.write view("tasks", tasks: Task.all)
end

# If the request method was a POST...
on post do |name|
Task.create(name: req[:name])

res.redirect "/tasks"
end

# If no other block matched...
on default do
res.status = 404
res.write "Not Found"
end
end
end

# This matches "/".
on root do
res.write view("home")
end
end
```

## 南茜

```
require 'nancy'
require 'nancy/render'
require 'data_mapper'

DataMapper.setup(:default, ENV['DATABASE_URL'] || "sqlite3://#{Dir.pwd}/development.db")

class Task
include DataMapper::Resource
property :id, Serial
property :name, String, required: true
property :completed_at, DateTime
end

DataMapper.finalize

class TaskApp < Nancy::Base
include Nancy::Render
use Rack::MethodOverride
use Rack::Static, :urls => ["/javascripts", "/stylesheets"], root: 'public'

get '/' do
redirect '/tasks'
end

get '/tasks' do
@tasks = Task.all
render('views/layout.erb'){ render 'views/index.erb' }
end

get '/task/:id' do
@task = Task.get(params['id'])
render('views/layout.erb'){ render('views/task.erb', { task: @task }) }
end

post '/task' do
Task.create(name: params['task']['name'])
redirect '/tasks'
end

put '/task/:id' do
task = Task.get(params['id'])
task.completed_at = params['task']['completed'] ? Time.now : nil
task.name = params['task']['name'] if params['task']['name']
task.save
redirect '/tasks'
end

delete '/task/:id' do
Task.get(params['id']).destroy
redirect '/tasks'
end
end
```

## 还有呢！

在我写这篇文章的时候，我发现了更多值得研究的 Ruby 微框架——它们是:

*   杜松子酒
*   [贤治](https://github.com/kballenegger/kenji)
*   [格子](https://github.com/celluloid/lattice)
*   [葡萄](http://intridea.github.io/grape/)
*   [绉](https://github.com/crepe/crepe)
*   [露营](http://camping.io/)
*   [差点辛纳特拉](https://github.com/rkh/almost-sinatra)

## 这是所有的乡亲

以上是我对 Ruby microframworks 的旋风式介绍，我希望你会觉得有趣。你以前听说过或者使用过这些吗？你知道我错过了什么吗？

你有没有尝试过其中的一些？也许你可以试着用它们来重写一个项目？你喜欢他们使用的任何惯例的声音吗？随着 Sinatra 2.0 的出现，也许它可以借用其中的一些概念。

你认为 Ruby 需要这么多“框架”吗？或者你有没有受到启发，试着编写自己的 Ruby 微框架(或者你已经这样做了)？

像往常一样，在下面的评论中留下你的答案。

## 分享这篇文章