# 让我们从最底层开始…

> 原文：<https://www.sitepoint.com/lets-start-at-the-bottom/>

正如我在[中提到的](https://www.sitepoint.com/blog/)，Java Web 技术多年来一直是分层构建的。今天，**至少有十几种实用的、经过验证的方法**来用 Java 构建网站。

我在这篇博客中的第一个目标是**带你对这些选项**进行一次盛大的旅行，让你了解每一个选项——它的优点和缺点——这样你就可以开始对学习什么做出明智的决定。

因此，我们将从查看 [Servlets](http://java.sun.com/products/servlet/) 开始，然后我们将继续查看 [JSP](http://java.sun.com/products/jsp/) ，在我们检查 [Model 2](http://www.javaworld.com/javaworld/jw-12-1999/jw-12-ssj-jspmvc.html) 时将二者结合起来，将此扩展到讨论 MVC 框架，如 [Struts](http://struts.apache.org/) 和 [Spring](http://www.springframework.org/) ，甚至探索各种视图技术，如 [Velocity](http://jakarta.apache.org/velocity/) 、 [JavaServer Faces](http://java.sun.com/j2ee/javaserverfaces/) 和 [XMLC](http://xmlc.objectweb.org/) 。**这绝不是一个详尽的列表**——有很多值得探索的地方！

当我们这样做的时候，我预测你会注意到一个有趣的趋势。大多数 Web 开发平台开始时都相当容易学习和使用，但是当你试图将更大、更实用和复杂的项目硬塞进去时，它们往往会变得越来越复杂。我想到了 PHP:尽管我非常喜欢这种语言的易用性，但你可以花几个月的时间去阅读人们关于如何最好地构建大型项目的想法，却仍然没有提出一个清晰的解决方案。

Java 的趋势是不同的:有很多东西需要提前学习，起初简单的任务看起来似乎是愚蠢的劳动密集型的。但是随着你对复杂的层次越来越适应，它们开始为你工作，你曾经觉得可怕的 T2 项目将开始变得容易。

为了开始我们的旅程，我们将从一个简单的 Web 应用程序开始:一个数据库驱动的待办事项列表。我们将使用我们探索的每一项技术构建同样的应用程序，我将为每个版本提供可下载的代码。因为这是一个简单的项目，它将允许你关注技术之间的差异，它们的优点和缺点，哪些功能容易实现，哪些很难实现。

这个应用程序的每个实例都将依赖于同一个 [MySQL](http://www.mysql.com/) 数据库，所以让我们从这个数据库开始。如果你打算继续下去，现在是时候**确保你的开发机器上有一个健康的 MySQL 安装**。如果你真的是数据库或服务器端 Web 开发的新手，你可以在我的书的前两章中找到关于 MySQL 的可靠介绍和完整的安装说明。这些章节可以在 sitepoint.com 上免费获得:[第一章](https://www.sitepoint.com/blog/)和[第二章](https://www.sitepoint.com/blog/)。

MySQL 就绪后，创建一个新数据库(我称之为“todo”)，其中包含以下单个表:

`CREATE TABLE todo (
todoid INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
todo VARCHAR(255) NOT NULL
)`

明天，我将展示与这个简单数据库接口的 Java 类。

## 分享这篇文章