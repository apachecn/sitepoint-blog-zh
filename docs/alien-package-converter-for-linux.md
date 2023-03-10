# 用于 Linux 的外来包转换器

> 原文：<https://www.sitepoint.com/alien-package-converter-for-linux/>

最近，我偶然发现了一个非常有用的工具，它是我的系统管理工具集中非常缺少的。Alien package converter 是一个强大的小应用程序，可以在 rpm (Red Hat)、dpkg (Debian)、stampede slp 和 slackware tgz 文件格式之间进行转换，这是我在 Linux 杂志上的一篇 Jeremy Garcia 文章中发现的。

我能想到在生产环境中我需要这样的东西有几十次了，我确信我会很好地利用它。没有什么比没完没了的搜索只是为了找到一个你需要的应用程序的错误包格式更令人沮丧的了！

我会非常注意网站上的警告，不要将 Alien 用于核心系统文件和库。

然而，对于那些出于不同目的可能有不同发行版的人来说，这可能是一个在您的服务器上更新软件的生产力实用程序。

这里有一个关于从源代码中进行构建的论点。虽然这肯定是最初打算编译的应用程序的数量，但除了全职系统管理员之外，我不知道有多少专业人员有时间查看每一个“自述文件”和“安装文件”,并使用自定义配置从源代码构建应用程序。

这不是缺乏激情，而是需要他们的时间。毕竟，时间就是金钱。

在“双击安装”的时代，使用软件包安装程序和工具(如 Alien)可以节省宝贵的时间，其中一些时间可以用于定制底层配置文件。这也使得来自非 Linux 操作系统环境的迁移更加容易。

## 分享这篇文章