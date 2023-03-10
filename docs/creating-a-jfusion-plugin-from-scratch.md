# 从头开始创建 JFusion 插件

> 原文：<https://www.sitepoint.com/creating-a-jfusion-plugin-from-scratch/>

## 场景

所有企业在创建动态网站时都会面临一个问题，无论是第一次还是为了拓展新业务，都是使用什么软件。每个人对自己想要的内容形式和用户交互系统都有一个模糊的想法，但是从来没有一个绝对的解决方案适合所有人。

## “无限延伸”的局限性

为了克服这个问题，最常见的方法是拥有一个平台，然后构建或扩展它以满足需求。使用 Joomla 或 WordPress 这样的软件，这可以通过插件/扩展来实现。即便如此，一些企业可能需要的复杂功能可能无法作为插件提供，并且可能需要在专有插件或扩展上进行大量投资，从而花费大量资金来制作和维护。并且可能在功能上变得过时，或者对进一步的业务扩展造成问题。

当涉及到调整付费代码时，依赖于为现有的商业插件付费也会导致一些负面影响。从没有合法的选择，因为涉及到许可证，或者开发者没有提供一个直接的方法来实现你的调整，如果他们的插件过于简化，符合黑盒标准(服务于一个容易识别的目的，但没有人知道它实际上是如何工作的)。

## 结合“为特定目的定制”的软件

无限扩展的另一个选择是使用多个软件，每个软件服务于一个特定的目的，并且只依赖于为你需要提供的不同在线环境使用不同的软件。这种方法的唯一问题是保持数据完整，这在两个软件中很可能是相同的，并且从一个软件到另一个软件的过渡也很平稳。

特别是，网站所有者认为无缝的主要领域有:

*   主题/模板
*   用户凭据和个人信息
*   通用用户操作

主题对于较小的公司来说可能是一个问题，因为雇佣某人来制作与他们的设置兼容的主题通常太贵了。已经受雇的设计师可能不具备所需的专业知识来克隆一个适用于特定平台的主题。

## JFusion 的出现

截止到本文，JFusion 是 Joomla 最流行的用户集成扩展，它通过同步用户数据，将用户与来自其他 PHP 软件的 Joomla 无缝集成。该扩展使用自己的插件系统(称为 JFusion plugins ),可以创建该系统来支持任何其他软件，只要你能弄清楚其他软件的内部工作方式。最有可能的是通过查看文档、代码注释和老的黑客技术。对于希望将 Joomla 用户与其他软件捆绑在一起，而无需担心从零开始创建自己的整个框架的开发人员来说，这个扩展是开源领域中最好的，将为您节省一生的编码时间。

[![](img/c86dd578340af659090d76b7e2d34d00.png "screenshot_2")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_2.png)

这个扩展提供的用户集成实现是你能从其他软件中找到的最常见的，你现在能为你创建的任何插件提供:

*   双重登录(SSO)
*   双重注销
*   双重注册
*   模板集成
*   活动集成
*   User sync
*   删除用户的多个实例
*   从用户的多个实例中更改密码

## 安装 JFusion

将 JFusion head 安装到 [JFusion 网站](http://jfusion.org)。在那里你可以直接下载。

[![](img/6ad6f43a24c38ccb61fd2fe1adaf1a7c.png "screenshot_3")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_3.png)

准备就绪后，前往您的 Joomla 站点(最好是一个测试服务器，而不是产品)，在 JFusion 插件的开发阶段，您可以在这里轻松地进行测试。请务必准备好您的管理员证书，并前往 Joomla(http://yoursite.com/joomla/administrator)的管理员页面登录。

[![](img/5df9c7a2d214a10f01e658a3e4dda36c.png "screenshot_4")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_4.png)

登录后，单击扩展管理器徽标(下面的图标)或从主菜单中选择它，方法是转到扩展，然后选择“扩展管理器”。

[![](img/871b59f5d377727614d1d56a17ad2744.png "screenshot_5")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_5.png)

这将把你带到扩展管理器页面，这里有几个安装扩展的选项。寻找“上传包文件”，在这个框中是一个上传字段，用于添加和安装 Joomla 扩展。无需解压缩您刚刚下载的档案，单击“选择”并找到 jfusion 下载并确认上传。

[![](img/e94fb6c4067856f73277cd231c7b769b.png "screenshot_6")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_6.png)

文件上传后，您应该会看到一个新页面，在屏幕顶部有一个蓝色的安装确认消息，类似于下面的屏幕截图。

[![](img/965e3abbe2b84088ebc0ee5492fb5f27.png "screenshot_7")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_7.png)

除此之外，您应该会看到该插件所依赖的已安装组件的列表(见下面的屏幕截图)。确保列表上的每一项都对应一个勾号，以确认该项安装成功。如果任何项目旁边有叉号，请重新安装，或在 JFusion 网站上请求支持。

[![](img/6f2199af8f09a0c9a8d44b2e4c0c48cf.png "screenshot_8")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_8.png)

现在所有东西都安装好了，你可以配置主插件了。这需要进行配置，以允许所有其他插件运行，并在触发其他软件上的效果之前，分配用户操作发生在哪些软件上。

## 配置主插件

要配置主插件，请从 JFusion 菜单(组件菜单> JFusion > Joomla 选项)中选择“Joomla 选项”，或者转到组件> cPanel 并选择“Joomla 选项”。

[![](img/b0861c355cf4239758ca5ff5365b4588.png "screenshot_9")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_9.png)

这将把你带到一个页面，允许你编辑(当前的)唯一主插件(主插件适用于当前的 joomla 安装)的配置，由于框架的当前限制，该插件可以在 JFusion 中启用。

默认情况下，由于这些设置，JFusion 可以自动计算出在这个阶段您实际上不需要为站点配置任何特殊要求，您可以简单地单击“应用”或“保存”,插件现在应该准备好启用了。

从 JFusion 菜单中选择“配置插件”。在这个页面上，您将看到一个表格，其中列出了您可以在 JFusion 中默认使用的插件。在这一阶段，除了带有 joomla_int 插件(joomla 内部插件，我们刚刚使用的插件)的那一行，所有的框都应该是灰色的。该行应主要在每个框中用红叉和几个灰色框填充，只有一列标有“状态”的除外，如果自动检测到配置，该列应包含一个绿色勾号，旁边写有“良好配置”字样。

单击“joomla_int”行上对应于“Master”列的红色十字，您现在应该会看到一行类似于以下屏幕截图的配置设置:

[![](img/b291a3e05851d80310797dc322e00648.png "screenshot_10")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_10.png)

如果你这样做了，JFusion 现在就可以在你的站点上工作了，你可以简单地选择任何你想要配置的预先开发的插件作为 Joomla 站点的从属插件，或者开始工作在你的插件上，这两个我都将涉及。

## JFusion 默认提供什么

JFusion 的当前开发者和贡献者已经创建了插件，这些插件默认包含在扩展中(但没有配置)。

从 JFusion 1.6 开始，当前的插件可用于不同的软件，您可以用它来设置 JFusion，而无需在几分钟内编写任何代码。这些插件文件夹(里面有插件文件)可以在 Joomla/administrator/components/com _ jfusion/plugins 文件夹中找到。

[![](img/94fb490ee912c1b212ca0765b9b4ef56.png "screenshot_11")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_11.png)

*   组织者

*   eFront

*   Elgg

*   画廊 2

*   外部 joomla 站点(将用户从一个 Joomla 同步到另一个 Joomla)

*   内部 Joomla 站点(重要:这允许在 Joomla 中进行 JFusion 操作)

*   Magento

*   MediaWiki

*   它可以帮助教育工作者创建高效地在线学习社团。

*   MyBB

*   OSCommerce

*   phpBB3

*   预车间

*   SMF

*   SMF2

*   vBulletin

*   WordPress

JFusion 第三方插件论坛上还有其他可用的插件，这些插件是由您应该检查的人创建的，这样您就可以看到您想要集成的东西以前是否还没有开发过。

如果作者选择，这些插件只限于来自作者的支持，并不保证插件是功能性的、无 bug 的或安全的。尽管如此，选项总是存在的，大多数第三方插件如 JFusion 都是开源的，可以免费修改或贡献代码。

## 创建你自己的插件

在编写插件之前，你首先需要考虑你要实现什么。JFusion 允许您同步和处理各种用户数据，但是您不一定需要提供 JFusion 框架提供的所有不同功能。你只需要整合你或其他人会用到的方面。

## 如何创建空白插件

每当你为 JFusion 创建一个新的插件时，你必须总是使用下面的结构，确保完全按照规定模仿文件名。这个文件夹和包含的文件位于目录 Joomla/administrator/components/com _ jfusion/plugins/:

文件夹

您的插件名称

(用你的软件的实际名称替换你的插件的名称，使用上面的样式，只有字母，小写，没有空格或特殊字符)。

## 文件夹内部

在该文件夹中，创建 5 个具有特定文件名和文件类型扩展名的空白文件，如下所示。这些文件以及它们所在的文件夹对于任何插件来说都是绝对必需的。

[![](img/f48c2ecf5bfe32d737bdfe183338d419.png "screenshot_12")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_12.png)

admin.php

这包含特定于 JFusion 管理的功能，以及您的插件，比如删除用户和配置集成的各个方面。

auth.php

这包含处理格式化数据并为身份验证过程做准备的函数，身份验证过程是在使用自定义数据加密方法的其他平台上工作时需要进行的。

index.html

简单地说，一个静态的 html 文件，其中包含一些非常基本的文本，可以防止人们进入您的开发目录。

jfusion.xml

现在，在很多 web 软件中使用的一种常见做法是提供一个 xml 文件，其中包含关于插件本身的文档，比如作者、创建日期等等。它还包含插件配置页面的模板信息，这将在后面解释。

public.php

一个包含函数集合的文件，可以在这个插件的任何其他函数中使用 php 文件
(一个包含文件)。

使用者 php

该文件包含与您要执行的用户同步操作相关的功能。

您还可以在该文件夹中创建任意数量的任何类型的其他文件，并在您已创建的自定义文件上合并任何依赖关系，只需将它们包含在依赖它们的主插件文件中。

## 快速笔记

出于本教程的目的，我使用 eyeOS 作为我想与本教程中写的插件集成的软件，尽管这些教程中的方法和技术将适用于所有插件。

[![](img/fecee3d0520dfd08db4494014d4254cc.png "screenshot_13")](https://www.sitepoint.com/wp-content/uploads/2012/03/screenshot_13.png)

## 下一步是什么？

本教程是该系列的一部分，下周的另一部分将继续。

## 分享这篇文章