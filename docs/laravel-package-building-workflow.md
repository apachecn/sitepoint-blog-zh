# 我的 Laravel 包构建工作流

> 原文：<https://www.sitepoint.com/laravel-package-building-workflow/>

*本文由 [Claudio Ribeiro](https://www.sitepoint.com/author/cribeiro) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

包是 Laravel 体验中非常重要的一部分(就像其他框架一样)。无论我们需要做什么，很可能已经有了一个解决方案；准备好来一场`composer require`带来一些魔力。

![Laravel Gift Package](img/1c90871ba5f49992cac5f2e6512f1e0c.png)

几周前，我有了一个新的想法。我为 [AdEspresso](https://adespresso.com/) 工作，在那里我们有一个 Symfony [FeatureBundle](https://github.com/adespresso/FeatureBundle) ，它处理我们项目中的功能切换。这是一段非常好的代码，我们用它来为特定的用户子集发布新功能。所以，我问自己…为什么不把它移植到拉勒维尔？我的 **[Laravel 功能包](https://github.com/francescomalatesta/laravel-feature)** 的想法就是这样诞生的。

在开始之前，我不得不问自己另一个问题:**我应该遵循哪条路径来制作 Laravel 5 的包？**我在网上找到了大量的技巧，许多指南，但是没有一个真正的方法论告诉我如何为我最喜欢的框架构建一个包。在这篇文章中，我将尝试解释**我如何为它准备我的开发环境**，以及**我在构建包的时候做了哪些选择**。不是开发每一行代码本身，而是从无到有的实际工作流程。

显然，不要认为每一步都是理所当然的:我充分意识到有些事情可能是草率的或者完全错误的。如果你想给我一些反馈，请在文章下面留言。

## 功能标记？

当我们在软件中实现功能标记时，我们能够控制何时推出特定功能，以及谁应该能够使用该功能——这对大项目非常有用，但对小项目不太有用。

特征标记应用的一个典型例子是“金丝雀释放”的概念(金丝雀被用于矿井中检测有毒气体，因为它们在人类之前被它击倒——它们是“测试者”)。想象一下，想要部署一个新特性:然而，为了更好地监控，我们想要在第一周限制对它的访问，以便更好地了解如果出现问题会发生什么。我们有一组特定的用户(例如，我们总用户群的 5%)被确定为测试人员，我们在这个实验中“使用”他们。实现特性标记意味着对我们的软件特性的生命周期有更多的控制，并避免错误和错误。

Laravel 不提供任何现成的特性标记。授权组件可能与之类似:但是，它总是与特定的资源相关。肯定不是我们的情况:一个特性可能同时涉及多个资源，或者有时一个都不涉及！所以…我们自己做一个吧！

*请注意，我并不打算涵盖软件包本身的所有内容。我想做的是以一种更通用的方式解释我用来构建包的工作流。*

那就说……给包装机！

## 准备环境

我总是在我的项目中使用 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) ，你也应该这样做。在几分钟内，我们将有一个新的虚拟机启动并运行我们的实验！如果我们真的非常非常懒，我们可以试试 [LaraPrep](https://github.com/francescomalatesta/laraprep) ，这是一个准备一切的有用脚本。它目前只与 Linux 兼容，但应该也能在你的 Mac 上运行。

本文将介绍该框架的 5.4 版本。

### 命名空间和文件夹

开发新包时需要回答的第一个问题是:“我的代码放在哪里？”

通常建议使用 PSR-4 自动加载创建另一个名称空间，与项目代码分开，并将其“链接”到一个专用文件夹。

`composer.json`文件中项目的默认值为

```
"psr-4": {
    "App\\": "app/"
} 
```

我们在这里要做的就是添加一个新的项目，比如

```
"psr-4": {
    "App\\": "app/",
    "LaravelFeature\\": "LaravelFeature/src"
} 
```

假设`LaravelFeature`文件夹将包含包代码，我们将它与一个完全分离的名称空间相关联。这种分离程度是一个好的开始，但显然还不够。让我们更深入地研究一下结构。

### 玩弄正确的骨架

如果你看一下 GitHub 上的[代码，你会注意到各种各样的文件，比如`CHANGELOG.md`、`CONTRIBUTING.md`等等。我并没有发明它们:它们是常见的实践，是由](https://github.com/francescomalatesta/laravel-feature)[PHP legue Skeleton Package](https://github.com/thephpleague/skeleton)建议的，对于任何想要开发新 PHP 包的人来说，这是一个极好的样板。

让我们看看它最重要的部分:

*   `src`文件夹:包含我们包的源代码。
*   文件夹:因为我们要写测试！我们总是写测试，对吗？
*   `README.md`文件:以散文的形式介绍这个包。
*   `LICENSE.md`文件:指定许可细节将帮助开发人员理解他们可以用我们的代码做什么，以及如何做。
*   `.scrutinizer.yml`、`.styleci.yml`和`.travisci.yml`文件:[审查者](https://www.sitepoint.com/inspecting-php-code-quality-scrutinizer/)，StyleCI 和 [TravisCI](https://www.sitepoint.com/php-continuous-integration-travis-ci/) 都是牛逼的(对开源项目是免费的)服务。他们分别分析代码质量、修复风格问题和运行测试。

在我们继续之前，**关于测试的一个快速提示:不要过度测试，但也不要测试不足**。现在是 2017 年，应该没有必要警告人们这一点，但我仍然看到许多包没有一个单一的测试来断言它们的质量(完全是双关语)。

## 进入代码:领域

当编写一个包时，一个好的方法是抽象出我们想要用来解决问题的领域逻辑，然后编码实现。Laravel 有一个很好的服务容器，它允许我们将一个接口绑定到一个具体的类，并保持我们的代码松散耦合:让我们使用它。

第一站:包的 GitHub 库。[在`src`文件夹](https://github.com/francescomalatesta/laravel-feature/tree/master/src)里有一个`Domain`文件夹:让我们打开它。请随意探索代码。你能看到任何 Laravel 特有的代码吗？不，因为我们的领域逻辑应该与真正的实现保持分离。

[`LaravelFeature\Domain\Model\Feature`](https://github.com/francescomalatesta/laravel-feature/blob/master/src/Domain/Model/Feature.php) 就是很好的例子。它描述了什么是特性，以及它的功能是什么。每个功能都有自己的名称和状态(在系统中启用或禁用)。

```
<?php

namespace LaravelFeature\Domain\Model;

class Feature
{
    private $name;
    private $isEnabled;

    public static function fromNameAndStatus($name, $isEnabled)
    {
        $feature = new self($name, (bool) $isEnabled);
        return $feature;
    }

    private function __construct($name, $isEnabled)
    {
        $this->name = $name;
        $this->isEnabled = $isEnabled;
    }

    public function getName()
    {
        return $this->name;
    }

    public function isEnabled()
    {
        return $this->isEnabled;
    }

    public function setNewName($newName)
    {
        $this->name = $newName;
    }

    public function enable()
    {
        $this->isEnabled = true;
    }

    public function disable()
    {
        $this->isEnabled = false;
    }
} 
```

我们也可以选择一个新名字，并决定启用或禁用它:这就是这里的`setNewName`、`enable`和`disable`方法的作用。最后，`fromNameAndStatus`工厂方法让我们可以在不使用构造函数的情况下以最好的(也是最具表现力的)方式创建我们的对象。

让我们看看 [`FeatureManager`](https://github.com/francescomalatesta/laravel-feature/blob/master/src/Domain/FeatureManager.php) 级。我们在特性上做的每一个操作都是从这里开始的(我们很快就会看到如何开始)。它与`Feature`对象和实现`LaravelFeature\Domain\Repository\FeatureRepositoryInterface`的类的一个实例一起工作。

让我们打开它。

```
<?php

namespace LaravelFeature\Domain\Repository;
use LaravelFeature\Domain\Model\Feature;
use LaravelFeature\Featurable\FeaturableInterface;

interface FeatureRepositoryInterface
{
    public function save(Feature $feature);
    public function remove(Feature $feature);
    public function findByName($featureName);
    public function enableFor($featureName, FeaturableInterface $featurable);
    public function disableFor($featureName, FeaturableInterface $featurable);
    public function isEnabledFor($featureName, FeaturableInterface $featurable);
} 
```

这个概念很简单:如果我们的`FeatureManager`使用一个接口而不是一个具体的类，我们可以绑定任何我们想绑定的东西，而不用担心其他任何事情。这是一个很好的实践，尤其是当我们正在开发一个包的时候:我们能做的最好的事情就是给我们的开发者最大可能的灵活性。

领域逻辑到此为止。

## 进入代码:实现

### 仓库

既然我们已经完成了域逻辑，是时候切换到实现了。让我们从我们将“绑定”到我们刚刚看到的接口的具体类开始。

[EloquentFeatureRepository](https://github.com/francescomalatesta/laravel-feature/blob/master/src/Repository/EloquentFeatureRepository.php)是将实现[FeatureRepositoryInterface](https://github.com/francescomalatesta/laravel-feature/blob/master/src/Domain/Repository/FeatureRepositoryInterface.php)的类。Laravel 默认使用口才，所以提供一个默认的方法来使用口才作为基础绝对是一个好主意。

我们不打算深入仓库的细节:它做一些非常基本的事情。让我们看看如何绑定到服务提供者。服务提供者是在我们的服务容器(和应用程序)中注册新绑定的最佳位置。如果你经常使用 Laravel，你可能在`config/app.php`文件中为 99%安装的包注册了一个服务提供商。对于我的包， [FeatureServiceProvider](https://github.com/francescomalatesta/laravel-feature/blob/master/src/Provider/FeatureServiceProvider.php) 是被选中的。

神奇的事情发生在`register()`方法中:

```
...
$config = $this->app->make('config');

$this->app->bind(FeatureRepositoryInterface::class, function () use ($config) {
    return app()->make($config->get('features.repository'));
}); 
```

`FeatureRepositoryInterface`被绑定到`EloquentFeatureRepository`类(它是配置文件中`features.repository`项的默认值)。

哦，关于那个…

### 配置文件

创建 Laravel 包的另一个好的实践是提供一个配置文件，这样最终的开发人员可以发布并使用它来定制包，以更好地满足他们的业务需求。要将配置文件添加到包中，我们所要做的就是为它选择一个位置。使用一个描述性的名称是最好的选择:这里是我为包添加的[。](https://github.com/francescomalatesta/laravel-feature/blob/master/src/Config/features.php)

在我们之前创建的服务提供者中

```
$this->publishes([
    __DIR__.'/../Config/features.php' => config_path('features.php'),
]); 
```

call 确保在发布供应商资产时，我们也将在主项目的`config`文件夹中获得一个闪亮的`features.php`文件。

此外，另一个调用

```
$this->mergeConfigFrom(__DIR__.'/../Config/features.php', 'features'); 
```

让开发人员选择覆盖什么，保留什么是完美的。更少的自举代码要写，更快乐的开发者！

现在让我们回到服务提供商。我们还可以看到对几个私有方法的两次调用:`registerBladeDirective`和`registerConsoleCommand`。让我们从第一个开始，介绍…

### 刀锋指令

检查某个功能是否启用的刀片指令可能是个好主意。它的实现非常简单:在`registerBladeDirective`方法中的一条指令。

```
private function registerBladeDirective()
{
    Blade::directive('feature', function ($featureName) {
        return "<?php if (app('LaravelFeature\\Domain\\FeatureManager')->isEnabled($featureName)): ?>";
    });

    Blade::directive('endfeature', function () {
        return '<?php endif; ?>';
    });
} 
```

通过这些简单的`directive()`调用，我们现在可以在我们的刀片模板中使用`@feature`和`@endfeature`。如果系统中启用了作为参数传递的功能，将显示块中的代码。否则，它将被隐藏。

顺便说一下，将这些代码放在服务提供者中并不总是一个好主意。在这个具体的例子中，我们只讨论了几个电话。如果背后有更复杂的逻辑，可以考虑用一个单独的类来实现，或者如果你想让开发人员决定必须启用哪些包特性，可以用一个单独的提供者。

### 控制台命令

现在，最后一部分:扫描我们项目的所有视图以找到`@feature`指令并使用`FeatureManager`自动将这些特性添加到系统中的命令怎么样？我们将使用一个控制台命令来实现它:让我们看看如何在一个包中注册一个新的。

下面是`registerConsoleCommand`方法中的内容。

```
private function registerConsoleCommand()
{
    if ($this->app->runningInConsole()) {
        $this->commands([
            ScanViewsForFeaturesCommand::class
        ]);
    }
} 
```

我们在这里做的事情很简单:当“这个应用程序在控制台中运行”时，将`ScanViewsForFeaturesCommand`命令添加到开发人员可以运行的命令列表中。

除此之外，这里真的没有什么特别可看的。一个非常线性的过程。然而…还是少了点什么，对吧？

### 正面

哦耶！*门面*还是不见了！给我们的开发人员一个*门面*也意味着给他们一个很好的工具来提高软件包的采用。

让我们构建它，并将其放置在[一个合适的位置](https://github.com/francescomalatesta/laravel-feature/blob/master/src/Facade/Feature.php)。

```
<?php

namespace LaravelFeature\Facade;

use LaravelFeature\Domain\FeatureManager;
use Illuminate\Support\Facades\Facade;

class Feature extends Facade
{
    /**
     * Get the registered name of the component.
     *
     * @return string
     */
    protected static function getFacadeAccessor()
    {
        return FeatureManager::class;
    }
} 
```

我们正在将*外观*绑定到`FeatureManager`类。这意味着如果开发者将它添加到`config/app.php`，他们可以调用

```
Feature::enable(); 
```

而不是创建一个`FeatureManager`类的实例，然后在其上调用`enable()`。相当大的进步(不是每次，但我们今天不打算报道[的正面戏剧](https://twitter.com/taylorotwell/status/838823537309777921/photo/1?ref_src=twsrc%5Etfw))！

## 建议

让我们以一些普遍适用的建议来结束这篇文章:

*   使用 git 标签来定义包的版本。推送到 Github 后，应该可以在 Packagist 上看到新版本。
*   记得仔细检查`composer.json`文件中的依赖关系。当使用 Laravel 的一些组件时，您必须确保包含正确的`illuminate/`包作为依赖项。在我的例子中，我使用了来自`illuminate/database`和`illuminate/support`的组件。
*   Laravel 生态系统为[提供各种帮助测试](https://www.sitepoint.com/laravel-dusk-intuitive-and-easy-browser-testing-for-all/)的软件包。当然，PHPUnit 已经做了很多事情，但是像`mockery/mockery`和`orchestra/testbench`这样的包可以帮上大忙。
*   尽可能多地编写文档。如果我们想看到我们的包在网络上传播，第一步是解释它的所有可能性。写例子是好事，解释理论和概念(必要时)是好事。用一口流利的英语做就更好了！

## 结论

这是我开发 Laravel 包时遵循的工作流程——你呢？你有过其他想分享的包开发经验吗？请在下面留下评论——让我们一起做一个好的包构建工作流程！

## 分享这篇文章