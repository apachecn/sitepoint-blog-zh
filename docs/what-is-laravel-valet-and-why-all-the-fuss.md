# 拉勒维尔的男仆是什么，为什么这么大惊小怪？

> 原文：<https://www.sitepoint.com/what-is-laravel-valet-and-why-all-the-fuss/>

昨天，泰勒·奥特威尔[释放了拉勒维尔·代客](https://laravel.com/docs/5.2/valet)。

![Valet icon](img/c73f7b125812b354c5af4ded1161cf52.png)

如果你只是对它是什么，它是为谁准备的，以及它是如何工作的粗略描述感兴趣，请观看下面有趣的(有点 NSFW 的)视频。如果你想更深入地了解这款产品，了解它的更多细节，并了解它为什么会在 Reddit 和 Twitter 上引起如此大的争议，请在跳转之后继续阅读。

[https://www.youtube.com/embed/H3Z4Gk9Wc0s?rel=0](https://www.youtube.com/embed/H3Z4Gk9Wc0s?rel=0)

* * *

## 粗线条

代客是一个工具，使旋转演示或可丢弃的项目轻而易举。

这个工具结合了 OS X 上的一些默认软件和一些关于 PHP 应用程序的文件路径和服务的想法，以及一些不需要修改`/etc/hosts`文件的附加工具。此外，它允许在需要时通过公共互联网转发对本地应用的访问。

与流浪者或码头工人不同，它不提供隔离，让所有应用程序使用相同的全球可用软件。下面将解释为什么这一点很重要。

## 细节

让我们一个一个地深入细节。

### 仅限 OS X

贴身男仆目前只限于 OS X。这是目前新来者遇到代客的头号投诉。

虽然从技术上来说，考虑到两个系统共享的体系结构以及大多数 Linux 发行版预装了旧版本 PHP 的事实，它应该可以在 Linux 上运行，但如果没有完整的、特定于操作系统的重写，Windows 可能永远不会成为一个选项。

这不是阴谋。泰勒不属于苹果崇拜者，他也不想成为精英。为 OS X 开发这样的项目非常容易，因为已经预装了非常好的软件包管理器(自制软件)和合理的默认设置。

### 共享系统状态

由于不使用虚拟机或容器，Valet 依赖于 OS X 上安装的 PHP 版本。没有多少人知道这一点，但 OS X 实际上预装了 PHP，尽管这是一个公认的过时版本(在撰写本文时默认为 5.5.31)。虽然这个版本足以运行最基本的应用程序，但如果不使用最新的版本就太可惜了。

要使用家酿软件在 OS X 上更新 PHP7，首先[安装家酿软件](http://brew.sh/)，然后运行:

```
brew update
brew tap homebrew/dupes
brew tap homebrew/versions
brew tap homebrew/homebrew-php
brew install php70
brew cleanup 
```

最后，通过将下面一行添加到您的`~/.bash_profile`文件中，让 OS X 默认执行新的 PHP:

```
export PATH="/usr/local/bin:/usr/local/sbin:$PATH" 
```

然后，重新启动您的终端窗口或运行`source ~/.bash_profile`以使更改生效。

![PHP 7 running on OS X](img/c956a555c8a4f94b02ef63d795e5005f.png)

请注意，您还需要在全球范围内安装 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 才能让代客工作。

顺便说一下，这是代客的主要缺点之一，类似于使用 MAMP 或 XAMPP 和类似的工具——所有的应用程序将使用相同的数据库/ PHP 版本/扩展。这意味着对一个应用程序的依赖会毁掉其他应用程序对其他应用程序的依赖。小心开始与代客严重的项目！

### 自动虚拟主机

Valet 使用 [DnsMasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) 来模拟虚拟主机，它接受你用它创建的任何文件夹名称，并通过`{foldername}.dev`域访问应用程序。

例如，如果您在特定文件夹中运行`valet park`，Valet 将在该文件夹中搜索与其支持的项目设置相匹配的项目设置，并立即在带有`.dev`后缀的浏览器中访问它们。因此，在`~/my-sites/laravel`创建一个新的 Laravel 项目将使该网站在`http://laravel.dev`可用。

请注意，要实现这一点，您根本不需要触摸`etc/hosts`，这与使用流浪者的情况相反。

这使得预览您正在构建的演示应用程序变得非常非常简单，并且完全跳过了对 Apache 或 Nginx 配置的需求——这对于大多数人来说是一件非常头疼的事情。

*默认代客使用`.dev`。虽然没有广泛使用，`.dev`实际上是一个有效的顶级域名。但是，代客可以配置为使用不同的后缀。用 Composer 安装后(最好是全局安装)，进入它的主文件夹，找到文件`config.json`。那里会有一个值为`.dev`的`domain`键。把它改成类似于`.devlocal`的东西应该就可以了。*

### 开发/生产平价

如果你已经读过我的书，你可能会问“但是开发/生产奇偶校验呢？”

你有理由怀疑——这是一件大事。您以这种方式开发的环境与任何可能的生产环境都相距甚远，期望任何东西在开发期间以这种方式运行实际上是荒谬的。这就是为什么代客泊车服务只适用于:

*   演示/一次性项目
*   或者，人们**绝对确定**他们已经配置了他们的 prod 环境，以匹配在 Valet 下开发的应用程序的所有条件

后者是非常不可能的，所以我怀疑我们会看到许多严重的，用代客开发的长期项目。

### 供应商锁定(带警告)

目前，代客仅支持以下类型的应用:

*   拉勒维尔
*   [流明](https://www.sitepoint.com/building-micro-markdown-api-app-lumen/)
*   [Statamic](https://www.sitepoint.com/building-database-free-websites-with-statamic-cms/)
*   [飞船](https://www.sitepoint.com/introduction-to-craft-cms/)
*   [WordPress](https://www.sitepoint.com/wordpress/)
*   竖锯
*   [静态 HTML](https://www.sitepoint.com/building-an-spress-svbtle-theme-responsive-static-blogs/)

这是因为它依赖于一个特定的文件和文件夹布局，通过内置的 PHP 服务器来正确地存放东西。然而，Valet 确实支持定制驱动程序,它允许扩展工具和增加托管替代应用程序结构的新方法。

当您试图用它运行您的自定义应用程序并发现它不起作用时，请记住这一点！

### 实时自主预览

代客服务的一个主要卖点是可以轻松地与客户或其他开发人员共享正在进行的工作。在一个应用程序的文件夹中运行`valet share`,将会通过一个自动生成的链接让它在网上公开。要停止共享网站，只需用`Ctrl + C`停止服务即可。

在幕后，Valet 使用 [Ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) 来完成这项工作。

## 结论

当然，你也可以在自己的上使用 Valet 中的整套工具[，但在典型的 Laravel 风格中，许多更小、更有用的东西被放在一个方便的包中，以使开发和调试应用程序的整个过程更快。毕竟，如果 Laravel 本身不是一个极其实用的整体，那么它又是什么呢？](https://www.sitepoint.com/accessing-localhost-from-anywhere/)

有关 Laravel Valet 的更多信息，请参见[文档](https://laravel.com/docs/5.2/valet)。我们不会很快放弃我们的[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)而支持代客(跨平台友好性对我们来说更重要)，但是代客有明确的价值——从加速共享和自托管过程的便利工具到资源使用(只有大约 7MB 的 RAM！)，与 MAMP 或 XAMPP 等机构相比，代客有很多优势。

你会用它吗？

## 分享这篇文章