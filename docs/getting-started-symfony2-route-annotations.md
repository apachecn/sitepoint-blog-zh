# Symfony2 路线注释入门

> 原文：<https://www.sitepoint.com/getting-started-symfony2-route-annotations/>

当你下载标准的 [Symfony 2](http://symfony.com/) 发行版时，它包含了一个有趣的包，名为[SensioFrameworkExtraBundle](https://github.com/sensiolabs/SensioFrameworkExtraBundle)，它实现了很多很棒的东西，尤其是在你的控制器中直接使用注释的机会。

这篇文章背后的想法不是说服开发人员接受这种方式，而是指出另一种简单配置控制器的方法。请记住，没有神奇的配方，这取决于您在每个特定场景中的需求。

Symfony 2 实现了一个强大的内置组件来管理应用程序的所有路由:路由组件。基本上，路由将 URL 映射到控制器动作。因为 Symfony 旨在模块化，所以有一个文件专门用于此: **routing.yml** 。你会在`app > config > routing.yml`中找到。

为了理解如何用注释定义我们的路线，我将以一个简单的博客应用程序为例。

## 步骤 1:创建主页路径

我们只想将路径`/`链接到我们的`HomeController`的一个动作。

### 不带注释

在`app/config/routing.yml`中:

```
blog_front_homepage:
  path : /
  defaults:  { _controller: BlogFrontBundle:Home:index }
```

在`src/Blog/FrontBundle/Controller/HomeController.php`中:

```
<?php
namespace Blog\FrontBundle\Controller;

class HomeController
{
    public function indexAction()
    {
        //... create and return a Response object
    } 
}
```

让我们看看那些文件。在`routing.yml`中，我们为名为`blog_front_homepage`的路由声明了一个简单的配置，它有两个参数:路径和我们想要作为目标的控制器的动作。就控制器而言，它不需要任何特殊的东西。

### 带注释

在`app/config/routing.yml`中:

```
blog_front:
    resource: "@BlogFrontBundle/Controller/"
    type:     annotation
    prefix:   /
```

在`src/Blog/FrontBundle/Controller/HomeController.php`中:

```
<?php

namespace Blog\FrontBundle\Controller;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;

class HomeController
{
    /**
     * @Route("/", name="blog_home_index")
     */
    public function indexAction() { /* ... */ }
}
```

首先，我们来看看`routing.yml`发生了什么:

*   `resource`目标控制器影响
*   `type`显然定义了我们声明路线的方式
*   `prefix`为控制器类的所有动作定义一个前缀(可选)

更有趣的是，我们的控制器现在是如何构建的。在此之前，我们必须调用 SensioFrameworkExtraBundle 的相关类:`use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;`。我们现在准备实现我们想要分配的路由和参数:在本例中，只有路径和名称(我们将在后面看到我们能做的所有事情):`@Route("/", name="blog_homepage")`。

你们中的一些人可能会想:“我们知道如何用路由层覆盖控制器，那又怎么样呢？最终，基本相同的结果需要更多的代码”。你是对的……至少现在是。

## 步骤 2:添加文章页面路径

### 不带注释

在`app/config/routing.yml`中:

```
blog_front_homepage:
  path : /
  defaults:  { _controller: BlogFrontBundle:Home:index }

blog_front_article:
  path : /article/{slug}
  defaults:  { _controller: BlogFrontBundle:Home:showArticle }
```

在`src/Blog/FrontBundle/Controller/HomeController.php`中:

```
<?php
// namespace & uses...

class HomeController
{
    public function indexAction() { /* ... */ }

    public function showArticleAction($slug) { /* ... */ }
}
```

### 带注释

在`app/config/routing.yml`中:

```
blog_front:
    resource: "@BlogFrontBundle/Controller/"
    type:     annotation
    prefix:   /
```

在`src/Blog/FrontBundle/Controller/HomeController.php`中:

```
<?php
// namespace & uses...

class HomeController
{
    /**
     * @Route("/", name="blog_home_index")
     */
    public function indexAction() { /* ... */ }

    /**
     * @Route("/article/{slug}", name="blog_home_show_article")
     */
    public function showArticleAction($slug) { /* ... */ }
}
```

你注意到了吗？不需要任何改变。现在，您只需看一眼就可以知道哪个动作是从路由模式中调用的。

如果您希望控制器中的所有操作都有一个前缀，例如`/admin`，您可以从`routing.yml`中移除`prefix`键，并在类的顶部添加一个额外的`@Route`注释:

在`app/config/routing.yml`中:

```
blog_front: ...

blog_admin:
    resource: "@BlogAdminBundle/Controller/"
    type:     annotation
```

在`src/Blog/AdminBundle/Controller/AdminController.php`中:

```
<?php
// namespace & uses...

/**
 * @Route("/admin")
 */
class AdminController
{
    /**
     * @Route("/", name="blog_admin_index")
     */
    public function indexAction() { /* ... */ }

    /**
     * @Route("/create", name="blog_admin_create_article")
     */
    public function createArticleAction() { /* ... */ }
}
```

## 步骤 3:额外路由配置

### 设置 URL 默认参数

语法:`defaults = { "key" = "value" }`。

```
/**
 * @Route(
 *       path     = "/article/{slug}",
 *       name     = "blog_home_show_article",
 *       defaults = { "slug" = "hello" }
 * )
 */
```

通过将`slug`添加到`defaults`键，不再需要`{slug}`占位符。URL `/article`将匹配该路线，并且`slug`参数的值将被设置为`hello`。URL `/blog/world`也将匹配，给页面参数一个值`world`。

### 添加要求

语法:`requirements = { "key" = "value" }`。

```
/**
 * @Route(
 *       path         = "/article/{slug}",
 *       name         = "blog_home_show_article",
 *       defaults     = { "slug" = "hello" },
 *       requirements = { "slug" = "[a-z]+" }
 * )
 */
```

我们可以用正则表达式定义对`slug`键的要求，明确定义`{slug}`的值必须全部由字母字符组成。在下面的例子中，我们对数字做了完全相同的事情:

```
/**
 * @Route(
 *       path         = "article/{id}",
 *       name         = "blog_home_show_article",
 *       defaults     = { "id" = 1 },
 *       requirements = { "id" = "\d+" } 
 * )
 */
```

如果你需要更多关于正则表达式的信息，请点击这个[链接](http://www.regular-expressions.info/tutorial.html)。

### 实施 HTTP 方法

语法:`methods = { "request method" }`。

```
/**
 * @Route(
 *       path         = "/article/{slug}",
 *       name         = "blog_home_show_article",
 *       defaults     = { "slug" = "hello" },
 *       requirements = { "slug" = "[a-z]+" },
 *       methods      = { "GET", "POST" }
 * )
 */
```

我们还可以在传入请求的方法上进行匹配(即`GET`、`POST`、`PUT`、`DELETE`)。请记住，如果没有指定方法，路由将匹配其中任何一个方法。

### 实施 HTTP 方案

语法:`schemes = { "protocol" }`。

```
/**
 * @Route(
 *       path         = "/article/{slug}",
 *       name         = "blog_home_show_article",
 *       defaults     = { "slug" = "hello" },
 *       requirements = { "slug" = "[a-z]+" },
 *       methods      = { "GET", "POST" },
 *       schemes      = { "https" }  
 * )
 */
```

在这种情况下，我们希望保护路由，确保通过 HTTPS 协议访问它。

### 强制使用主机名

语法:`host = "myhost.com"`。

```
/**
 * @Route(
 *       path         = "/article/{slug}",
 *       name         = "blog_home_show_article",
 *       defaults     = { "slug" = "hello" },
 *       requirements = { "slug" = "[a-z]+" },
 *       methods      = { "GET", "POST" },
 *       schemes      = { "https" },
 *       host         = "myblog.com"
 * )
 */
```

我们也可以在 HTTP 主机上匹配。这个只有在主机是`myblog.com`的情况下才会匹配。

## 步骤 4:锻炼

由于我们现在能够构建一个可靠的路由结构，想象我们必须创建一个动作来删除我们的`AdminController.php`中的一篇具有正确路由的文章。我们必须:

*   将路径定义为`/admin/delete/article/{id}`；
*   将名称定义为`blog_admin_delete_article`；
*   将键`id`的要求定义为仅数字；
*   定义`GET`请求方法。

卡住了？看看下面的解决方案:

```
<?php
// src/Blog/AdminBundle/Controller/AdminController.php
// namespace & uses...

/**
 * @Route("/admin")
 */
class AdminController
{
    /* ... */

    /**
     * @Route("
     *    path          = "/delete/article/{id}", 
     *    name          = "blog_admin_delete_article"
     *    requirements  = { "id" = "\d+" },
     *    methods       = { "GET" }
     * )
     */
    public function deleteArticleAction($id) { /* ... */ }
}
```

## 最后的想法

正如您所看到的，用注释管理路线既易于编写，也易于阅读和维护。它还有一个很好的优点，将代码和配置都收集在一个独特的文件中:controller 类。

您使用注释还是标准配置？你更喜欢哪个，为什么？

## 分享这篇文章