# 与 Laurent Sansonetti 一起了解 RubyMotion

> 原文：<https://www.sitepoint.com/getting-to-know-rubymotion-with-laurent-sansonetti/>

![](img/7fb50e4252589214b7db43be3ec03daf.png)

洛朗·桑索尼蒂创造了 [RubyMotion](http://www.rubymotion.com)

就在去年四月，Laurent Sansonetti 用 RubyMotion 抓住了整个 Ruby 社区的想象力。Ruby 开发者第一次能够使用我们都熟悉和喜爱的语言直接为 iOS 移动平台编写应用程序。现在不再需要学习陈旧、冗长的 Objective-C 语言，或者更糟糕的是，不再需要学习在 XCode IDE 中导航令人困惑的无数窗口和对话框。我们可以把 Ruby 的魔力带到 iOS 中，使用我们最喜欢的命令行工具。

本月早些时候，当 Laurent 同意为 RubySource 采访我时，我非常激动。我可以直接从 RubyMotion 的发明者那里学到更多关于它的知识。因为我有很多问题，我把采访分成了两个帖子。首先今天我要问 Laurent 一些基本问题:RubyMotion 到底是什么？它是做什么的？应该怎么用？为 iOS 编写 Ruby 和编写一个“普通的”Ruby 应用程序有什么不同吗？

明天我会贴出[采访](https://www.sitepoint.com/laurent-sansonetti-on-rubymotion-internals/)的后半部分，会更技术性一点:RubyMotion 内部是如何工作的？Laurent 如何使用 LLVM 框架将 Ruby 代码编译成静态可执行文件？它与 MacRuby 和 Rubinius 的工作方式有何不同？我喜欢学习 Ruby 的内部结构，RubyMotion 无疑是 Ruby 的一个独特而迷人的实现。

## # INSPECT——首次专门讨论 RubyMotion 的会议

![](img/7d09dd309fbd8a43018e3d6801e0606a.png)

洛朗将于今年三月在他的家乡举办第一次关于 RubyMotion 的会议

问:你能先给我们介绍一下即将到来的 [RubyMotion #INSPECT2013](http://www.rubymotion.com/conference/) 大会吗？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

RubyMotion 发布 6 个月后，我有了做一个小活动的想法，一个为期一天的关于 RubyMotion 的会议。然后我就开始忙别的事情了。我把这个放在一边，最近我来自 Phusion 的朋友邀请我做了一个演讲，非常棒，他们在阿姆斯特丹举办了一个名为 [BubbleConf](http://www.bubbleconf.com/) 的会议，专门讨论创业。我在推出 RubyMotion(此处[可用](https://vimeo.com/53607381))之前谈过恐惧和沮丧。Ninh Bui，一个非常好的朋友，实际上组织了会议；当然，他得到了一些帮助。这是件大事。然后我就嫉妒了；我说:“我也想办自己的发布会。”

问:听起来工作量很大。

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

是的，这不容易。同时，我不像宁那样雄心勃勃。宁做了一场真正的大秀；他租了一个很旧的场地，阿姆斯特丹一个能容纳 300 人的很棒的电影院。我的简单多了；我只要 150 个人。最初我想在美国做，因为大多数 RubyMotion 用户都在那里。但后来我告诉自己，第一版应该很特别，于是我决定在比利时出版。

问:为什么会这样？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

第一，我组织起来比较容易。我想把第一个做得越小越好，做一些亲密的事情，人们可以真正地互相交谈，可以真正地一起黑客攻击。我这样做不是为了钱，我认为第一次我们实际上会赔钱。这一切都是为了开心和推广 RubyMotion。仅仅召开一次会议就是社区健康的一个很好的标志。我们还将为参加会议的人举办一些活动:参观巧克力工厂，我们还将参观一家酿酒厂，那里生产非常典型的布鲁塞尔啤酒。

我们实际上有一个很长的发言者名单。但同时这些都是很短的谈话，30 分钟，我们之间有很多空间。我还想要一个单声道——只有一套扬声器。我们的演讲者包括来自 RubyMotion 社区的人们，著名的人物如尼克·夸兰托(37signals)和 T2·马特·汤普森(T3)(Heroku)，但也有一些不太出名的人，他们在 RubyMotion 中做出了惊人的事情。所以我认为这会很有趣。

## RubyMotion 是如何起步的

问:你以前在苹果公司工作，对吗？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

是的，七年了。MacRuby 是我在苹果的最后一个项目。它是开源的，这在苹果很少见。这还算成功。有一点很明显，管理层对赞助 MacRuby 不再感兴趣，所以我必须做出决定:我可以留在苹果，从事其他工作，或者我可以离开公司，加入另一家公司，或者创办自己的公司。我想了很多，意识到我有一个很好的机会去做自己的公司。

![](img/319fdeab071c83d564757f1cad7b3e36.png)

[左师藤田](https://twitter.com/watson1978)又名“沃森”

我的孩子很小，四个月大。那时我们住在加利福尼亚，但我们想回欧洲。所有的拼图实际上都在融化，这一切都让我有理由开始创业。

问:你和其他人一起开发 RubyMotion 吗？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

很长一段时间只有我一个人，但是四个月前我雇佣了左师·藤田；他的昵称是“华生”他在 MacRuby 社区很受欢迎。当我开始使用 RubyMotion 时，我真的不再使用 MacRuby 了。沃森带走了项目的一切；他是该项目的最大发起人。今年我还想雇佣另外两个人。

## RubyMotion 是什么？

问:什么是 RubyMotion？你是怎么开始的？这个想法是从哪里来的？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)![](img/d7be000b97f189ca6b56efbd456088a1.png)

克莱·奥尔索普最近出版了一本关于 RubyMotion 的书

RubyMotion 是一个用 Ruby 进行 iOS 开发的工具链。我所说的工具链是指一组不同的组件。首先，RubyMotion 是 Ruby for iOS 的一个实现。它是 Ruby 语言在 Objective-C 运行时之上和 iOS 核心框架之上的重新实现。这意味着在 RubyMotion 中，对象模型实际上是使用 Objective-C 运行时实现的。所以在 RubyMotion 中，当你创建类、对象或方法时，你实际上是在使用 Objective-C 对象、类或方法的相同运行时实现。Ruby 和 Objective-C 语言之间没有桥梁。在 RubyMotion 中，人们实际上可以访问完整的 iOS APIs 集，这些 API 是用 Objective-C 原生实现的，不需要使用任何类型的中间件。

![](img/f554403c8c377a6e9656b806dd3777d1.png)

Ruby 1.9、JRuby 和 Rubinius 都使用虚拟机运行你的
代码

问:它们都是同一个工作系统的一部分？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

没错。Objective-C 运行时实际上是一个非常小的 C 库，人们可以使用，这就是我们所做的。RubyMotion 应用程序调用相同的运行时 API，就好像该应用程序是用 Objective-C 编写的一样。此外，Ruby 的这种实现使用 iOS 的核心框架来实现一些内置的类。

例如，RubyMotion 中的 String 类是作为基础框架中 NSMutableString 的子类实现的。从 Ruby 用户的角度来看，变化很少。在幕后，它实际上使用了与 Objective-C 字符串相同的行为。这意味着如果你在 Ruby 中创建一个字符串，然后你把这个字符串传递给一个 Objective-C API，这个 API 需要一个字符串，没有转换；我们只是按原样传递对象。这就是我们所说的“统一运行时”我们对对象模型使用相同的 Objective-C 运行时，并使用核心框架来构建类。

![](img/855aead9944baa74be125f2fa5a5dc20.png)

然而，RubyMotion 将您的代码静态编译成运行在目标设备上的本机语言

问:你必须编写对 Ruby 方法的支持，比如“upcase”，这不是 Objective-C 的一部分？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

是的。RubyMotion 实际上是 MacRuby 的一种风格，是我大约 5、6 年前在苹果公司创建的。那个设计来自 MacRuby。

问:RubyMotion 工具链中还有什么？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

RubyMotion 也有一个静态编译器。这会马上将 Ruby 编译成优化的机器码。它与我们编写的 Ruby 的实现相关联。这就是 RubyMotion 的意义所在——它是一个运行时，一个 Ruby 的实现，也是一个编译器。

![](img/6cccb1b4d1955eff85e3752214c04ca6.png)

RubyMotion 还包括一个运行时组件，它实现了 Objective-C 中不存在的
Ruby 特性

问:所以设备上运行的是 Objective-C 运行时，Ruby 运行时，就这样？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

对，就是这样。我们为对象模型使用 Objective-C 运行时，然后我们有这个 Ruby 实现运行时，我们静态地链接它，大约 1MB。这包含所有现有的类，如字符串、数组、散列等。它还实现了 Objective-C 中不存在的所有 Ruby 语言特性。我们必须自己实现 mixins。同样，在 Objective-C 中，你不能动态地为一个类设置实例变量，所以我们也必须解决这个问题。

## RubyMotion 与 MRI

问:RubyMotion 基于哪个版本的 Ruby？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

我们实际上遵循的是 Ruby 1.9。RubyMotion 是 MacRuby 的一个分支，MacRuby 在很长一段时间内都试图遵循 [RubySpec](http://rubyspec.org) 项目。RubySpec 是一组可执行的 Ruby 规范。我认为它是从 [Rubinius](http://rubini.us) 项目中提取的。MacRuby 试图遵循这一点，以确保我们遵守 Ruby 的规范。当时我觉得 RubySpec 的最新版本是 1.9.1。然后我们把 MacRuby 换成了 RubyMotion，从那以后我们再也没有关注过 RubySpec。

在 RubyMotion 中，我们实际上删除了 Ruby 的一些特性，因为它是静态编译的，而且 Ruby 的某些特性我们不能公开。

问:如果我是一名 Ruby 开发人员，如果我每天都写 Ruby 并开始使用 RubyMotion，我会注意到什么不同？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

我认为最大的区别也是唯一的区别是在 RubyMotion 中没有“require”语句。

问:这是什么意思？如何包含其他库？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

因为 RubyMotion 是静态编译的，所以构建系统必须在构建时提前知道项目的所有文件。它必须提前构建一个可执行文件。所以我们不能只在运行时需要文件。

问:这类似于 C 程序使用 includes，有一个 Makefile，诸如此类的东西？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

没错。现在的构建系统有点智能。当你创建一个 RubyMotion 项目时，你有一个名为“app”的目录。在应用程序目录中，您可以创建。rb 文件；每个类有一个文件是组织项目的好方法。然后构建系统会将所有这些。rb 文件并尝试检测它们之间的依赖关系。例如，在一个文件中创建一个 Foo 类，然后在另一个文件中创建一个继承自 Foo 的 Bar 类。构建系统会检测到这一点，并在编译第二个文件之前编译第一个文件。在运行时，当程序启动时，Foo 类在 Bar 类之前创建。

因为这个特性，人们通常不需要要求文件。

问:…它只是自动发生的？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

我们实际上可以有一个 require 方法作为编译器提示。但是我不喜欢这个想法，因为 Ruby 中的 require 是内核类的一个方法；不是语言关键词。因为它是一个方法，你可以传递一个变量给它。它不需要是立即类型。可以需要 X，X 指向一个之前已经定义好的字符串。这意味着编译器必须处理这些边缘情况，这听起来不是一个好主意。

所以现在构建系统将会解析代码并尝试为您设置依赖关系。RubyMotion 中的 Require 会引发异常。这就是传统 Ruby 和 RubyMotion 最大的区别。除此之外，差异相当小。

第二个区别是，在 RubyMotion 中，字符串的“eval”方法不起作用。支持 eval 意味着我们必须为 Ruby 提供一个解释器，一个应用程序内部的解释器。关于苹果 iOS 的指导方针，这一点并不完全清楚。

问:他们不允许你这么做？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

你实际上可以提供一个解释器，但是你不能下载代码。我只是担心，如果我们在 RubyMotion 中发布一个解释器，苹果会停止批准 RubyMotion 应用程序。

问:所以通过删除 eval，你可以告诉他们你没有这样做…

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

还有 iOS 的硬件，尤其是老的，没有那么快。实际上，他们以前很慢。在运行时解释代码并不是一个好主意。

问:相关的方法也是无效的吗，比如 instance_eval，class_eval 之类的？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

这些都在起作用——其他一切都应该在起作用。你可以调用所有元编程的东西。

问:那么，这是仅有的两个区别吗？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

大概就是这样；除了一些其他的小差异。这里有一个列表:

*   由于构建系统的原因，不支持 Require，

*   字符串的求值不起作用，

*   Proc #绑定已被移除，并且

*   禁用对 Fixnum 和 Float 运算符的重写。

[作者注:Laurent 和我在这次采访的第二部分中讨论了 Proc #绑定和操作符重写。]

## RubyMotion 为什么不开源？

问:RubyMotion 是开源的吗？还是专有？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

专有组件是运行时和编译器。构建系统是用 Ruby 编写的，并且是开源的，这是一件很棒的事情，因为很多人喜欢破解构建系统。

问:所以你在 RubyMotion 中为客户提供的真正特殊技术是编译器？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

是的，编译器和 Ruby 实现(运行时)。Ruby 实现是闭源的。我一直在努力思考这个问题:我应该开源所有的东西，还是应该开源其中的一部分。

为了使这个项目可行，我需要钱。如果它是免费的，开源的，我需要找到赞助商。我不太喜欢赞助商，因为他们可以随时让你离开，然后你需要找到其他赞助商来维持这个项目，或者你需要在一旁做另一种工作。然后我也意识到，如果 RubyMotion 是免费和开源的，那么这并不意味着这个项目实际上会增长得更快。MacRuby 从一开始就是开源的，我认为我们只有四个人在六年多的时间里为运行时或者编译器贡献代码。

我担心的是，如果 RubyMotion 是开源的，我实际上不确定它是否有帮助，因为 Ruby 程序员大多使用 RubyMotion。而且 Ruby 程序员对 C 编译器和虚拟机也不是很熟悉。

问:这不像 Rails，它们可以轻松地修复东西或添加新功能。没有人会进入你的编译器并在那里修改一些东西。

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

没错。所以在 RubyMotion 上，我尝试开源所有 Ruby 的东西，这很好，因为我们有很多补丁。我可以看到我们实际上有更多的拉请求，所以我应该检查它们。我很懒。

问:不好意思！我占用了你所有的时间…

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

没关系。

所以我认为这是一个很大的妥协；我们开源，通过这种方式，我们要求用户提供资金，这样我们就可以围绕它建立业务。我也不想为这个创业公司获得投资。我的目标是经营一家完全自举的公司，没有外部投资者。

问:那太棒了。

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

我认为这是应该走的路，因为我希望产品能够存活多年，一旦你投资了，你就不再真正控制公司，因为大多数投资者希望他们的投资在 3 到 5 年内得到回报。

问:这样你可以按照自己的时间表做你想做的事情。

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

没错。对我来说，唯一的办法就是实际收费。

## 下一步是什么？

问:RubyMotion 的下一步是什么？

![](img/3a7939f4ec9989e840d52a5cade96d8e.png)

现在很难，因为 RubyMotion 发展得太快了。我们发布才 8 个月。我们现在有很多不同的选择。我们可以为这个平台做很多事情，很多重要的功能。与此同时，我们的人力资源有限。所以我们必须做出决定。

我们可能要做的是改进工具链。目前的工具链完全基于命令行，它适用于大多数用户。但同时有些人真的很喜欢 ide，我们一直在和 JetBrains 合作。RubyMine 有很大的 RubyMotion 支持；你可以在 RubyMine 里面调试 RubyMotion 代码，这很棒。我们希望继续与他们合作，这样我们就可以为 RubyMotion 用户编写一个 IDE。

很多用户也一直要求将 RubyMotion 移植到其他平台上。

## 敬请期待！

正如我在上面提到的，请关注明天 RubySource 的[第二部分采访。Laurent 和我将探索 RubyMotion 内部实际上是如何工作的。](https://www.sitepoint.com/laurent-sansonetti-on-rubymotion-internals/)

## 分享这篇文章