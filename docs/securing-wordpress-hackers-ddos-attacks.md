# 保护 WordPress 免受黑客和 DDoS 攻击

> 原文：<https://www.sitepoint.com/securing-wordpress-hackers-ddos-attacks/>

WordPress 的受欢迎程度毋庸置疑，它为全球超过 7460 万个网站提供支持，Technorati 排名前 100 的博客中有 48%由该平台管理。然而在网络世界，任何流行的东西都更容易受到攻击，WordPress 也不例外。然而，倾向于攻击 WordPress 网站的攻击类型——除非你是一个大品牌——通常是由没有大量技术知识的人进行的。这些人通常被称为“脚本小子”，因为他们使用通用代码、技术和工具包来入侵目标网站。

![XML-RPC for WordPress](img/7c81df121ce03113683306b6ad807c97.png)

关于这一点的好消息是，这意味着通常攻击可以被快速而容易地处理。虽然没有必要达到攻击造成损害的阶段，因为大多数攻击可以在一开始就被阻止。所以今天，我们将看看如何保护您的安装并避免常见的黑客攻击。

## 从服务器开始

在你考虑保护你的网站之前，你应该从头开始，这意味着首先要确保你的主机服务器是安全的。从基础开始，你应该根据安全和信誉而不是价格来选择主机。虽然我肯定有一些像样的廉价主机，但在很大程度上，每月花费 2 美元的主机是不够的。

大多数托管的 WordPress 托管服务都有安全托管的名声。但是它们并不都允许一些与性能相关的插件，所以你应该先检查一下你到底有什么访问权限和控制级别。

其中大多数都提供:

*   托管 WordPress 主机
*   自动安全更新
*   每日备份
*   一键恢复点
*   自动缓存
*   顶级安全性

无论你决定和谁一起去，你都应该检查他们是否提供了以下服务:

*   运行稳定版本的服务器软件，并根据需要进行修补
*   启用服务器级防火墙
*   允许您经常轻松地备份和恢复(站点和数据库)
*   入侵检测

托管主机(比如 WPEngine)使用通过 CDN 传递的缓存，所以如果你真的不想使用托管的 WordPress 主机，那么考虑实现一个 CDN 和一个缓存插件，比如 W3 Total Cache。这是一种设置网站的简单方法，这样所有通过 CDN 缓存的流量也可以通过安全套接字层(SSL/TLS)传输。如果您需要帮助来了解这些技术，我推荐以下由 MaxCDN 提供的可视化指南。为了全面披露，我为 MaxCDN 工作，但我相信您会发现它们是有用的资源:

*   [什么是 CDN？](https://www.maxcdn.com/one/visual-glossary/cdn/)
*   [SSL 如何工作](https://www.maxcdn.com/one/visual-glossary/ssl/)
*   [用带有 CDN 的 W3 总缓存设置 WordPress】](https://www.maxcdn.com/one/tutorial/setup-wordpress-with-w3-total-cache/)

不幸的是，WordPress 安装在共享服务器上，而不是 VPS 或专用服务器上，通常安装和配置的方式对主机来说是最容易的，但不一定是最安全的。

*请注意，以下配置适用于熟悉编码或基本系统管理任务的高级用户。如果你不是，那么请你的网络开发者为你设置。*

## 登录、密码和插件

考虑到超过 70%的 WordPress 安装容易受到攻击，在这个问题上简单说一下，值得重复一下。当你安装了 WordPress 之后，一定要确保一旦有了最新的版本，就马上更新。这同样适用于你的主题和你使用的所有插件。这同样适用于您的服务器软件。对于你们中的许多人来说，这听起来可能是显而易见的，但统计数据本身就说明了问题，有许多许多旧版本的平台被安装。

说到密码，我每天都会遇到一些人仍然使用“companyname123”这样的密码，这些人都是科技行业的人，应该知道得更多。因此，对你自己和其他用户来说，生成复杂的密码并存储在密码管理器中，比如 LastPass，这样更安全。

## 应用自动更新

为了确保次要和主要更新在 WordPress 中自动发生，你可以对应用它们的代码做一个小的改变。这消除了你手动做备份的需要(只有小的更新会自动应用到 WordPress v.3.7 和更高版本)，但是你应该确保你启用了自动的、频繁的备份，以防出现问题并使你的网站瘫痪。

要启用更新，请将以下代码应用到您的`wp-config.php`文件:

```
#Enable all core updates, including minor and major:
define ( 'WP_AUTO_UPDATE_CORE', true );
```

更常见的情况是，如果你使用的插件没有合理地频繁更新，你会遇到自动更新的问题，所以一定要确保你安装的插件得到维护，并在可能的情况下提供支持。

## 禁用 PHP 错误报告

如果你正在使用的插件或主题出现了错误，那么很可能产生的错误信息会显示你的服务器路径，这反过来会被黑客拦截。考虑到这一点，您应该通过将以下代码添加到您的`wp-config.php`文件中来禁用错误报告:

```
error_reporting (0);
@ini_set ('display_errors', 0);
```

或者，如果你对编辑你的配置文件没有信心，那么你可以让你的网络主机为你禁用它。

## 停止暴力攻击

如果你要监控你的 WordPress 网站每天有多少次登录尝试，你可能会感到震惊。这些都是常见的攻击，通过使用复杂的密码在一定程度上是可以避免的。暴力攻击通常来自试图猜测您的管理员密码的僵尸网络。您可以通过使用 HTTP AUTH 在登录屏幕级别添加额外的保护层来降低风险并阻止大多数暴力攻击。

为此，你首先需要通过设置`.htaccess`密码保护来保护你的目录。一旦你完成了这些，你需要添加下面的代码到你的`.htaccess`文件中:

```
#Protect wp-login
<files wp-login.php="">
AuthUserFile ~/.htpasswd
AuthName "Private access"
AuthType Basic
require user mysecretuser
</files>
```

这将弹出一个认证框，提示你输入用户名和密码，然后你需要登录到正常的 WordPress 登录界面——当然你应该为两者使用不同的密码。

您还可以通过监控试图登录的 IP 地址并将其锁定来防止暴力攻击。或者，您可以简单地将管理员用户名从“admin”更改为您自己的名称或其他名称，然后删除默认的管理员用户配置文件。你和你的网站管理员/开发者真的应该是这个网站上唯一拥有管理权限的人。

## 基于 URL 的攻击

对于试图通过发出 URL 请求来寻找网站弱点的黑客来说，这实在是暗箭伤人，这些请求应该会返回一个错误，但有时会被完成。

网址可能看起来像这样:http://yourwebsite.com/your/files/%3G/config

通常，黑客会在 URL 中使用一个左括号，所以首先，为了克服这一点，有必要生成一个 403 禁止页面来阻止任何包含括号的请求。为此，只需将下面一行粘贴到您的。htaccess 文件:

```
RedirectMatch 403 [
```

要创建更复杂的规则集，您不必自己编写所有代码。如果你熟悉使用`.htaccess`并且你的站点在 Apache 服务器上，那么你可以使用 5G 防火墙，这是一个常见攻击的黑名单。您也不必使用所有的行，因为它是模块化的，如果它确实产生了错误，您可以逐行删除，直到您发现问题。

您可以通过在文件中添加以下行来保护`.htaccess`文件本身:

```
<files .htaccess>
order allow,deny
deny from all
</files>
```

## WordPress 安全插件

你当然也可以使用 WordPress 的安全插件。在安装之前，你应该检查你使用的任何插件是否被支持并经常更新。如果是这样的话，那么你也应该检查评级和评论，以确定哪个被 WordPress 社区认为是最好的。

也要记住，如果你的系统上有很多插件，要定期移除你不用的东西。问问你自己，任何给定插件允许你使用的功能是否真的有必要，去掉那些你不需要的。对于那些你已经停用的插件，你也应该删除它们，因为它们为黑客提供了潜在的途径。如果插件不再被支持，那么你应该寻找一个替代品，因为它注定会在某个时候产生漏洞，如果它还没有的话。

在很大程度上，WordPress 安全是关于使用常识和理解很多时候，黑客和恶意软件可以归结为终端用户的错误。在很大程度上，黑客是通过利用软件入侵的，所以如果你确保你总是拥有最新的版本，你就能很好地保护自己。黑客寻找最容易的路线，除非他们专门针对你，所以收紧你的网站，不要让他们容易。

## 进一步阅读

如果你有兴趣阅读更多内容，这里有一些以前在 SitePoint 上发表的与 WordPress 安全相关的文章，值得一看:

*   [关于 WordPress 安全插件你可能不知道的事情](https://www.sitepoint.com/wordpress-security-plugins/)
*   [如何保护自己免受流氓 WordPress 插件的攻击](https://www.sitepoint.com/protect-yourself-from-rogue-wordpress-plugins/)
*   WordPress 维护权威指南
*   [托管 WordPress 主机:利弊](https://www.sitepoint.com/managed-wordpress-hosting-pros-and-cons/)
*   [使用谷歌认证器对 WordPress 进行两步验证](https://www.sitepoint.com/2-step-verification-wordpress-using-google-authenticator/)
*   [轻松发现 WordPress 漏洞](https://www.sitepoint.com/uncovering-wordpress-vulnerabilities-ease/)
*   更新 WordPress、插件和主题的指南
*   [防止针对 WordPress 网站的暴力攻击](https://www.sitepoint.com/preventing-brute-force-attacks-against-wordpress-websites/)

## 分享这篇文章