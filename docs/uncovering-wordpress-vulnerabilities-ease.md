# 轻松发现 WordPress 漏洞

> 原文：<https://www.sitepoint.com/uncovering-wordpress-vulnerabilities-ease/>

作为一名开发人员或专业设计人员，在 WordPress 上构建你的网站的最大好处之一是，在大多数情况下，你是在一个久经考验的平台上构建你的代码，这个平台已经随着时间的推移得到了加强。不幸的是，说到安全，没有一个完全防黑客的系统。幸运的是，当涉及到保护你和你的客户系统时，有一些 WordPress 漏洞扫描器可以帮助你在错误失控之前发现它们。

值得注意的是，虽然本指南主要面向 WordPress.org 用户，但这些技术也适用于 WordPress.com 用户。对于那些不熟悉这两种产品之间差异的人来说， [Sitepoint](https://www.sitepoint.com/wordpress-com-vs-wordpress-org/) 有一个指南来澄清这些差异。WordPress.com 的用户在使用这些工具时，电量会减少，但从技术上来说，它们仍然可以工作。

尽管信任一般的在线扫描器是值得怀疑的，但一种新的开源安全工具允许开发人员和其他精通技术的专业人员轻松测试他们的代码是否被利用。虽然这些工具有一点学习曲线，但学习渗透测试工具的基础知识可以帮助您领先于大多数数字威胁。

## WordPress 特定工具

### WP 扫描

![WP Scan](img/b8428b0091214c822232a99e13bb740e.png)

WP Scan 是一个用于 Linux 和 Mac OSX 的开源工具，它是一把瑞士军刀，可以攻击几乎所有的 WordPress 安装。主要功能包括从 WordPress 数据库中提取用户名，扫描指定网站正在使用的插件，以及查看服务器上安装了哪些主题。WP Scan 还集成了已知的漏洞数据库，因此该软件可以过滤结果，只显示易受攻击的代码。

虽然 WP Scan 是一个强大的工具，但是如果你的系统上还没有安装 Ruby，安装过程会很困难。这非常适用于 CentOS 系统——许多主机的默认 Linux 发行版——因为操作系统没有所有必需的库。幸运的是，通过使用 Ubuntu 或 MacOSX，你可以大大简化这个过程。如果你是一个完全的 Linux 新手，WP Scan 预装在多个以安全为中心的 Linux 发行版上，可以在项目网站上找到列表。

### Plecost

![Plecost](img/9d397b8caf021f03b28e956765a023ba.png)

Plecost 是一个开源的 WordPress 指纹识别工具，它可以分析安装在指定 WordPress 系统上的插件，以及通用的 WordPress 漏洞和暴露(CVE)代码。由于 Plecost 是一个 Python 脚本，安装它就像将文件添加到您的服务器上，然后按照项目网站上的说明进行操作一样简单。

虽然这个工具仅限于显示已安装插件中的漏洞，但 CVE 代码集成使 Plecost 成为一个值得注意的工具，因为它为用户提供了关于如何利用服务器上过时软件的即时反馈。

由于 Plecost 是 Python 脚本的集合，安装相当简单，只要安装并配置了 Python，就可以在 Windows、Mac OSX 和 Linux/Unix 系统上运行该实用程序。

# 通用漏洞工具

虽然本指南主要集中在你的 WordPress 安装上，因为 WordPress 只是你的服务器的一个组件，知道如何使用通用渗透测试工具对于保护你的系统免受黑客攻击也是至关重要的。

### Nikto

![Nikto](img/6b4e62ba196c756d10e8748457a59c16.png)

Nikto 是一个通用的漏洞扫描器，可以扫描过时的软件、配置文件、隐藏目录等等。默认情况下，Nikto 旨在测试您自己的服务器，因为该工具运行迅速，可能会触发许多入侵检测系统的危险信号。如果需要的话，一个扩展可以让它变得更隐秘，但是对于你自己的服务器的基本测试，这可能是不必要的。

除了收集信息之外，Nikto 还可以强力验证目标网站的某些部分，从而确保您的网站用户遵循安全最佳实践。由于该工具可以在任何支持 Perl 的系统上运行，它几乎可以在任何 Linux 和 Unix 系统以及 MacOSX 上运行。Nikto 也可以配置为在 Windows 上运行，但是，这些系统需要安装 ActiveState Perl 或 Strawberry Perl。

### 维基托

![Wikto](img/edbb39f7a5101c16aaa6a84e48aa7bad.png)

Wikto 是一个主要为 Windows 环境设计的工具，由于其易用性，它在这个列表中的大多数工具中脱颖而出。虽然该计划是为 Windows 系统，它仍然包括强大的功能，如:模糊逻辑错误代码检查，后端矿工，谷歌辅助的目录挖掘和实时 HTTP 请求/响应监测。

这个工具的杀手锏是集中式的谷歌黑客集成。虽然这在技术上只不过是使用谷歌搜索来发现敏感信息，但 Wikto 允许您将已知查询的数据库导入到程序中，从而简化了这一过程。从那里，您可以自动运行对网站的查询，并以最少的努力查看结果。

## 掌握安全最佳实践

虽然安全是一个广阔而复杂的领域，但是你可以通过遵循来自 [SANS Institute](http://www.sans.org/) 的趋势和来自 [WordPress Codex](http://codex.WordPress.org/Hardening_WordPress) 的建议来保护你的网站免受本指南中提到的漏洞扫描器之类的工具的攻击。

## 分享这篇文章