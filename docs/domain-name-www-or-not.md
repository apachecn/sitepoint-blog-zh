# 你的域名:你是不是 www？

> 原文：<https://www.sitepoint.com/domain-name-www-or-not/>

*本文是与 [SiteGround](https://www.siteground.com/go/article-sp) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

在网络的早期，发布带有“www”前缀的网址是非常必要的。这三个字符防止混淆；很明显你指的是网络域名。

但是在 2017 年,‘www’有必要吗？

web 是多产的，很少有组织选择以“www”开头来发布他们的 URL。人们知道 Google.com、Facebook.com、Twitter.com 和 SitePoint.com 是网站。点号的顶级域名(TLD)如。“com”让这一点变得更加明显，但即使没有它，网络通常也是任何人寻找公司或服务的第一站。

## “www”的案例

“www”前缀未被弃用。这是明确的，技术上准确的，并区分地址从类似的协议，如邮件或 FTP 的网址。

虽然网址可以从它的。在过去的几年里，数百个顶级域名选项被引入。如果你使用的是不太容易识别或不明确的 TLD，比如 mysite.ninja，加一个“www”将有助于避免任何疑问。

如果没有“www”，您必须将根(非 www)域 DNS A 记录设置为指向您的 web 服务器的 IP 地址。如果您遇到可用性或性能问题，这可能太死板了；A 记录是固定的，可能需要一两天的时间来传播更改。“www”子域可以使用 DNS CNAME 记录进行配置，该记录可以即时更新和更改。

您还需要小心 cookie 和客户端存储。为非 www 域设置的 cookie、sessionStorage 或 localStorage 值在所有子域中共享。如果您的主要网站—mysite.com—设置了 10Kb 的 cookie 数据，那么无论该应用程序是否使用这些数据，它都会随每个针对 app.mysite.com 的请求和响应一起传输。

最后,“www”前缀在电子邮件客户端和文字处理器等将文本转换为链接的应用程序中是必不可少的。

## 没有“www”的理由

尽管有上面提到的技术问题，使用非 www 域很少成为问题，除非您有复杂的主机或应用程序需求。根地址更容易阅读，打字更快，更适合更小的(移动)浏览器界面。这个地址更有可能在你下一条推文中使用，而不需要使用网址缩写。

很少有人会感到困惑——尤其是当很多人使用谷歌搜索框而不是浏览器地址栏的时候。

## 这主要是虚荣心作祟

有些域名看起来更好，与“www”更平衡，例如

*   www.google.com
*   www.facebook.com
*   www.bbc.co.uk

有些不戴更好看:

*   twitter.com
*   yahoo.com
*   wikipedia.org

有些似乎在任一方向上都能很好地工作:

*   sitepoint.com/www.sitepoint.com
*   Amazon.com/www.amazon.com
*   dropbox.com/www.dropbox.com

选择任何适合你的领域，但…

## 明智地配置它

用户可以添加也可以不添加“www ”,以确保当浏览器发出请求时，www 和非 www 地址都有效。这可以通过多种方式实现，但最简单的方法是 web 服务器配置设置。

中的以下代码将 Apache 配置为将所有非 www 请求重定向到 URL 的 www 版本。将其添加到 web 根目录下的一个`.htaccess`文件中:

```
RewriteEngine On
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteRule ^(.*)$ http://www.%{HTTP_HOST}/$1 [R=301,L]
```

或者，这个`.htaccess`将所有 www 请求重定向到非 www URL:

```
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
RewriteRule ^(.*)$ http://%1/$1 [R=301,L]
```

这允许你在文档或电子邮件中公开‘www’域名，即使这不是必需的。

如果你的网站通过安全的 HTTPS 提供服务，你也应该小心。SSL 证书通常对非 www 和 www 域都有效，但不能保证。请咨询您的证书颁发机构，如果您的证书不正确，可能需要购买额外的或通配符证书。一些主机服务提供商，比如我们的合作伙伴 [SiteGround](https://www.siteground.com/go/article-sp) ，允许你订购一个 SSL 证书，无论你的域名有没有 www 前缀都可以使用。

最后，做一个决定，坚持下去。千万不要心血来潮在非 www 和 www 网址之间切换！上面的`.htaccess`代码发出一个“301 永久移动”的 HTTP 重定向代码，这样浏览器和搜索引擎就会相应地更新它们的索引。传播变化可能需要时间；在这种情况下，用户可能会遇到访问问题。

你的公司是选择使用还是放弃“www ”?成功了吗？有人注意到了吗？！

## 分享这篇文章