# 单文件 Symfony 应用程序？是的，用 MicroKernelTrait！

> 原文：<https://www.sitepoint.com/single-file-symfony-apps-yes-with-microkerneltrait/>

*这篇文章由[尤尼斯·拉菲](https://www.sitepoint.com/author/yrafie/)、[克劳迪奥·里贝罗](https://www.sitepoint.com/author/cribeiro/)和[海达·külekci̇](https://twitter.com/kulekci)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

单页应用程序(SPA)通过加载单个 HTML 页面，并根据需要动态更新而无需重新加载，为 web 应用程序的用户提供了桌面体验。然而，一个 [Symfony](https://symfony.com/) 应用程序可能有数百个类，在一个基本的应用程序中，我们最终会得到很多我们并不真正需要的文件。

![Illustration of a programmer holding up an elephant](img/d10d976e75ed8882e220e729e134eb5a.png)

Symfony 的最新版本(2.8 和 3.0)向我们介绍了单文件应用程序(SFA)的概念——在一个文件中实现的超薄应用程序或微框架。

要跟进，您需要有一个正在运行的 web 服务器，并有自己的方式在本地运行 web 应用程序。参见 [Laravel 代客文章](https://www.sitepoint.com/what-is-laravel-valet-and-why-all-the-fuss/)快速建立本地开发环境，不需要配置 web 服务器、虚拟主机和修改主机文件。另一个选择是我们值得信赖的[家园改善](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)一个现成的经验。

## 步骤 1:安装准系统 Symfony

我们将安装 Symfony 与[作曲家](https://www.sitepoint.com/php-dependency-management-with-composer/)，因为它允许我们只安装主软件包。创建一个文件夹，在那里你通常有你的网络应用程序，让我们称之为`sfa`。我的在`~/Sites/sfa`下面。在其中，我们安装了 Symfony:

```
composer require symfony/symfony 
```

现在，在`sfa`中创建两个文件夹，命名为`app`和`web`。

## 第二步:前端控制器

在内部`sfa/web`我们将放置我们的前端控制器——一个文件，它接收应用程序的所有请求，将其传递到正确的位置进行处理，并将响应返回给发出请求的客户端。

您可以将这个文件命名为任何名称，但是您需要确保您的 web 服务器已经配置为在正确的位置找到它。Laravel 有`public/index.php`，Drupal 8 有`index.php`，Symfony 有`web/app_dev.php`(开发中)和`web/app.php`(生产中)。既然这是一个 Symfony 应用程序，让我们称我们的为`app_dev.php`:

```
<?php

use Symfony\Component\HttpFoundation\Request;

require __DIR__.'/../vendor/autoload.php';
require __DIR__ . '/../app/SfaKernel.php';

$kernel = new SfaKernel('dev', true);
$request = Request::createFromGlobals();
$response = $kernel->handle($request);
$response->send();
$kernel->terminate($request, $response); 
```

这个文件和普通的 Symfony 3 安装有两个小的区别。

首先，我们的内核类将在`app/SfaKernel.php`中。没有什么能阻止我们称它为`Kernel.php`，但是我们想要不同的东西。其次，我们选择不调用`loadClassCache()`方法。我们的应用程序很小，没有来自标准安装的大量类，所以我们现在可以忽略这个方法。

尽管我们谈论的是单个文件应用程序，但你会注意到它并不是真正的单个文件——我们确实有一个前端控制器和一个迷你内核来完成所有繁重的工作。这是从`vendor`加载的所有其他类的补充。然而，对于所有意图和目的，从单个内核文件启动和运行 Symfony 应用程序可以被视为单个文件应用程序。

## 步骤 3:内核类

创建`app/SfaKernel.php`并添加以下内容:

```
<?php

use Symfony\Component\HttpKernel\Kernel;

class SfaKernel extends Kernel
{
} 
```

我们的类应该从 Symfony core 的内核类继承。

由于内核类是一个抽象类，我们的具体类必须实现`registerContainerConfiguration()`方法。顺便说一下，如果你在`Symfony\Component\HttpKernel\Kernel.php`文件中查找，你不会找到一个`registerContainerConfiguration()`方法——它在`Symfony\Component\HttpKernel\KernelInterface.php`中，内核本身实现了这个方法。

在这里，我们对 Symfony 3 的一个新特性感兴趣(从 2.8 开始也可以使用)，它让我们创建一个微框架，并被恰当地命名为`MicroKernelTrait`。在类内部，使用这个特征:

```
<?php

use Symfony\Component\HttpKernel\Kernel;
use Symfony\Bundle\FrameworkBundle\Kernel\MicroKernelTrait;

class SfaKernel extends Kernel
{
     use MicroKernelTrait;
} 
```

现在我们需要在这个类中实现三个方法——`configureRoutes()`、`configureContainer()`和`registerBundles()`。前两个是来自特征的抽象方法，而`registerBundles()`在内核实现的`Symfony\Component\HttpKernel\KernelInterface`中，我们反过来在我们的微内核中扩展。如果我们仔细看看那些方法，我们可以从评论中学到很多。

1.  `registerBundles()`

    Symfony 中的 bundle 是实现一个特性的一组文件。其他应用或框架讲的是插件或模块。目前我们唯一需要的就是 Symfony 框架本身。注释说，*“返回要注册的包的数组。”*，所以我们的方法应该是这样的:

    ```
    public function registerBundles()
    {
        return [
            new FrameworkBundle()
        ];
    } 
    ```

2.  `configureRoutes()`

    这是我们为应用程序添加或导入路线的地方。路由是查看请求中的路径，并确定将它定向到哪里，以最终获得响应。该注释通常很有帮助，它告诉我们两种方式:

    ```
    $routes->import('config/routing.yml'); 
    ```

    您可以在定义路线的位置添加配置文件，并将其导入到此处。

    ```
    $routes->add('/admin', 'AppBundle:Admin:dashboard', 'admin_dashboard'); 
    ```

    您指定一个路径(`/admin`)，用一个方法(`AppBundle:Admin:dashboard`)添加一个控制器类，并可选地给它一个名称或别名(`admin_dashboard`)。

    然而，有第三种方式，而不是第二种方式来指定您的控制器。例如，`'kernel:home'`引用当前内核类中的一个方法`home()`。实际上，这个`SfaKernel`类同时也是一个控制器。多好啊！让我们添加 2 条路线。

    ```
    $routes->add('/', 'kernel:home'); 
    ```

    当我们转到我们的主页时，请求将被路由到这个类中的`home()`方法。

    ```
    $routes->add('/greet/{who}', 'kernel:greet'); 
    ```

    类似地，这个路由将所有请求匹配到带有由`{who}`表示的路由参数的`/greet/{who}`，并将它们传递给带有`$who`参数的名为`greet`的方法。

    让我们继续实现这些方法。再一次，确保你有`use Symfony\Component\HttpFoundation\Response;`在班上名列前茅。

    ```
    public function home() {
        return new Response(
            '<p>Home, sweet home</p>'
        );
    }

    public function greet($who)
    {
        return new Response(
            "<h1>Greeting</h1><p>Hello $who</p>"
        );
    } 
    ```

    请记住，您需要从方法中返回一个响应对象。

3.  `configureContainer()`

    容器保存了各种各样的类，它们通常需要不同的参数或传递给它们的任何其他配置。这是您注册任何扩展、服务或参数的地方，例如

    ```
    $c->loadFromExtension('framework', array(
           'secret' => '%secret%'
    ));

    $c->register('halloween', 'FooBundle\HalloweenProvider');

    $c->setParameter('halloween', 'lot of fun'); 
    ```

    我们得到的唯一扩展是 FrameworkBundle，它有许多[配置选项](http://symfony.com/doc/current/reference/configuration/framework.html)，但只有一个选项`secret`是必需的。它们作为由选项键控的关联数组提供。我们的`secret`的值应该是唯一的，所以我们的方法应该是这样的:

    ```
    protected function configureContainer(ContainerBuilder $c, LoaderInterface $loader)
    {
        $c->loadFromExtension('framework', [
            'secret' => 'micr0',
        ]);
    } 
    ```

如果你已经配置了你的网络服务器，进入主页，你会看到，**主页，甜蜜的主页**。将`/greet/Symfony`追加到 URL。**你好 Symfony** 应该显示在浏览器中。

您可以在`registerRoutes()`中注册任意多的路由，并从同一个类返回一个响应。你有一个工作的 Symfony 单文件应用程序。

在继续之前，让我们回到最初扩展内核类的时候。我们只需要实现一种方法——`registerContainerConfiguration()`。当我们添加 MicroKernelTrait 时，我们需要 3 个方法。

如果你打开`Symfony\Bundle\FrameworkBundle\Kernel\MicroKernelTrait.php`，你会注意到已经为我们实现了`registerContainerConfiguration()`方法，以使它更加灵活和可配置。让我们过一遍。

```
public function registerContainerConfiguration(LoaderInterface $loader)
{
    $loader->load(function (ContainerBuilder $container) use ($loader) {
        $container->loadFromExtension('framework', array(
            'router' => array(
                'resource' => 'kernel:loadRoutes',
                'type' => 'service',
            ),
        ));

        $this->configureContainer($container, $loader);

        $container->addObjectResource($this);
    });
} 
```

在闭包内部，查看`router`的`resource`数组键，您将看到`kernel:loadRoutes`。当我们考虑为我们的路由指定一个控制器类时，我们遇到过类似的事情。这里是同一个概念。`kernel`指的是在扩展内核类时使用这个特征的类，`:loadRoutes`会在这个特征中寻找一个方法。

```
public function loadRoutes(LoaderInterface $loader)
{
    $routes = new RouteCollectionBuilder($loader);
    $this->configureRoutes($routes);

    return $routes->build();
} 
```

我们的`configureRoutes()`就是从这个方法被调用的。为了确保它在任何使用这一特性的微框架中实现，它被定义为一个抽象方法。

## 用例

在单个文件中实现现实生活中的应用程序并不是 MicroKernelTrait 的主要目标。这是为了让开发人员在如何构建应用程序、添加什么包以及何时添加等方面拥有更大的灵活性。他们可以从一个非常小的安装开始，逐步构建更多的功能，而不是使用一个庞大的软件包的一小部分。例如，我们的例子没有模板，但是可以很容易地添加。

其他人建议基于全栈 Symfony 框架从 MicrokernelTrait 构建微服务。另一种可能性是将 GET 请求分离出来，由更精简的 MicrokernelTrait 应用程序来处理，而对更多资源密集型进程的其他请求则由更传统的 Symfony 应用程序来管理。

## 结论

在 Symfony 2.8 和 3.0 之前，Silex 和 Lumen 等微框架是对实现完整框架有所保留的项目的可用选项。然而，单个文件应用程序的概念提供了另一个中间地带。

对于 Symfony 来说，这是一件令人兴奋的事情，在未来的日子里，人们可以期待开发人员从这一新功能中挤出巧妙的用例。就个人而言，我希望`vendor`文件夹得到进一步的审查。带有`composer require symfony/symfony`的准系统安装真的应该包含所有这些依赖吗？

现在还为时尚早，但潜力是存在的，开发人员采用这一新功能的方向还有待观察。你还在用它吗？让我们知道！

## 分享这篇文章