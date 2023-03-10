# 为 WordPress 登录页面设置 IP 限制

> 原文：<https://www.sitepoint.com/setting-ip-restrictions-to-the-wordpress-login-page/>

根据 2013 年进行的一项研究，每天大约有 [3 万个网站遭到黑客攻击](http://www.forbes.com/sites/jameslyne/2013/09/06/30000-web-sites-hacked-a-day-how-do-you-host-yours/)。不言而喻，你需要采取措施来保护你的网站免受黑客攻击。

保护你自己的数据很重要，但是让你的访问者知道他们的数据是安全的就更重要了。受保护的站点是可信的站点。

有几种方法可以降低你的 WordPress 网站被黑客攻击的几率。将访问权限限制在预先定义的一组用户就是其中之一。在这篇文章中，我们将带你一步步地为 WordPress 登录页面设置 IP 限制。

![WordPress Login Page](img/5393694b6d7489bbf8e94a66591d88c3.png)

在我们进入教程之前，让我们快速回顾一下你的网站面临的一些安全威胁。

## WordPress 安全威胁

**暴力登录尝试**–当黑客试图使用一系列用户名和密码组合登录您的网站时。

**登录信息确认**–WordPress 通知用户他们输入了错误的登录凭证的哪一部分。例如，如果你的用户名是正确的，但是密码不匹配，WordPress 会通知用户。这使得暴力攻击变得非常非常容易。

如果黑客发现你正在使用的 WordPress 版本，他们可以利用特定版本的漏洞进入你的网站。

默认情况下，WordPress 不允许世界各地的用户注册你的网站。作为预防措施，此选项应保持禁用状态。

**访问主题和插件**–WordPress 网站所有者可以访问文件编辑功能，如果你的网站被黑客攻击，这可能是一个安全问题，所以一般不建议这样做。

让我们看看在修改站点文件之前需要采取的一些预备步骤。

## 一些安全措施

在本教程中，我们将把 PHP 代码添加到站点的`.htaccess`配置文件中。作为预防措施，第一步是备份配置文件。

有些人可能还想在开始之前备份整个网站。定期备份你的网站是一个好习惯，在对它进行任何大规模的改动之前一定要做。VaultPress 是一个很棒的插件，可以完成这项工作。

## 静态 IP 与动态 IP

在本教程中，我们将向你展示如何在静态 IP 地址和动态 IP 地址上限制对你的 WordPress 站点仪表板的访问。

如果你从你的桌面或者其他地方编辑你的站点，应该遵循静态 IP 地址教程。在这种情况下，您的 IP 地址不会改变，即它保持静态。

如果你的站点是从几个地方编辑的，那么应该遵循**动态 IP 地址**教程。如果出现以下情况，您的 IP 地址会频繁更改:

*   其他团队成员从不同的位置访问网站进行编辑
*   你使用移动设备来编辑你的网站
*   您经常出差，需要从不同的位置访问您的站点

既然基础知识已经讲清楚了，让我们开始吧。

## 入门指南

为你的 WordPress 登录页面设置 IP 限制的第一步是知道你的 IP 地址。如果你还不知道，你可以通过像[whatismyipaddress.com](http://whatismyipaddress.com)这样的网站查看。

如上所述，我们将对`.htaccess`配置文件进行修改。第二步是定位您站点的`.htaccess`配置文件。`.htaccess`配置文件位于你站点的根目录下。然而，如果由于某种原因没有`.htaccess`配置文件，您可以自己创建一个。使用 cPanel 或 FTP 客户端登录并找到您的文件。

找到它之后，第三步是找到一个合适的文本编辑器，这样就可以向配置文件添加代码。我们建议您使用 cPanel 的内置文本编辑器或桌面特定的文本编辑器(如记事本)来编辑`.htaccess`配置文件。

**注意:所有代码都将被添加到`.htaccess`配置文件的最顶端，以防止弄乱现有的站点设置。**

## 使用静态 IP 地址设置 IP 限制

如果您的 IP 地址不经常改变，或者如果您从几个已知的 IP 地址访问您的站点，那么您可以使用静态 IP 地址方法设置 IP 限制。在本教程中，你将能够为那些访问你的 WordPress 站点登录页面的用户(IP 地址)创建一个安全的 IP 列表。

### 如何使用静态 IP 地址设置 IP 限制

*   在 cPanel(或任何文本编辑器)中打开站点的`.htaccess`配置文件。
*   将以下代码添加到`.htaccess`配置文件的顶部( [Gist 也可以从](https://gist.github.com/rafaysansari/d50ed4d18bd40f960f8e)获得)。

    ```
    RewriteEngine on

        RewriteCond %{REQUEST_URI} ^(.*)?wp-admin$

        RewriteCond %{REMOTE_ADDR} !^12\.345\.678\.90

        RewriteCond %{REMOTE_ADDR} !^IP Address InsertTwo$

        RewriteCond %{REMOTE_ADDR} !^IP Address InsertThree$

    RewriteRule ^(.*)$ - [R=403,L] 
    ```

*   保存`.htaccess`配置文件。

### 编辑代码

你现在所要做的就是编辑第 4 行和第 5 行(T2 要点中的第 9 行和第 10 行)并添加允许访问 WordPress 登录页面的 IP 地址。为此，用您想要授予登录地址的 IP 地址替换`IP Address InsertTwo$`和`IP Address InsertThree$`。你给的 IP 地址应该是第 3 行指定的格式(T4 要点的第 8 行)。

### 添加或删除授权用户

如果您想授予更多 IP 地址的登录权限，您可以简单地复制粘贴`RewriteCond %{REMOTE_ADDR} !^IP Address Insert$`并插入 IP 地址来代替`IP Address Insert$`。类似地，如果您想只授予一个或两个访问权限，那么从代码中删除多余的`RewriteCond %{REMOTE_ADDR}`行。

### 当未经授权的用户访问页面时会发生什么？

现在你已经设置了 IP 限制，当一个未经授权的用户访问你网站的登录页面或`wp-admin`页面时，他们会看到你当前主题的`404 Error`页面。

如果你遵循[要点](https://gist.github.com/rafaysansari/d50ed4d18bd40f960f8e)，你会注意到代码在前两行迎合了一个重定向循环。您必须将第 1 行和第 2 行中的`your-site's-path`改为您站点的正确路径。

## 使用动态 IP 地址设置 IP 限制

你们中的一些人可能必须授予几个用户登录权限，无论是因为你的站点有许多贡献者，还是因为你正在运行一个多站点网络。基本事实是，有几个动态变化的 IP 地址需要登录到您站点的仪表板。

### 如何使用动态 IP 地址设置 IP 限制

*   在 cPanel(或任何文本编辑器)中打开站点的`.htaccess`配置文件。
*   将以下代码添加到`.htaccess`配置文件的顶部( [Gist 也可以从](https://gist.github.com/rafaysansari/e0d567355dd79d7d27dd)获得)。

    ```
     RewriteEngine on

        RewriteCond %{REQUEST_METHOD} POST

        RewriteCond %{HTTP_REFERER} !^http://(.*)?your-site's-name.com [NC]

        RewriteCond %{REQUEST_URI} ^(.*)?wp-login\.php(.*)$ [OR]

        RewriteCond %{REQUEST_URI} ^(.*)?wp-admin$

    RewriteRule ^(.*)$ - [F] 
    ```

*   保存`.htaccess`配置文件。

### 编辑代码

为了使代码适用于你的站点，用你的 WordPress 站点的 URL 替换第三行(第七行)中的 your-site's-name.com。

这段代码的要点版本在前两行中也考虑了重定向循环。您必须将第 1 行和第 2 行中的`your-site's-path`改为您站点的正确路径。这样做的话，如果你的网站陷入重定向循环，就会显示一个`404 Error`页面。

### 代码的功能

这个代码限制黑客通过机器人使用暴力攻击来访问你的 WordPress 站点。黑客试图从外部访问您的网站。将此代码添加到`.htaccess`配置文件意味着只有通过您的实际站点(即内部站点)导航到该页面的访问者才能访问该站点的登录页面或`wp-admin`页面。

## 包装它

没有一个解决方案可以保证您的站点免受任何可能的威胁。为 WordPress 登录页面设置 IP 限制将会对保护你的站点免受暴力攻击大有帮助。

如果你想了解更多关于 WordPress 安全的信息，你可以阅读 Narayan Prusty 的[防止对 WordPress 网站的暴力攻击](https://www.sitepoint.com/preventing-brute-force-attacks-against-wordpress-websites/)和 Tim Carr 的 [10 个确保 WordPress 安全的技巧](https://www.sitepoint.com/tips-to-secure-wordpress/)。

你的 WordPress 站点曾经面临过安全威胁吗？你采取什么措施来保护你的站点免受安全威胁？请在下面的评论区告诉我们。

## 分享这篇文章