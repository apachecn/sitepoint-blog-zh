# 构建一个 Drupal 8 模块:路由、控制器和菜单链接

> 原文：<https://www.sitepoint.com/build-drupal-8-module-routing-controllers-menu-links/>

请注意，由于撰写本文时 Drupal 8 正在经历开发过程，部分代码可能已经过时。看一下[这个库](https://github.com/upchuk/d8-demo-modules)，我试图在其中更新示例代码，并使其与最新的 Drupal 8 版本兼容。

Drupal 8 带来了很多改变，试图让它加入其他现代 PHP 框架所属的俱乐部。这意味着旧的 PHP 4 风格的过程化编程被面向对象的架构所取代。为了实现这一点，在*的倡议下，Drupal 8 包含了不是专门为 Drupal 开发的代码。*

Drupal 最重要的新增功能之一是 [Symfony 组件](https://www.sitepoint.com/symfony-drupal-8/)，对 Drupal 开发人员有两个主要影响。首先，它有可能极大地增加现在想要为 Drupal 开发的开发人员的数量。第二，它让一些没有多少现代 PHP 实践经验的当前 Drupal 7 开发人员感到害怕。但是没关系，我们都在学习，从 Symfony(希望还有 Drupal 8)这样的框架中吸取的经验教训将很容易扩展并适用于其他 PHP 框架。

与此同时，Drupal 8 正处于发布周期的后期，在撰写本文时的当前版本是`alpha11`。我们将使用这个版本展示 Drupal 7 开发人员将首先遇到并应该熟悉的模块开发的一些基本变化。我建立了一个 [Git repo](https://github.com/upchuk/d8-demo-modules) ,在那里你可以找到我在这个系列中写的代码，所以如果你愿意，你可以继续这样做。

## 我如何创建一个模块？

我们要看的第一件事是定义必要的文件和文件夹结构，以告诉 Drupal 8 我们的新模块。在 Drupal 7 中，我们必须创建至少两个文件(`.info`和`.module`)，但是在 Drupal 8 中，前者的 YAML 版本就足够了。是的，`.info`文件现在被替换为`.info.yml`文件，包含相似的数据，但结构不同。

另一个主要变化是自定义和贡献模块文件夹现在直接进入根`modules/`文件夹。这是因为所有的核心代码都被移到了一个单独的`core/`文件夹中。当然，在`modules/`目录中，我们鼓励您像在 Drupal 7 中一样，在*自定义*和*贡献*之间分离模块。

让我们继续创建一个名为`demo`(非常原始)的模块，并将其放在`modules/custom/`目录中。正如我提到的，在这个新创建的`demo/`文件夹中，我们需要的是一个`demo.info.yml`文件，它包含以下必需的内容:

```
name: Drupal 8 Demo module
description: 'Demo module for Drupal 8 alpha11'
type: module
core: 8.x
```

你应该熟悉的四个中的三个(名称、描述和核心)。`type`现在也是一个需求，因为你也可以为主题创建 yml 文件。要记住的另一件重要的事情是，yml 文件中的空格是有意义的，适当的缩进用于在类似数组的结构中组织数据。

您可以查看[这个文档页面](https://drupal.org/node/2000204)，了解可以进入模块`.info.yml`文件的其他键|值对，以及宣布转换到这种格式的[变更通知](https://drupal.org/node/1935708)。

就这样，一个文件。您现在可以导航到*扩展*页面，找到演示模块并启用它。

正如我提到的，我们不再需要在启用模块之前创建一个`.module`文件。从架构上来说，`.module`文件的大小将会显著减小，因为大部分业务逻辑都转移到了服务类、控制器和插件上，不过我们稍后会看到其中的一部分。

## 什么是‘路由’，hook_menu()及其回调发生了什么？

在 Drupal 7 中，`hook_menu()`可能是实现最多的钩子，因为它用于定义到 Drupal 的路径，并用回调函数连接这些路径。它还负责创建菜单链接和其他一些东西。

在 Drupal 8 中，我们不再需要`hook_menu()`了，因为我们大量使用了 [Symfony2 组件](https://drupal.org/node/1635626)来处理路由。这包括将路由定义为配置并在控制器中处理回调(一个`Controller`类的方法)。让我们通过创建一个输出经典`Hello world!`的简单页面来看看它是如何工作的。

首先，我们需要为我们的模块`demo.routing.yml`创建一个路由文件。该文件放在模块根文件夹中(在`demo.info.yml`旁边)。在这个文件中，我们可以有以下(简单的)路由定义:

```
demo.demo:
  path: '/demo'
  defaults:
    _content: '\Drupal\demo\Controller\DemoController::demo'
    _title: 'Demo'
  requirements:
    _permission: 'access content'
```

第一行标志着模块`demo`的新路径`demo`的开始(第一行是模块名，第二行是路径名)。在`path`下，我们指定我们希望该路由注册的路径。在`defaults`下，我们有两个东西:默认页面标题(`_title`)和引用了`DemoController`类上的方法的`_content`。在`requirements`下，我们指定访问用户查看页面所需的权限。您应该查阅[该文档页面](https://drupal.org/node/2092643)以获得更多关于该路由文件的选项。

现在，让我们创建第一个名为`DemoController`的控制器，当用户请求该页面时，将调用名为`demo()`的方法。

在模块目录中，创建一个名为`src/`的文件夹和一个名为`Controller/`的文件夹。这将是存储控制器类的地方。继续创建第一个:`DemoController.php`。

将控制器和其他类放入`src/`文件夹是采用 PSR-4 标准的一部分。[最初](https://drupal.org/node/2246699)，我们必须创建一个更大的文件夹结构(PSR-0 标准)，但现在有一个过渡阶段，两者都将工作。因此，如果你仍然看到代码放在一个名为`lib/`的文件夹中，那就是 PSR-0。

在我们的`DemoController.php`文件中，我们现在可以声明我们的类:

```
<?php
/**
 * @file
 * Contains \Drupal\demo\Controller\DemoController.
 */

namespace Drupal\demo\Controller;

/**
 * DemoController.
 */
class DemoController {
  /**
   * Generates an example page.
   */
  public function demo() {
    return array(
      '#markup' => t('Hello World!'),
    );
  }      
}
```

为了在页面上显示一些东西，这是我们需要做的最简单和最少的事情。在顶部，我们指定了类的名称空间，在下面我们声明了类。

在`DemoController`类中，我们只有返回类似 Drupal 7 的可呈现数组的`demo()`方法。没什么大事。我们现在要做的就是清空缓存并导航到`http://example.com/demo`，我们应该会看到一个印有 *Hello World* 的 Drupal 页面。

## 菜单链接呢？

在 Drupal 7 中，当我们实现`hook_menu()`时，我们还可以添加菜单的注册路径，以便在站点上显示菜单链接。这也不再用这个钩子来处理，但是我们使用一个 yml 文件来声明菜单链接作为配置。

让我们看看如何创建一个菜单链接，显示在管理的`Structure`菜单下。首先，我们需要在模块的根目录下创建一个名为`demo.menu_links.yml`的文件。在这个 yml 文件中，我们将定义菜单链接以及它们在站点现有菜单中的位置。为了实现我们的目标，我们需要:

```
demo.demo:
  title: Demo Link
  description: 'This is a demo link'
  parent: system.admin_structure
  route_name: demo.demo
```

同样，我们有一个基于缩进的 yml 结构，其中我们首先为模块`demo`定义菜单链接的机器名称(`demo`)(就像我们对路由所做的那样)。接下来，我们有链接标题和描述，后面是这个链接的父链接(应该放在哪里)和它应该使用什么路由。

`parent`的值是父菜单链接(附加了它的模块),为了找到它，你需要在`*.menu_links.yml`文件中做一些挖掘。我知道`Structure`链接是在核心系统模块中定义的，所以通过查看`system.menu_links.yml`文件，我可以确定这个链接的名称。

`route_name`是我们想要用于该链接的路径的机器名。我们之前定义了我们的。有了这个，你可以清空缓存并导航到`http://example.com/admin/structure`，在这里你会看到一个全新的菜单链接，有正确的标题和描述，链接到`demo/`路径。还不错。

## 结论

在本文中，我们开始探索 Drupal 8 中的模块开发。在这个阶段(alpha11 版本)，是时候开始学习如何使用新的 API 和 port contrib 模块了。为此，我写下了我对这个新的和现有的框架的探索，这个框架将会是 Drupal 8，这样当发布日到来时，我们都可以了解这些变化并立即投入运行。

首先，我们看了一些基础知识:如何启动 Drupal 8 模块(文件、文件夹结构等)，所有这些都与 Drupal 7 进行了比较。我们还看到了如何定义路由和一个控制器类，以及这个路由调用的方法。最后，我们看到了如何创建一个使用我们定义的路径的菜单链接。

在下一篇教程中，我们将继续构建这个模块，并看看 Drupal 8 使用的其他一些很酷的新东西。我们将看到如何创建块以及如何使用表单和配置系统。到时候见。

## 分享这篇文章