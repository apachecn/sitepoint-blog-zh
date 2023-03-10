# 快速提示:用版本扫描确保你的 PHP 版本是安全的

> 原文：<https://www.sitepoint.com/quick-tip-make-sure-php-version-safe-versionscan/>

有一个工具可以用来检查你的 PHP 版本是否有最多的错误修正。这个工具的版本是扫描，最近发布了 1.0 版本。

这个快速技巧将向您展示如何将它安装到您的环境中，以便可以从任何文件夹访问它，让您在未来的任何时间调用它，而实际上不需要在 PHP 项目中使用它。

## 步骤 0–可选–家园改善

接下来，您最好建立一个 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)实例。它是一个可以让你在五分钟内启动并运行 PHP 5.6+的虚拟机，所以你可以马上测试我们在这里做了什么。

## 步骤 1–Composer 全局安装

当一个工具是由 Packagist 托管的 Composer 项目时，为了使它可以被全局访问，我们用全局标志来安装它。进入您的虚拟机(`vagrant ssh`)并执行以下命令:

```
composer global  require psecio/versionscan:dev-master
```

这会将软件包全局安装到 Composer 的主文件夹中。在 Homestead Improved(见步骤 0)上，Composer 已经在全球范围内安装，可以从任何文件夹调用。如果您没有从第 0 步开始使用 VM，请先安装 Composer，然后继续这一步。

除非您全局安装 Composer，否则您可能必须像这样运行命令:`php composer.phar global require ...`

## 步骤 2–运行版本扫描

```
versionscan scan
```

输出应该是这样的:

![](img/2d7063fa36b27203e7e5bde1ca5f2ea5.png)

## 就是这样！

这就是全部了。现在，您已经安装了一个随时可访问的关键问题扫描程序。除了纯粹的意识，这个工具帮不了你什么，但是知道肯定比不知道好。

## 分享这篇文章