# 用 CodeIgniter 探索面向方面编程，第 1 部分

> 原文：<https://www.sitepoint.com/explore-aspect-oriented-programming-with-codeigniter-1/>

你以前听说过面向方面编程(AOP)吗？这是一个在开发企业级系统中广泛使用的概念，尽管它在 PHP 中还没有太多的用途。我打算以这篇文章为契机，向 PHP 开发人员介绍 AOP。

本教程将作为一个由 3 部分组成的系列提供给你。在这一部分，我将解释 AOP 的概念。在第 2 部分中，我将向您展示 AOP 的实际用途和创建 AOP 规则结构。最后，我将在第 3 部分展示如何使用 CodeIgniter 集成 AOP 功能。

## 什么是 AOP？

在应用程序开发中，我们经常会发现需要在代码的多个地方使用的功能，但这些功能实际上与业务逻辑并不相关。在执行特定任务之前检查身份验证以确保用户已登录就是一个很好的例子。这种任务被称为跨领域问题。让我们看看维基百科给出的[对横切关注点的定义:](http://en.wikipedia.org/wiki/Cross-cutting_concern "Cross-cutting_concern - Wikipedia, the free encyclopedia")

> 在计算机科学中，横切关注点是程序中影响其他关注点的方面。在设计和实现中，这些问题通常无法从系统的其余部分中清晰地分解出来，并且可能导致分散(代码重复)、缠结(系统之间的显著依赖性)，或者两者兼而有之。

现在您对横切关注点有了一个基本的概念，让我们看看它们在代码中是什么样子的。

考虑一下你是 SitePoint 这样的博客的编辑的情况。您需要登录才能创建帖子、批准帖子、编辑帖子等。如果您没有登录，应该会被定向到登录屏幕。为了确保这种行为，代码必须在上面提到的每个任务之前验证身份验证。

```
<?php
class BlogPost extends CI_Controller
{
    public function createPost() {
        if (!Authentication::checkAuthentication()) {
            // redirect to login
        }
        else {
            // proceed
            Messages::notifyAdmin();
        }
    }

    public function approvePost() {
        if (!Authentication::checkAuthentication()) {
            // redirect to login
        }
        else {
            // proceed
        }
    }

    public function editPost() {
        if (!Authentication::checkAuthentication()) {
            // redirect to login
        }
        else {
            // proceed
        }
    }

    public function viewPost() {
        // ...
    }
}
```

看一下上面的代码，你可以看到在每个需要用户登录的方法内部都调用了`checkAuthentication()`,并且在创建新帖子时调用`notifyAdmin()`来通知管理员。这不仅是重复的代码，而且`BlogPost`类应该只负责管理帖子。身份验证和通知应该分开进行。我们违反了[单一责任原则](http://en.wikipedia.org/wiki/Single_responsibility_principle "Single responsibility principle - Wikipedia, the free encyclopedia"):

> 单一责任原则声明每个类都应该有一个单一的责任，并且这个责任应该完全由类来封装。它的所有服务都应严格符合这一职责。

所以现在我们来到了一个可以理解 AOP 含义的地方。横切关注点可以被分组到称为“方面”的对象中，从我们的核心代码中分离横切关注点的过程被称为面向方面的编程。

## AOP 术语

AOP 中很少使用特定的术语来解释它的特性。理解这些术语可能是成功地将 AOP 集成到 PHP 项目中的关键。

*   方面
*   建议
*   Joinpoint
*   切入点

我们刚刚学习了什么是方面，所以现在让我们看看其他三个术语是什么意思。

### 建议

一个方面的功能被称为一个建议。顾名思义，建议定义了在某些情况下做什么以及什么时候做。在我们前面的例子中，检查身份验证(什么)是建议，它应该在执行特定方法内的代码(什么时候)之前应用。

### Joinpoint

连接点是应用程序中我们可以创建通知的地方。回头看看示例代码，您会发现我在几个地方调用了与业务逻辑不直接相关的功能。例如，在`createPost()`中，横切关注点发生在执行任何带有认证检查的逻辑之前，之后是向管理员发送消息。这两个都是可能的连接点。

Joinpints 可以在你的应用程序代码中的任何地方定义，但是根据你的 AOP 框架特性，应用通知只能在某些点上可用，这将在本教程的后面讨论。

### 切入点

切入点定义了一种将通知匹配到某些连接点的方法。虽然在我们的例子中有几个连接点，但是在你的应用程序中可能有几千个连接点，你可能不需要对所有的连接点都应用建议。在这种情况下，我们可以匹配连接点的子集，这就是所谓的点切割，并且只对所选的元素应用建议。

假设我们想要建议`createPost()`、`approvePost()`和`editPost()`，而不是`viewPost()`。我们必须以某种方式匹配这三种方法，并应用这些建议。稍后我们将创建一个包含方面细节的 XML 文件，它将包含正则表达式来匹配连接点。

总而言之，**当应用程序中出现横切关注点时，我们创建一个方面，在使用切入点选择的某些连接点中建议应用程序的功能。**

## AOP 建议类型

有几种方法可以建议我们的代码。正如我前面提到的，这些通知类型的可用性取决于您使用的 AOP 框架的类型，但是您应该熟悉的一些类型有:

*   建议之前
*   返回建议后
*   抛出建议后
*   围绕建议

### 建议之前

Before 通知将在代码中的特定点之前应用，通常是方法调用。

到目前为止，我已经使用了方法内部的建议来简化概念并帮助您快速理解它。但是在现实世界中，建议通常不会出现在方法中。每个方法都应该有一个独立的全局控制器，在方法内部，我们可以包装 AOP 功能。这个全局控制器运行在系统内部，对我们来说是不可见的。

```
<?php
class PathController
{
    function controlPaths($className, $funcName) {
        Authentication::checkAuthentication();
        $classObj = new $className();
        $classObj->$funcName();
    }
}
```

在这里，我创建了一个虚拟类来展示它实际上是如何发生的。假设`controlPaths()`方法充当应用程序的全局入口点，每个方法调用都将通过该方法传递。在上面的代码中，我们在实际执行所需的方法调用之前应用了建议`checkAuthentication()`。这叫做事前建议。

### 回来后

一旦特定的功能被完全执行并返回到调用点，这个建议将被应用。考虑以下代码:

```
<?php
class PathController
{
    function controlPaths($className, $funcName) {
        $classObj = new $className();
        $classObj->$funcName();
        Database::closeConnection();
    }
}
```

这里可以看到，一旦方法完成，我们就清理数据库资源。这是返回建议后调用的。

### 投掷后

如果函数在执行过程中抛出异常，抛出后可以应用建议。这里，抛出后的建议是错误报告。

```
<?php
class PathController
{
    function controlPaths($className, $funcName) {
        try {
            $classObj = new $className();
            $classObj->$funcName();
        }
        catch (Exception $e) {
            Error::reportError();
        }
    }
}
```

### 围绕建议

第四种类型的建议是围绕建议，这是建议前和建议后的组合。

```
<?php
class PathController
{
    function controlPaths($className, $funcName) {
        Logger::startLog();
        $classObj = new $className();
        $classObj->$funcName();
        Logger::endLog();
    }
}
```

## 摘要

在本文中，我向您介绍了 AOP。您应该对 AOP 试图实现的目标有一个基本的了解，并且熟悉一些基本的 AOP 术语。在本系列的下一部分，我将向您展示在现实世界的项目中哪里需要 AOP，以及我们如何使用 CodeIgniter 钩子来创建 AOP 功能。敬请期待！

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章