# 使用 Laravel 中间件中断应用程序

> 原文：<https://www.sitepoint.com/middleware-in-laravel-5/>

![Laravel Course Image of Rocket](img/05c98bcf4817745eea666584243c5eac.png)

在我们开始在 Laravel 中使用中间件之前，什么是中间件？一般来说，中间件是连接不同系统的软件。在 [Laravel](https://laravel.com/) 中，它是当今可用的最著名和最健壮的 PHP 框架之一，中间件为你提供了一种过滤 HTTP 请求并管理这些请求的方法。这允许您指定这些请求如何与您的应用程序交互。

### 为什么我需要 Laravel 的中间件？

在 Laravel 5 中使用中间件可以中断对应用程序的请求。但这很不透明，不是吗？这对你有什么帮助？如果你已经非常熟悉 Laravel，或者各种软件系统的交互，你可能已经知道这个答案了。如果没有，请容忍我们。

### 你需要 Laravel 的中间件来中断东西

Laravel 的中间件可以中断这些请求，然后执行各种操作，如控制访问和要求验证才能进入某些区域。因此，如果你想象你的应用程序，它只是一个应用程序，挂在外面，接受传入的 web 请求，提供你的视图，或 API 响应，或你交付的任何其他东西。但是您的应用程序如何管理这些请求呢？它是如何过滤掉它们的？它如何知道是否检查您的用户凭证，或者他们是否被授权做请求所要求做的事情？或者，您的应用程序如何根据请求的内容知道如何处理该请求呢？

答案当然是中间件。中间件可以用于与身份验证相关的事情，但也可以用于启动日志记录之类的事情，或者用于 API 的速率限制。中间件有许多用例，Laravel 的中间件允许您用一种优雅的方法来处理这些用例，而不是试图为每个实例拼凑一些东西。

### 工匠——Laravel 的主力

这真的是黑客的反义词。Laravel 的中间件非常容易使用，这在很大程度上是因为 Laravel 5 内置的命令行界面工具 [Artisan](//laravel.com/docs/5.3/artisan”) 。Artisan 提供了一种创建中间件的简单方法，然后可以对其进行编辑。您添加了您的逻辑，然后您必须配置它以供您认为合适的任何路由使用，很快，您现在就有了可以工作的中间件。

### 观看魔术

TL；博士？本视频将带您在 Laravel 5 应用程序中使用中间件，并向您展示它的一些实际用途，参考使用 [Laravel 5 的中间件文档](https://laravel.com/docs/5.3/middleware)。使用 Laravel 的中间件，您将能够轻松地控制应用程序的请求流！

本视频还将向您展示如何实现您根据路由构建的中间件，以便对特定路由的请求现在将使用您构建并运行的中间件，然后再对请求做任何进一步的处理。这很简单，但是很有效，Laravel 让这个过程变得非常容易操作。

### 还想了解更多关于 Laravel 5 的信息吗？

我们已经为你准备好了一个全面的[博客！前往 SitePoint 开始吧！本课程将为您提供对 Laravel 5 PHP 开发的坚实理解。它将为您提供从头开始安装 Laravel、使用 Laravel 工具和方法、分离 Laravel 应用程序的各个方面以及使用 Laravel 处理创建、读取、更新和删除(CRUD)系统的基本方面所需的指导。](https://www.sitepoint.com/premium/courses/laravel-5-2930)

你的导师， [Isaac Castillo](https://www.sitepoint.com/premium/users/IsaacCastillo) ，已经在设计和 web 应用程序开发领域工作了超过 15 年。他广泛使用 PHP、Laravel、WordPress、React Native 和其他技术，以前是 LAMP stack 编码训练营的讲师。整洁，是吧！

## 分享这篇文章