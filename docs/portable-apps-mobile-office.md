# 便携式应用程序:台式电脑的移动办公室

> 原文：<https://www.sitepoint.com/portable-apps-mobile-office/>

虽然可移植的应用程序已经存在很长时间了，但是直到最近我才意识到拥有一个完整的可移植的 web 开发环境是多么的有用。最棒的是，这样的环境可以快速、轻松、免费地创建。你所需要的只是一个便携式驱动器(我用的是 4GB 的 USB 闪存驱动器，现在你可以用不到 30 美元买到)和一台电脑。

出于本教程的目的，我选择使用 Windows 操作系统，并且只使用开源应用程序。

你有 15 分钟时间吗？如果是这样，拿起你的移动硬盘！离完全可移植的 web 开发环境只有四步之遥。我们开始吧！

##### 变得便携

![Figure 1: The PortableApps Suite](img/e6831626a439beed9128dfe9464895fd.png)

首先下载并安装[便携应用套件(基础版)](http://prdownloads.sourceforge.net/portableapps/PortableApps_Suite_Base_1.0.exe?download)。这个程序给你一个友好的前端，并使安装在我们的闪存驱动器上的其他便携式应用程序变得轻而易举。

##### 堆叠你的棍子

我们的下一步将是安装 web 开发环境的核心。我个人的选择是 XAMPP，它提供了所有的必需品。我们特别感兴趣的是:

*   阿帕奇(网络服务器)
*   MySql(数据库)
*   PHP(网络脚本)
*   PHPMyAdmin(数据库接口)

![Figure 2: The XAMPP logo](img/281ad6487d27fac0a2017add1dcaf471.png)

要安装 XAMPP 堆栈:

1.  [下载 XAMPP 建兴](http://portableapps.com/apps/development/xampp#download_details)。

3.  将此归档文件的内容解压缩到您的便携式驱动器的根目录下。

5.  [下载 XAMPP 发射器 1.2](http://downloads.sourceforge.net/portableapps/XAMPP_Launcher_1.2.paf.exe?download) 。

7.  启动 PortableApps 套件，然后单击选项。

9.  选择下载的文件。安装人员会处理剩下的事情。

![Figure 3: The XAMPP Control Panel](img/424907cae95ecd3873006c3c09e9b88c.png)

要测试堆栈，请执行以下步骤:

1.  打开 XAMPP 控制面板，如上图 2 所示。

3.  单击 Apache 和 MySQL 的开始按钮。

5.  启动您选择的网络浏览器，并在地址栏中输入`http://localhost/`。您应该会看到图 3 所示的 Windows XAMPP 起始页。

![Figure 4\. The XAMPP start page](img/7ba527d18bb8d51423f9c8b0871f94e3.png)

现在单击 Status 链接，它位于左侧栏中。您应该会在 PHP 和 MySQL 旁边看到一个绿色的“已激活”通知列表。

祝贺您——您的支持 PHP 的便携式 web 服务器和数据库已经启动并运行了！

*注意:不用于生产！*
*如果你打算用这种配置来驱动一个面向公众的网站，你可能要三思了。阅读 Apache Friends 网站上标题为[安全问题](http://www.apachefriends.org/en/xampp-windows.html#523)的部分，了解更多关于保护便携式 web 环境的内容。*

![Figure 5: The XAMPP Security Check](img/ea7b9e98470fa627dc44a0c97e824b9f.png)

##### 应该抢哪些 app？

现在我们有了一个 web 服务器、一个数据库和一个工作 PHP 环境，让我们访问 [portable apps 应用程序页面](http://portableapps.com/apps/)并下载更多的应用程序。至少，我喜欢随身携带一个执行代码语法高亮显示的文本编辑器、一个所见即所得的编辑器、一个 FTP 客户端、一个 web 浏览器和一个图形应用程序。以下项目符合我的要求:

*   [记事本++便携版](http://portableapps.com/apps/development/notepadpp_portable)
*   [共混笔记本](http://portableapps.com/apps/development/nvu_portable)
*   [Filezilla 便携式](http://portableapps.com/apps/internet/filezilla_portable)
*   [火狐便携版](http://portableapps.com/apps/internet/firefox_portable)
*   [GIMP 便携式](http://portableapps.com/apps/graphics_pictures/gimp_portable)

虽然有大量可移植的应用程序，但我选择了这些应用程序，因为它们为我们提供了完成工作的工具。最重要的是，它们中的每一个都可以从便携式应用程序网站下载，这意味着安装是轻而易举的。

一旦您下载了所有的应用程序，并通过 PortableApps 安装程序安装了它们，就该熟悉新的开发环境了。

##### 测试您的环境

既然我们已经有了可移植的开发环境(和一些工具),让我们继续创建一个简单的`helloworld.php`页面来测试这个棒是否符合要求。但是在我们开始编写脚本之前，让我们创建一个单独的文件夹来存放它。

*   浏览到`K:xampplitehtdocs`(用您的便携驱动器号替换`K:`)并创建一个名为`HelloWorld`的文件夹。
*   接下来，打开便携记事本++并复制以下程序:

```
<?php  

    Print "Hello World";  

?>
```

*   将文件作为`index.php`保存到您的`HelloWorld`目录中。
*   打开你的桌面浏览器(或 Firefox 的便携版)并进入`http://localhost/HelloWorld/`

如果您看到了“Hello World”消息，那么您已经准备好进入一些更有趣的可能性了，可移植 web 开发环境使之成为可能。

##### 便携式开发环境的用途

***使用#1:完整备份你所有的站点***

以不到 30 美元的价格购买 4GB USB 闪存驱动器意味着您可以将所有网站的备份放在口袋里！除了作为有用的数据备份之外，您永远不知道什么时候您可能想要对网站进行快速更新，因此使您的网站易于访问意味着您的备份可以成为一个开发环境。

***使用#2:快速试用一款开源 PHP 应用***

因为 XAMPP 堆栈的默认设置在安全性方面非常宽松，所以您可以使用它来轻松地测试 PHP 应用程序。有了你的便携式 PHP 环境，你可以查看最新最棒的应用程序。这些程序大多包含基于 web 的安装脚本，这意味着让它们运行起来通常就像将应用程序的脚本提取到 htdocs 目录下的一个文件夹中并浏览到该目录一样简单。

如果像 SugarCRM、Knowledge Tree、Alfresco、Joomla、WordPress 和其他领先的开源应用程序不在你的考虑范围内，那么是时候看看它们了。拥有一个便携的实验环境意味着你的主机保持干净，没有你正在测试的应用程序。

***使用#3:随身携带你的全部网络作品集***

虽然向潜在客户展示您通过启动 live 站点创建的众多站点之一可能会令人印象深刻，但如果您能让他们先睹为快，他们会印象更加深刻。如果你发现自己没有网络连接，你的演示不需要妥协。

***使用# 4:***(几乎)完美的测试服务器

你没有在网上编辑任何你客户的数据，对吗？对吗？？

有效的开发模型包括一台开发机器、一台测试服务器和一台生产服务器。像往常一样，将您的主要计算机用作开发机器，并通过将您的便携式 web 环境用作测试服务器来增加一层安全性。

一旦您测试了您的更改并确定一切正常，您就可以放心地将信息上传到实时服务器。更不用说你可以向你所有的客户吹嘘你有一个专用的测试环境。(没必要提，那时候它可能也在你的口袋里！)

##### 摘要

在本文中，我向您展示了如何为您的 Windows 机器创建一个可移植的开发环境。手头有一个可移植的开发环境可以带来技术和业务两方面的好处。考虑到像 PortableApps 这样的套件易于设置、易于使用，并且不需要任何成本(除了 USB 存储设备的成本)，您真的没有理由不在棒上为自己创建一个 web 开发环境！

## 分享这篇文章