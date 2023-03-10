# 备选红宝石

> 原文：<https://www.sitepoint.com/altruby/>

不可否认，Ruby on Rails 在 web 开发界引起了一些轰动——你已经读过博客，也许还买过 t 恤，但是你知道 Ruby 作为一种语言也引起了一些关注吗？

到目前为止，您一直在使用由 Ruby 设计师 Yukihiro Matz Matsumoto 构建的原始 Matz Ruby 解释器(MRI)。这是默认安装在 Linux 和 OSX 上的标准解释器。然而，也有一些问题。

*   速度:按照设计，解释型语言会比编译型语言慢——解释器每次运行都需要解析和编译源代码。
*   集成:如果你想连接到一个 ActiveDirectory 服务器或 MSQL 服务器，你需要用 Ruby 编写的库。如果你试图在企业级上使用 Ruby，这可能会引起一些麻烦

因此，一些非常聪明的人正在开发不同版本的 Ruby 解释器来缓解这个问题。

## JRuby

JRuby 是 Java 中 Ruby 解释器的一个端口。这可能是最成熟的了，拥有几乎完整的库集，并且可以运行 Rails(非官方的)。除了能够访问所有的标准 Java 库，JRuby 还可以被编译成字节码(使用即时或提前编译)，这消除了 MRI 需要执行的解析和编译阶段。这使得 JRuby 在很多情况下比 MRI 更快。当运行 mongrel 的 JRuby 版本时，这种加速是显而易见的。

## 鲁宾纽斯

Rubinius 是一个试图为 Ruby 语言构建虚拟机的项目。Ruby 虚拟机和 Java 虚拟机的工作方式是一样的——代码被编译和优化成一个通用的字节码，然后由 VM 执行。这具有速度优势，消除了解析和编译阶段，同时仍然允许平台之间的可移植性。Rubinius 团队还为 Ruby 构建了最完整的单元测试集，许多其他项目都在使用它。

## 铁红宝石

IronRuby 是运行在微软的 Ruby 的一个端口。NET 动态语言运行时(DLR)平台。IronRuby 继承了 Ruby.NET 的遗志(使用公共运行时语言构建)，旨在将 Ruby 编译成。NET 中间语言(基本上与 Java 或 Rubinius 字节码一样)。就像 JRuby 允许 Ruby 访问 Java 类文件一样，IronRuby 也会允许 IronRuby 访问。NET 程序集。事实上因为。NET 是语言不可知的，你甚至可以用它来编写 ASP.NET 或 Silverlight，并与用 C#或 VB.NET 写的代码集成。IronRuby 仍处于早期阶段，主要是因为 DLR API 尚未完成，但不管怎样，它正在取得进展。

正如你所看到的，在 Ruby 的底层有很多事情要做。如果您感兴趣，您可以下载并运行所有这些系统——只是不要期望它们都能正常工作！

## 分享这篇文章