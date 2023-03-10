# 使用 Composer 脚本构建自动化

> 原文：<https://www.sitepoint.com/build-automation-with-composer-scripts/>

在 Alexander Cogneau 介绍了使用 Composer 进行[依赖管理之后，您现在知道 Composer 是一个用于管理外部项目依赖和版本约束的解析器。但这就是它的全部功能吗？在本文中，我将向您展示如何将 Composer 用作基本的构建自动化工具。](https://www.sitepoint.com/re-introducing-composer/ "PHP Dependency Management with Composer")

## 作曲家脚本

任何名副其实的构建自动化工具都必须能够编写一系列自动化任务的脚本——从构建、打包和运行测试套件，到在试运行和生产系统上部署。例如，Phing 基于 Ant，允许您在 XML 构建文件中定义这样的任务。

Composer 在这方面有所不同，因为它不假设这些任务是什么，或者是否要执行这些任务。Composer 所做的是在执行过程中暴露其预安装和后安装/更新/卸载事件挂钩，您可以使用“脚本”进行回调，这与 [Pyrus 提供通过`--plugin`选项在`package.xml`中为其`install`、`upgrade`和`uninstall`命令定义自定义命令](http://pear.php.net/manual/en/pyrus.plugins.php "Manual :: Creating plugins for Pyrus")的能力非常相似。

脚本属性是在根包的`composer.json`文件的根 JSON 对象中定义的。您可以定义任意数量的 PHP 静态方法(必须可以通过 Composer 的自动加载器机制自动加载)、命令行可执行文件或两者的组合。

然后在 Composer 的执行过程中调用这些脚本定义的任何自定义代码或特定于包的命令。需要注意的是，只有在根包的`composer.json`中定义的脚本才会被执行。Composer 不会执行根包的依赖项中指定的任何脚本。

在 Composer 执行过程中会触发以下事件:

*   `pre-install-cmd`–在执行安装命令之前发生
*   `post-install-cmd`–在执行安装命令后发生
*   `pre-update-cmd`–在执行更新命令之前发生
*   `post-update-cmd`–在执行更新命令后发生
*   `pre-package-install`–在安装软件包之前发生
*   `post-package-install`–安装软件包后发生
*   `pre-package-update`–在更新软件包之前发生
*   `post-package-update`–在更新软件包后发生
*   `pre-package-uninstall`–在卸载软件包之前发生
*   `post-package-uninstall`–卸载软件包后发生

这些是不言自明的，我想你会同意，美在于它的简单。但是为了说明，这里有一个根包`composer.json`的例子:

```
{
    "name": "MyProject",
    "description": "An example to demonstrate the use of Composer scripts",
    "version": "1.0.0",
    "require": {
        "php": ">=5.3",
        "ext-xsl": "*",
        "ext-imap": "*",
        "ext-gd": "*"
      },

    "autoload": {
        "psr-0": {
            "MyProject": "src/"
        }
    },

    "scripts": {
        "pre-install-cmd": "MyProject\Installer::preInstall",
        "post-install-cmd": [
            "MyProject\Installer::postInstall"
        ],
        "post-package-install": [
            "MyProject\Installer::postPackageInstall",
            "phpunit -c /tests",
            "./bin/install.sh"
        ]
    }
}
```

我已经为`pre-install-cmd`、`post-install-cmd`和`post-package-install`事件定义了一些脚本。如您所见，我们可以定义静态 PHP 方法和命令行可执行文件的任意组合。在`post-package-install event`的例子中，它还执行一些单元测试和一个定制的安装脚本。

下面是我们的示例脚本:

```
<?php
namespace MyProject;
use ComposerScriptEvent;

class Installer
{
    public static function preInstall(Event $event) {
        // provides access to the current ComposerIOConsoleIO
        // stream for terminal input/output
        $io = $event->getIO();
        if ($io->askConfirmation("Are you sure you want to proceed? ", false)) {
            // ok, continue on to composer install
            return true;
        }
        // exit composer and terminate installation process
        exit;
    }

    public static function postInstall(Event $event) {
        // provides access to the current Composer instance
        $composer = $event->getComposer();
        // run any post install tasks here
    }

    public static function postPackageInstall(Event $event) {
        $installedPackage = $event->getComposer()->getPackage();
        // any tasks to run after the package is installed?
    }
}
```

当每个事件被触发时，Composer 的内部事件处理程序将一个`ComposerScriptEvent`对象作为第一个(也是唯一的)参数传递给每个回调。`Event`对象向您的回调公开其他 Composer 对象的以下 getters:

*   `getComposer()`–返回`ComposerComposer`的当前实例
*   `getName()`–返回被触发事件的名称
*   `getIO()`–返回当前执行`ComposerIOIOInterface`的输入/输出流，以读取/写入控制台

您可以参考 [Composer API 文档](http://getcomposer.org/apidoc/master/index.html "Composer API")，了解每个方法签名和每个对象公开的其他方法——特别是`Composer`实例和`IO`接口。

虽然这个看似基本的实现可能没有 Phing 定义那么“强大”,但它的简单性掩盖了它令人难以置信的灵活性。它利用了您现有的 PHP 知识投资，通过一点创造力和想象力，您可以使用 Composer 的依赖解析器和原生 PHP 脚本来创建一些相当复杂的构建和拆卸任务。您甚至可以[将它集成到 Jenkins](https://www.sitepoint.com/continuous-integration-with-jenkins-1/ "Continuous Integration with Jenkins, Part 1") 中进行持续集成。

## 摘要

在本文中，我介绍了如何使用 Composer 脚本来执行构建自动化的基本示例。这些任务可以根据您的需要简单或复杂，因为它们利用了您现有的 PHP 知识投资。希望这篇文章能启发您使用 Composer，而不仅仅是依赖管理。有关如何使用作曲脚本的更多信息，请参见[getcomposer.org/doc/articles/scripts.md](http://getcomposer.org/doc/articles/scripts.md "Composer")。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章