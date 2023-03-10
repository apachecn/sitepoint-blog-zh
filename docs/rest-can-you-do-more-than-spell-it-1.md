# 休息——除了拼写，你还能做什么？第一部分

> 原文：<https://www.sitepoint.com/rest-can-you-do-more-than-spell-it-1/>

几千年前，当我们第一次开始构建网页时，事情非常简单。你可以在页面上放一些文本，甚至是一张图片，差不多就是这样。但是今天是一场完全不同的比赛。取代静态页面的是我们所依赖的动态应用程序。因此，如何设计这些应用程序来进行通信变得非常重要。在这个系列中，我将向您介绍 REST 架构风格。在本文中，我将帮助您准确理解它是什么，稍后我将向您展示如何在 PHP 环境中实现它。

## 什么是休息？

REST 是一组原则，定义了服务器和客户机如何以简单、直接和健壮的方式相互通信(以及与外部资源通信)。尽管您经常看到“REST”和“架构”这两个词在一起，但是 REST 并不是一个特定的架构。我喜欢 M. Elkstein 博士在 [Learn REST: A Tutorial](http://rest.elkstein.org/) 中所说的话，他称之为“架构风格”。REST 为如何构建事物提供了明确的指导，但并不坚持使用特定的构建模块以特定的方式构建事物。

在许多参考资料中，你会看到 REST 和 SOAP 都被作为竞争对手提及。这不是真的。SOAP 实际上是一种协议，而不是一种架构风格。REST 可以与之相比的是 SOA 和 RPC。这三个都是 web 服务风格的例子，每个都有自己的概念焦点。RPC 关注操作，SOA 关注消息，REST 关注资源。

REST 背后的思想已经存在一段时间了，最早出现在 HTTP 协议的开发者之一 Roy Fielding 的博士论文中。事实上，REST 对 HTTP 的影响，以及对互联网本身的影响是显而易见的。但是 REST 不是一个标准或协议；没有 W3C 文档来概述它应该是什么，也没有版本号来确定它的日期。如果你真的休息，你肯定会以某种方式结束做事，但这并不等于说这是一种标准。

REST 代表*具象状态转移*，虽然我第一次看到它时，这对我来说毫无意义，但 Fielding 对它的意义有非常明确的想法。

*   代表性指的是这样一个事实，当你通过网络访问某个东西时，返回给你的是这个对象的一个表示，而不是实际的对象本身。如果您出去获取一个地址列表，您可能会得到一个 XML 数据流，而不是存储地址的实际数据库。
*   状态指的是不应该在服务器上保存任何会话状态。来自客户端的每个请求都应该包含所有必要的信息，以便服务器理解请求的上下文，而无需引用以前的事务。所有状态都由客户端维护。
*   传输指的是数据如何通过网络在客户端和服务器之间共享。

## 休息原则

那么如果休息是一套原则，那么这些原则是什么呢？

**将一切都标识为 URI**–REST 中的一切都是某种资源的表示，可以通过唯一的 URI(统一资源标识符)来标识。这就是网站本身的设计方式，它确保了一种简单性，如果资源被赋予各种不同的名称，这种简单性是不存在的。

作为应用程序状态引擎的超媒体——web 的一个优点是能够从一个页面链接到另一个页面，那么为什么不用它作为所有状态转移的基础呢？客户端只能通过资源内的操作(如超链接)在 web 应用程序中进行转换。

请求是无状态的–REST 的核心是无状态的概念。也就是说，服务器处理的每个请求都可以在不了解任何先前请求的情况下完成。换句话说，客户机在请求中提供服务器完成请求所需的所有必要信息。

**标准协议用法**–REST 中没有任何东西要求它使用 HTTP。然而，当你看到 REST 系统时，大多数时候它是建立在 HTTP 之上的。原因是 HTTP 使用四个基本操作(`GET`、`PUT`、`POST`和`DELETE`)来完成所有操作，而 REST 倾向于一个小型的标准化操作集。

总的来说，REST 鼓励简单和标准化，试图让已经存在的协议特性来完成大部分繁重的工作，并消除用程序员编写的操作重新发明这些功能的需要。尽管今天完成的许多 web 应用程序开发都采用 RPC 或 SOA 方法，但人们对使用 rest 提倡的更简单但更健壮的风格越来越感兴趣。

## 上传与上传

正如我上面提到的，REST 不是一个真正的架构，它不会强迫你使用某些工具或协议。但是大多数实现都使用 HTTP，主要是因为它简单。每个协议中都定义了操作，当涉及数据时，这些操作执行所需的 CRUD(创建、检索、更新和删除)工作。HTTP 使用四种基本操作`GET`、`PUT`、`POST`和`DELETE`来完成它的 CRUD 工作。

如您所料，`GET`为您检索数据，`DELETE`删除数据。这就把我们带到了`PUT`和`POST`。讨论其中任何一个，尤其是`POST`有点像讨论宗教或政治；你一定会让某人不高兴。但是现在…

有些人倾向于将`PUT`与创建联系起来，将`POST`与更新联系起来。但老实说，我认为这是一种过于简单化的说法，有点像是说，原力的阴暗面只是你在有点不高兴的一天所经历的。实际上，这两种方法有很大的不同。

首先，让我们从“幂等”这个术语开始。不要把这与一种类似名称的非常严重的男性疾病相混淆，这种疾病只能用一些药物来治疗，这些药物在东欧以外都是非法的。幂等意味着如果多次应用该运算，将得到与刚刚应用一次相同的结果。有什么区别？如果你在网上购物，并且过于频繁地按回车键，那么这就是购买一个真正的多莉·帕顿小雕像或五个之间的区别。原来，`GET`、`PUT`、`DELETE`都是幂等的。`POST`不是。

您可以多次执行同一个操作而不会搞砸一切的唯一方法是，当您请求该操作时，传输与该资源相关的所有数据。因此，这就是`PUT`所做的。服务器接收所有可用的数据并完全重写资源。资源是否已经存在并不重要；您将完全替换它，因此创建和更新被视为相同。

另一方面，`POST`不是幂等的。多做几次，就会发生多件事。例如，如果您正在创建一个新的用户登录，并且您使用了一个`POST`，那么您将在每次点击 Enter 时创建一个新的帐户。`POST`请求只需要与资源相关的部分数据，而不是全部数据。事实上，如果您发送的数据代表了对已有资源的补充，比如对博客帖子添加评论或对故障单进行更新，这是非常好的。你听懂了吗，滑头？

第二，由于 REST 是一种面向资源的架构风格(与 SOA 的面向消息和 RPC 的面向操作相反)，让我们从资源发生了什么的角度来看这个问题。幂等运算`PUT`影响请求中指定的 URI。也就是说，它在 URI 更新由客户端提供的资源。`POST`影响由服务器创建和命名的 URI。该资源使用从客户端传递来的数据进行更新，但是该资源与名为 URI 的客户端是独立且不同的。

当你和自己争论`PUT`和`POST`时，一个很好的问题是谁应该命名 URI，客户端还是服务器？另一种说法是，您是否希望保留对 URI 的控制，或者将其出租，让服务器创建相关的 URI。可以用`POST example.com/blog`创建一个博客帖子。服务器将创建 ID 为 42 的条目，该条目将作为`example.com/blog/42`可用。或者，如果资源不存在，可以用`PUT example.com/blog/42`创建一篇博文(在这种情况下，它将被替换)。资源`example.com/blog/42`的命名由服务器用`POST`决定，由客户端用`PUT`决定。

## 摘要

REST 不是一种固定的做事方式，它是一种基于几个简单原则的思想状态:将所有东西都视为由 URI 标识的资源，使用超链接从一个资源移动到另一个资源，将所有东西都视为无状态的，以及使用具有通用操作的标准协议。REST 相对于 SOA 或 RPC 的优势在于简单性和速度(因为在您必须构建的内容及其处理过程中开销更少)，同时仍然允许您构建一个可靠且健壮的系统。大多数 REST 应用程序使用 HTTP(因为它很简单),所以理解`PUT`和`POST`方法之间的区别很重要。

在这个传奇的下一章中，我们的英雄和他勇敢的伙伴将向你展示 PHP 是如何适应这一切的。PHP 如何与 REST 一起使用，特别是它如何与 HTTP 接口。我不知道你怎么样，但是我很兴奋！我迫不及待地想了解这项令人兴奋的技术。

图片 via[little Sam](http://www.shutterstock.com/gallery-351511p1.html)/[Shutterstock](http://www.shutterstock.com/)

## 分享这篇文章