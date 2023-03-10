# Parallels Plesk 12:增强您的 WordPress 站点

> 原文：<https://www.sitepoint.com/plesk-12-wordpress-toolkit/>

*本文由 [Parallels](http://sp.parallels.com/?utm_source=sitepoint&utm_medium=blog&utm_campaign=advert&utm_content=parallels) 赞助。感谢您对使 SitePoint 成为可能的赞助商的支持！*

Web 管理工具并不新鲜，它们已经存在很多年了，它们都支持普通 web 应用程序的一键安装。然而，在大多数情况下，所有这些只是安装应用程序，然后你就只能靠自己了。一旦安装了这些应用程序，您就无法管理它们。

如果你考虑到用户喜欢安装主题和插件，管理会变得更加有趣。这个问题的解决方案通常涉及第三方服务来集中管理我们的 WordPress 站点。如果这些管理功能被内置到我们的主机控制面板中不是很好吗？好吧，有了最新版本的 Plesk，现在这是可能的。

[Parallels Plesk](http://www.plesk.com/?utm_source=sitepoint&utm_medium=blog&utm_campaign=advert&utm_content=plesk) 是市场上领先的主机控制面板和自动化平台之一。如果你使用过一些主机服务提供商，你很有可能使用过 Plesk。

在这篇文章中，我将带你了解如何使用 Plesk 12，重点是与 WordPress 专业人士最相关的特性，主要是 WordPress 工具包。我还会谈到那些管理多个 WordPress 站点的人感兴趣的其他一些领域。

## Plesk 12 中 WordPress 工具包的第一印象

当我第一次登录时，很明显最新版本的 Plesk 非常迎合 WordPress 开发者和管理员。

虽然支持其他网络应用程序，但 WordPress 特有的功能令人印象深刻。Plesk 12 引入了他们所谓的' **WordPress Toolkit** ,它将专业的 WordPress 管理特性带到了主流的 web 主机控制面板中。这包括检测手动安装、创建新安装(控制各种配置选项)、执行批量更新和管理插件/主题的能力。

除了 WordPress 管理功能，如果你想跳转到一个特定的 WordPress 仪表盘，在 Plesk 界面的大多数地方通常都有一个直接链接。也就是说，您无需离开 Plesk 就可以轻松执行日常任务。

功能概述:

*   安全性
*   更新管理
*   插件管理
*   主题管理
*   CloudFlare 的 ServerShield
*   ModSecurity
*   失败 2 班
*   出站反垃圾邮件
*   版本范围

## 使用 WordPress 工具包安装 WordPress

安装 WordPress 很容易，使用 WordPress 工具包更容易。

要安装 WordPress，请登录 Plesk 并进入“应用程序”页面。在这个页面上，你可以通过右上角的下拉框选择安装 WordPress。第一个选项是“安装”,这将运行一个默认的 WordPress 安装。

![Installing WordPress](img/2abace9f0971bfe7b98ed69c8cf70fa4.png)

第二个选项是“安装(自定义)”。此选项将为您提供对常见配置选项的更多控制。

![Install WordPress with Custom Options](img/f1dc01e38fcfb84a30befced46c27f8c.png)

使用此选项，您将能够选择安装路径、更新设置和管理员访问权限。

![Custom Options Part 1](img/c6ebd3206986ba215c288b7497a187e2.png)

再往下，你会发现你的标准 WordPress 配置选项，比如你的网站名称、管理员电子邮件地址、语言和数据库细节。

![Custom Options Part 2](img/f1e2b72781648a016faaf5e9021cd6a8.png)

安装完成后，您将看到如下所示的消息:

![Installing WordPress on Plesk 12 Complete](img/36ba930946250f96faf109f22dc7da2f.png)

## 安全性

Plesk 12 还包括同类最佳的安全控制，既有以 WordPress 为中心的选项，也有传统的网络安全工具。

### 检查安全性

这个功能将允许你进行安全检查，以确保 WordPress 已经被正确配置，并且一般的安全措施已经到位。用户通常会安装插件来达到相同的效果，但现在这在 Plesk 中是原生可用的。

### 如何使用“检查安全性”功能

有两种方法可以使用该功能。第一个在您登录 Plesk 时出现。在“网站&域名”下，你会在 WordPress 安装名称旁边找到一个标有“安全扫描”的按钮。

第二种方法是点击 WordPress 安装名称，在右边栏的“工具”菜单下选择“检查安全性”。

![The Security Scan Option in Plesk](img/5a149df50aecfe9e30020131a73b0af7.png)

![The Check Security Option in Plesk](img/95f54d338de85256d21d6948cabdeddf.png)

选择“安全扫描”或“检查安全”将显示如下所示的屏幕。第一次在新站点上运行时，您会看到一些警告，让您知道可以采取一些措施来加强您的安装。确保选择了这些选项，然后单击“安全”:

![WordPress Toolkit Security Check Before](img/9f78595763f19f0526b61fa85eb8fe25.png)

现在，如果您重新运行此扫描或检查，它将如下所示:

![WordPress Toolkit Security Check Before](img/2fb5ab82188cabdffb8776430dd27290.png)

你会注意到一些权限给了你“回滚”的选项，我可以看到这在故障排除时变成了一个真正的时间节省器。

按照上面的这些基本步骤，你已经极大地强化了你的 WordPress 站点。我经常看到安全插件被宣传为安全的银弹，然而，遵循“安全检查”中的基本最佳实践将提供更多的保护来抵御已知和未知的威胁。它还消除了对另一个插件的需求。

### 检测 WordPress 安装

WordPress 工具包还包括一个“扫描”功能，你可以用它来检测运行 3.4 或更高版本的 WordPress 站点。这允许你把一个安装附加到你的 WordPress 工具包网站上。

值得注意的是，Plesk 只知道通过 WordPress Toolkit 使用 Plesk 的应用程序安装程序(基于应用程序打包标准技术)创建的安装，或者在扫描过程中检测到的安装。建议你定期扫描你的客户网站，以便在 WordPress 工具包中管理它们。

![Security Scan or Security Check](img/ac34b7b1939b9b31362c66121c572217.png)

### 更改您的管理员用户名

我们都应该知道不要使用默认的“admin”作为管理员帐户，但是，如果我们继承了其他人的网站，可能会有这样的情况，你会遇到可怕的“admin”用户名。或者您可能只想更改管理员用户名。

有很多方法可以改变你的管理员用户名，大多数用户会使用插件来做这件事，或者创建一个新用户作为管理员，然后删除旧的“管理员”帐户。

使用 WordPress 工具包，这很容易管理，只需点击“管理”,如下所示:

![WordPress Toolkit Changing Admin Username Link](img/a0da9da9e25fb2be5bc7c16daaa5e6ba.png)

这将带您进入一个页面，您可以在其中指定新的管理员用户名。

![WordPress Toolkit Changing Admin Username](img/8f6df549b89aa4c4e5deaaf0198fbdd7.png)

### 安全码

安全性是 Plesk 平台的核心主题。在版本 12 中，有几个强大的工具被捆绑到“安全核心”中。这里有几个可用的工具，供那些想采取额外的步骤来强化他们的网站(应该是每个人！):

*   ModSecurity
*   失败 2 班
*   出站反垃圾邮件
*   CloudFlare 的 ServerShield

我们将在下面更详细地介绍这些工具。

## 更新管理

保持任何 web 应用程序的更新是至关重要的。WordPress 运行在互联网上 47.38%的可识别 CMS 上，它是攻击者的一个流行目标。WordPress Toolkit 的一个关键组件是能够在一个地方管理你所有的 WordPress 核心更新。

### 如何更新多个网站

在“网站和域名”标签下，选择右边栏的“WordPress”。这将显示你所有 WordPress 站点的列表。要运行单次或批量更新，请选择您想要更新的站点，然后单击“更新”按钮，这再简单不过了。在我的测试中，更新 WordPress 工作得非常完美。

![WordPress Toolkit Updates](img/52bd5c0d22da417f026b1cb195f40c45.png)

一旦更新安装完成，你会在屏幕右下角得到一个提示。

![WordPress Toolkit Update Complete](img/f280f6bc85acaa9ebd3d904c2d4feae9.png)

### 管理自动更新

当 WordPress 团队宣布转向自动更新时，我们大多数人都喜欢这个想法。虽然我个人并不建议关闭自动更新，但我可以理解为什么有些人喜欢自己控制更新。此外，像 4.0 这样的核心更新仍然需要手动更新，所以执行手动更新是我们都必须做的事情。

即使有一些方法来管理更新，比如编辑你的`wp-config.php`，或者安装一个插件，比如 [WP 更新设置](https://wordpress.org/plugins/wp-updates-settings/)，一旦你有了很多网站，你真的需要集中管理来让事情变得更容易。

要打开(或关闭)自动更新，只需打开你的 WordPress 安装上的“自动更新”开关。

![Managing Automatic Updates](img/ad2a1a41ee0aa75fb539abd7b90f3f94.png)

我喜欢尽早和经常更新，但是如果您的客户喜欢慢慢来，您至少可以很容易地查看他们在您的系统上运行的版本。

## 管理插件

一旦你控制了更新，插件可能是支持大量 WordPress 站点时最大的问题之一。

诸如性能、兼容性和安全性等问题经常与插件的选择联系在一起。Plesk 12 中的 WordPress 工具包带有一个管理插件的部分。你可以在“网站&域名”标签下进入这个区域，然后选择右边栏的“WordPress ”,接着是“插件”标签，它将显示已安装插件的全球列表。它看起来是这样的:

![Plesk WordPress Plugin List](img/3df8bc0bb6eb7822fcd54cd62138aef5.png)

在插件部分，您可以执行许多操作:

*   激活/停用
*   安装
*   删除
*   更新
*   搜索

如果你发现了不兼容或安全问题，搜索系统中所有插件的能力是很有用的。

如果你想管理特定安装的插件，在“WordPress 安装”标签下选择站点，然后在工具栏中选择“插件”:

![Plesk Plugin Management](img/b98377e049fa66688b72c4a22f368456.png)

开发或配置不良的插件通常是性能问题的原因。有了插件视图，你可以一键禁用插件或者安装一个更好的替代插件。

另一个强大的特性是批量安装插件的能力。要管理单个站点上的插件，请在“网站和域”选项卡中单击该站点，然后选择您要管理的站点旁边的“管理插件”。然后，您可以选择想要激活或不激活的插件。

![Bulk Install Plugins](img/9521363f38e8a06569fc8c115a3c36e9.png)

如果你想跨多个站点管理插件，请转到右边栏的“WordPress”来查看“WordPress 安装”页面。然后选择你想批量安装插件的站点，然后在工具栏中选择“插件”。

![Bulk Install Plugins](img/31ace43ae4025765137d009f6f532417.png)

如上所示，你可能想在你所有的站点上安装一个故障诊断插件，比如 [P3(插件性能分析器)](https://www.sitepoint.com/optimizing-wordpress-performance-with-p3/)来帮助你的客户识别常见的性能问题。你会注意到“安装后激活”被选中，这是可选的。

## 管理主题

类似于插件管理功能，你可以在“网站和域名”标签中看到所有已安装和活动主题的列表，点击右边栏的“WordPress”并选择 WordPress 站点:

![Plesk Theme Management](img/19b4aee0bb9f5208ce857c8e59a5483c.png)

或者点击“主题”标签:

![Plesk WordPress Theme Management](img/7fee1a3eaeff4d605638dff1c2d67a63.png)

我知道许多 WordPress 开发者喜欢移除默认主题，但是我喜欢保留默认主题来排除和隔离主题和主题/插件兼容性问题。当你有喜欢“尝试”的客户时，这一点尤其重要。

与上面的插件管理部分一样，在 Plesk 中安装、激活和停用主题的能力可以节省大量时间。

## 通过 CloudFlare 启用 ServerShield

ServerShield 是与 Parallels 和 CloudFlare 合作的成果，是 Plesk 12 的一项新的关键功能。

人们通常将 CloudFlare 与“仅仅是一个 CDN”联系在一起，毫无疑问，它绝对是一个世界级的 CDN，但是 CloudFlare 远不止于此。它们还提供了一系列与安全相关的功能，可以进一步锁定您的网站。

要启用 ServerShield，请选择侧边栏中的链接，如下所示:

![ServerShield Link](img/f1621377925a4cc8747885fc99f7f394.png)

ServerShield 直接在您的 Plesk 界面中为您的客户端站点提供了简单的 CloudFlare 和 StopTheHacker 集成，实现这两种服务再简单不过了。

![Plesk ServerShield](img/b456c769de59d8338a4f1d1bcda481af.png)

ServerShield 有两个主要组件:

*   云耀斑
*   StopTheHacker

### 云耀斑

CloudFlare 的安全平台非常全面，超出了本文的讨论范围，但以下是 CloudFlare 帮助您防范的一些威胁:

*   垃圾评论
*   SQL 注入
*   XSS
*   恶意和收割机器人

![Plesk CloudFlare](img/8e53d7c2ad941d9c9708157edfc67e23.png)

### StopTheHacker

StopTheHacker 提供对恶意软件和网络钓鱼黑名单(如谷歌的安全浏览列表)网站信誉的日常监控。它还提供了一些建议，告诉你如果你发现自己在列表中，该如何解决这个问题。在 Core Security 中，只需点击一下鼠标，即可为您或您的客户站点启用 StopTheHacker 监控，如下图所示:

![Plesk StopTheHacker](img/8e53d7c2ad941d9c9708157edfc67e23.png)

## ModSecurity

ModSecurity 是一个强大的网络应用防火墙，包含在 Plesk 12 的所有版本中。

Plesk 给你一个简单的界面来管理 ModSecurity 的行为。Plesk 12 的所有版本都包括来自 AtomiCorp 的高级 ModSecurity 规则。这意味着它们将由著名的 ModSecurity 规则提供商定期更新，以保护您免受各种最新的威胁。

除了 AtomiCorp，Plesk 的 ModSecurity 还附带了 OWASP 核心规则集(CRS)和 Comodo ModSecurity 规则集。OWASP 规则非常严格，可能会给 WordPress 带来问题，因此 Parallels 建议在这种情况下使用 Atomic 或 Comodo 的规则。

![Plesk ModSecurity](img/a74b7ba4156d4f14496c8b4604d60193.png)

ModSecurity 接口也有一些不错的地方，比如可以通过 ID、CVE 或正则表达式来关闭规则。如果你试图隔离一个问题，这是非常有用的，因为一些 ModSecurity 规则会导致误报。

如果你正在考虑强化你的站点，ModSecurity 是你想要确保你正在使用的东西(并且保持开启状态),所以值得花一些时间去了解它。

## 失败 2 班

Fail2Ban 是一个流行的应用程序，它在您的日志文件中查找各种服务的任何可疑活动，并阻止(或“监禁”)与该活动相关的 IP 地址。这对于自动阻止来自 IP 地址或网络的暴力攻击非常有用。Fail2Ban 应用程序还可以自动更改防火墙规则并发送电子邮件警报。

![Fail2Ban](img/747f7b90855cddc486d3e0f9eccc0f3d.png)

要配置 Fail2Ban，请转到“工具&设置> IP 地址禁止(Fail2Ban)(在安全组中)”并选择“启用入侵检测复选框”。然后，您可以配置禁止时间长度、攻击间隔以及 IP 地址被禁止之前的失败次数。

您也可以通过转到“工具&设置> IP 地址禁止(Fail2Ban)>可信 IP 地址>添加可信 IP”将可信 IP 地址列入白名单。这是有用的，这样你就不会意外地被阻止，或者如果你想在你的系统上执行你自己的安全检查。

## 出站反垃圾邮件

黑名单 IP 地址的问题甚至困扰着一些最大的玩家，当你在一个单一或小范围的 IP 地址上运行许多网站时，这是一个严重的问题。使用出站反垃圾邮件，您可以通过限制传出邮件来保护您的 IP 信誉。这是一个重要的功能，可以保护您的用户不会将他们的 IP 地址列入黑名单。

![Plesk Outbound Antispam](img/d3f7f59b0428409d8c295ae0bef007e2.png)

## 选择正确的版本

Plesk 有四种风格，从托管少量站点到专业托管提供商。可用的版本有:

*   网络管理版
*   Web 应用程序版本
*   网络专业版
*   网络主机版

Plesk 12 的所有版本都包含 WordPress 工具包。它是 Web PRO 和 Web HOST 版本的标准配置，也是 Web ADMIN 和 Web APP edition 的可选附件。

Parallels 整理了各种 Plesk 版本的比较图表[以帮助您选择正确的版本。](http://www.plesk.com/#editions?utm_source=sitepoint&utm_medium=blog&utm_campaign=advert&utm_content=editions)

## 摘要

随着 WordPress 越来越受欢迎，我们部署的网站数量也在不断增长，任何有助于我们自动化和简化所有网站管理的功能都是至关重要的。

除了我在这篇文章中强调的，Plesk 还有很多东西，我只介绍了 WordPress 特有的好东西。Plesk 12 率先加入了专业的 WordPress 管理功能，这是我见过的所有主机控制面板中最好的。

[点击这里](http://www.plesk.com/#demo?utm_source=sitepoint&utm_medium=blog&utm_campaign=advert&utm_content=demo)亲自体验 Plesk 12 演示。或者如果你已经在使用它，我很乐意在下面的评论中听到你的想法。

## 分享这篇文章