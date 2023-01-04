# Zend Framework 2 的新特性

> 原文：<https://www.sitepoint.com/whats-new-in-zend-framework-2/>

当我开始为 SitePoint 写作时，[我的第一篇文章](https://www.sitepoint.com/setting-custom-error-messages-for-zend-form-element/ "Setting Custom Error Messages for Zend_Form_Element")是关于 Zend 框架的。从那以后，该框架发布了稳定版 2。除了名字，版本 2 和老版本相比真的是一个新项目；ZF 完全被改写了。在这篇文章中，我将向您概述新的特性和已经引入的变化。

## 包装系统

你有多达四种不同的方法来获得 ZF2。第一种是传统方式，从[网站的下载页面](http://framework.zend.com/downloads/latest "Latest - Downloads - Zend Framework")或[packages.zendframework.com](http://packages.zendframework.com/ "ZF2 Packages")下载其中一个软件包。第二个类似，但是你可以从 [ZF GitHub 库](https://github.com/zendframework/zf2/ "zendframework/zf2 - GitHub")下载。第三种是通过[使用 Composer](https://www.sitepoint.com/php-dependency-management-with-composer/ "PHP Dependency Management with Composer") ，一个用于依赖管理的工具。要使用 Composer 安装 ZF2，请将以下几行添加到您的`composer.json`文件中:

```
"repositories": [
  {
    "type": "composer",
    "url": "https://packages.zendframework.com/"
  }
],

"require": {
  "zendframework/zend-config": "2.0.*",
  "zendframework/zend-http": "2.0.*"
}
```

第四种方法是使用 PEAR2_Pyrus，运行以下命令:

```
pyrus.phar .
pyrus.phar . channel-discover packages.zendframework.com
pyrus.phar . install zf2/Zend_Framework#Standard
```

对于版本 2，ZF 已经开发并发布了一个基本的框架应用程序来帮助你开始开发自己的应用程序。你可以从 [GitHub](https://github.com/zendframework/ZendSkeletonApplication/archive/master.zip "Sample application skeleton using the ZF2 MVC layer") 下载或者使用 Composer:

```
composer.phar create-project --repository-url="http://packages.zendframework.com" zendframework/skeleton-application path/to/install
```

## 自动装载系统

ZF2 似乎为每个新特性提供了许多不同的选择。例如，自动装载系统现在有三个不同的选项。你会发现在第一个版本中大量使用的`require_once`行不见了。当然，需要一个`require_once`来启用新的自动加载系统，即自动加载器。要使用的代码应该如下所示:

```
<?php
require_once 'Zend/Loader/StandardAutoloader.php';
$autoLoader = new ZendLoaderStandardAutoloader(array(
    'fallback_autoloader' => true,
));
$autoLoader->register();
```

这个方法使用 PSR-0 标准加载命名空间类(哈里·K·T 在他的文章《PHP 中的自动加载和 PSR-0 标准》中对 PSR-0 做了大量的讨论)。注意，设置`'fallback_autoloader' => true`启用了一个回退，允许您调用 PHP 默认包含系统来包含那些不属于 Zend 名称空间的文件。

第二种方法基于类别映射，这是一个带有关联数组的文件，其中键是类名，值是它们的绝对路径。由于类别映射是一个返回绝对路径的简单文件，因此与前一种方法相比，这种方法的速度更快，在标准使用情况下可提高 20%，使用 APC 等操作码缓存系统时可提高 80%。这个系统中令人厌烦的是创建地图文件本身，尤其是对于较大的项目。幸运的是，ZF 团队创造了一个[类地图生成器](https://github.com/zendframework/zf2/blob/master/bin/classmap_generator.php "Class map generator")，它可以自动为你构建地图。默认情况下，它会在当前目录中搜索文件，并将输出写入在指定路径中创建的文件中。其用法的一个例子是:

```
php classmap_generator.php My/Project
```

但是，您可以配置该脚本。关于可用选项的概述，请看一下[类地图生成器文档](http://framework.zend.com/manual/1.12/en/zend.loader.classmap-generator.html "Class map generator documentation")。

第三种方法与第一种非常相似。这种情况下的变化是，您可以使用给定的名称空间指定文件的路径，如下例所示。

```
<?php
require_once 'Zend/Loader/StandardAutoloader.php';
$autoLoader = new ZendLoaderStandardAutoloader();
$autoLoader->registerNamespace('ApplicationNamespace', 'Path/to/files');
$autoLoader->register();
```

或者，您可以设置特定的前缀。

```
<?php
require_once 'Zend/Loader/StandardAutoloader.php';
$autoLoader = new ZendLoaderStandardAutoloader();
$autoLoader->registerNamespace('FilesPrefix_', 'Path/to/files');
$autoLoader->register();
```

## 从属系统

开发人员试图编写尽可能解耦的代码，为了实现这一目标，新版本遵循了控制反转(IoC)原则。正如 [Wikipedia](http://en.wikipedia.org/wiki/Inversion_of_control) 所述，IoC 是“一种编程技术，用面向对象编程的术语来表达，其中对象耦合在运行时由汇编器对象绑定，在编译时使用静态分析通常是未知的。”为了更好地解释这种模式，我建议你看看亚历杭德罗·格瓦西奥的文章[控制倒置——好莱坞原则](https://www.sitepoint.com/inversion-of-control-the-hollywood-principle/ "Inversion of Control – The Hollywood Principle")。

## 事件管理器

本文中我想讨论的最后一个特性是由类`ZendServiceManager`实现的新事件管理器，它取代了先前 1.x 版本的组件`ZendApplication`。事实上，ZF2 现在是事件驱动的。您可能已经知道，在实际运行控制器的动作之前，框架会做很多事情。第一步是运行引导程序，设置应用程序模块和配置。引导之后，解析用户请求的 URL，以正确的方式(通常是模块/控制器/动作)路由应用，然后启动调度程序。在每一个步骤中，您都有一组事件，您几乎可以用您喜欢的任何方式来管理这些事件，以改变应用程序流。

您可以通过`EventManager`类的`attach()`方法添加事件。它接受要监听的事件的名称、事件触发时要调用的回调函数以及指定事件优先级的(可选)参数。后者由正整数指定，默认情况下为 1。数字越大，优先级越高，执行越早。

可以使用同样属于`EventManager`的`trigger()`方法调用该事件。它的参数是要触发的事件的名称、通常是拥有该事件的对象的实例的上下文(如果使用匿名函数，则为 null)以及包含要传递给事件处理程序的参数的数组。下面是一个简单的例子:

```
<?php
// Attach the handler
$event = new EventManager();
$event->attach(
   'myEvent',
   function($event) {
      $parameters = $event->getParams();
      echo 'The given name is ' . $parameters['name'] . ' ' . $parameters['surname'];
   },
   100
);

// Fire the event
$parameters = array('name' => 'Aurelio', 'surname' => 'De Rosa');
$event->trigger('myEvent', null, $parameters);
```

## 结论

通过这篇文章，您了解了 Zend Framework 2 中的主要变化。综上所述，这些是我讨论的要点:

*   新的打包系统，基于 composer 和 pyrus，下载和安装
*   基于事件的新型 MVC 架构
*   更好的性能
*   新的自动加载类系统
*   ZF2 使用了几个新的设计模式，如事件管理器和依赖注入，帮助您分离代码

您可能会被所有这些变化淹没，我认为这并不奇怪，因为已经引入了大量的新功能和改进。其实这篇文章只是撇清了表面。请随意下载该框架的副本并进行探索。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章