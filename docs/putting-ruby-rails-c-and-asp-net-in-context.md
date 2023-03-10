# 将 Ruby、Rails、C#和 ASP.NET 放在上下文中

> 原文：<https://www.sitepoint.com/putting-ruby-rails-c-and-asp-net-in-context/>

我注意到他们之间有相当多的误解。NET 开发人员解释什么是 Ruby 或 Rails，以及它们与他们在. NET 中所了解和喜爱的东西相比如何。下面的评论摘自我之前的两篇文章。NET 和[为什么一个. NET 开发者应该研究 Ruby 或者 Ruby on Rails](https://www.sitepoint.com/why-should-a-net-developer-look-into-ruby-or-ruby-on-rails/) 来说明我的观点:

“看了看露比，但没有谢谢。NET 仍然是我的出路。”

“…有没有 Ruby on Rails 的演示或示例可以与我用 ASP.NET 或 Silverlight 创建的 web 应用程序以及像 ASP.NET MVC 的 DevExpress 这样的组件相媲美？Ruby 里有类似的东西吗？”

而第一条评论将 Ruby 比作。NET，这是一种语言和整个开发平台之间的比较，第二条评论将基于 html 的应用程序与基于 Silverlight 的富客户端应用程序混合在一起，将第三方可视化控件带入等式中，并怀疑这样的控件在 Ruby(一种语言……)中是否可用。

这里有很多困惑，所以我希望在这篇文章中澄清这些误解。

# Ruby 是什么？

Ruby 是一种语言。就像 C#是一种语言一样。借用 Ruby language 网站上的一句话:

Ruby 是一种动态的、开源的编程语言，专注于简单性和高效性。它有一个优雅的语法，读起来很自然，写起来也很容易。”。

那么 Ruby 和 C#相比到底怎么样呢？你会在 Ruby 中找到对象，还有类、方法、继承等等。你还会发现函数式编程的元素；就像 C#通过匿名委托和 lambdas 获得一些那样。虽然 C#一直是一种静态的、强类型的语言，并最终获得了一些动态特性，但 Ruby 是一种完全动态的语言。

你可以期待更多的帖子来讨论 Ruby 和 C#的一些特性的比较。你可以从查看[开始。NET to Ruby: Classes](https://www.sitepoint.com/net-to-ruby-classes/) 发布在 RubySource 上。

## 露比是谁的？

Ruby 是一种开源语言，正因为如此，它的发展直接基于其社区的需求，而 C#属于微软，微软对语言的内容有最终决定权。

尽管如此，除了微软的 C#实现，还有一个由 [Mono](http://www.mono-project.com/Main_Page) 提供的开源跨平台实现。

同样，Ruby 也有一个实现，来自它的创造者， [Yukihiro Matsumoto，或“Matz”](http://en.wikipedia.org/wiki/Yukihiro_Matsumoto)，以及 [Rubinius](http://rubini.us/) 、 [JRuby](http://jruby.org/) (一个 Java 实现)，和 [IronRuby](http://ironruby.net/) (一个. NET 实现)。IronRuby 存在的事实应该是。NET 开发人员研究 Ruby，在构建时将它添加到他们的锦囊妙计中。NET 应用程序。

# 什么是 Ruby on Rails？

那么什么是 Ruby on Rails 呢？一种语言？一个框架？一个平台？火车运输大红宝石的公司？我们开始吧:

*   Ruby on Rails(或 Rails，或简称 RoR)是一个遵循模型-视图-控制器(MVC)架构模式的 web 应用程序框架。因此，它比较像 ASP.NET 的 MVC 框架；
*   就像 ASP.NET MVC 框架建立在 ASP.NET 之上，Rails 建立在 [Rack](http://en.wikipedia.org/wiki/Rack_(web_server_interface) 之上一样；
*   就像有其他的 web 应用框架建立在 ASP.NET 之上(比如 WebForms，或者 [FubuMVC](http://mvc.fubu-project.org/) )，也有其他基于 Rack 的框架(比如[Sinatra](http://www.sinatrarb.com/))；
*   就像 ASP.NET 是用 C#写的，Rails 是用 Ruby 写的；
*   虽然“大多数”基于任何 ASP.NET 风格的 web 应用程序都是用 C#编写的，但是所有基于任何 Rack 风格的应用程序都是用 Ruby 编写的。

## 谁拥有 Ruby on Rails？

Rails 是一个开源框架，很像 Ruby，它的开发是由社区驱动的。基于用户面临的真实世界的需求，新的特性被添加到框架中。

# 我可以用 Rails 构建什么样的应用程序？

既然人们会问诸如*“我能用 Rails 构建类似 Silverlight 的应用程序吗？”*，我认为需要澄清。仅仅说“*你可以构建 **web 应用*** ”已经不够了。它是一个 web 应用程序，因为它是在 web 上托管的吗？还是因为它*运行在浏览器*里？还是因为是*基于 html 的*？还是因为它访问来自 web 的*数据？*

典型的 Rails 应用程序具有基于 html 的前端部分。因此，它在浏览器中运行。网页由网络服务器中的控制器提供。这些控制器加载填充了数据的*模型*，这些数据来自网络服务器可以访问的某个地方(可能是同一个盒子里的数据库，也可能是地球上某个地方的*网络服务*)。一旦模型建立起来，控制器选择*视图*模板，该模板生成要发送到浏览器的 html。

另一个典型的 Rails 应用程序可能拥有上一段中提到的所有东西，除了 html 前端。因此应用程序提供了一个 API(通常是 RESTful API ),其他应用程序可以通过 web 调用它。为了。这有点像创建 ASP.NET 网络服务，或 WCF 服务。在这种情况下，使用这个 API 的其他应用程序可以是任何类型的；例如，iPhone 应用、Silverlight 或 WPF 应用、ASP.NET 应用等。

用 Rails 创建一个“客户端专用”的应用程序是不可能的，比如那些用 WPF、Silverlight 等创建的应用程序。不过，你可以混合搭配不同的技术。例如，我目前从事的项目大部分是基于 Rails 构建的，但也有一部分是用 Silverlight 编写的，它使用数据并将数据推送到 Rails 中创建的 RESTful API。应用程序的 Rails 部分还使用了由第三方供应商用 Java 创建的 API，该供应商也使用了我的 API 来发送一些数据。该项目的另一部分将包括 iOS (iPad，iPhone…)和 Android 的应用程序，该应用程序还将使用 Rails 应用程序提供的 API。

# 有可重用的组件吗？

。NET 开发人员习惯于使用提供各种特定功能的库，如加密、成像、报告等。类似的东西在 Ruby on Rails 中也有，它们通常被称为“gems”。有很多这样的库:大量的库为您提供了大量的功能，您可以将这些功能添加到您的应用程序中(Ruby on Rails 本身就是一颗宝石！)

最近，。NET 开发人员也已经了解了库包管理器 [NuGet](http://nuget.org/) ，它有助于将外部库添加到项目中，引入它们的依赖项，调整项目的设置，创建特定的代码生成器等等。这种东西在 Ruby 世界已经存在好几年了，它被称为 [RubyGems](http://rubygems.org/) 。

# “可视化”组件呢？

。NET 开发者喜欢他们的第三方可视化控件！这些在 WinForms、WebForms、WPF 和 Silverlight 中大量存在。ASP.NET MVC 的选择少得多。对于 Rails，我还没有遇到过这样的事情。然而，就我所见，Rails 开发人员非常乐于简单地使用 CSS 和 jQuery 来创建令人敬畏的网站(这是一些。NET 开发人员也在他们的 ASP.NET MVC 应用程序中使用)。

总有开发人员希望得到“ubber 第三方网格，可以显示数据库中的大量行”。说实话，我以前也是这样。不再是了。如果我需要在客户端的网格中处理大量的数据，这意味着我做错了。我在我的[上说的更多“你被删了！”](http://lassala.net/2010/11/18/you-have-been-deleted/)岗位。

# Rails 跨平台吗？

简单的回答是肯定的。下面是一个很长的答案:

*   任何机器都可以访问 Rails 应用程序:苹果电脑、个人电脑、智能设备…
*   通常，Linux 服务器*托管*这些应用程序，即使它们可以托管在其他操作系统中；
*   这些应用程序可以在任何平台上开发，尽管 Mac 和 Linux 是目前最流行的环境。我已经在 Windows 上做了几个月的 Rails 开发，尽管在某种程度上效果不错，但某些东西要么不如在 Mac 上运行得好，要么就是太慢了(比如在运行自动化测试时)。

# 总结

Ruby on Rails 只是一个 web 应用程序框架，类似于 ASP.NET MVC 框架，只是 Rails 已经存在了更长时间。我希望这篇文章有助于澄清 Ruby on Rails 的一些细节与这些事情的关系。NET 开发人员习惯于。

## 分享这篇文章