# 使用。防止网页抓取的 htaccess

> 原文：<https://www.sitepoint.com/using-htaccess-prevent-web-scraping/>

网络抓取，也称为内容抓取、数据抓取、网络采集或网络数据提取，是一种从网站提取数据的方式，最好是使用一个发送大量 HTTP 请求的程序，模拟人类行为，获得响应并从中提取所需的数据。像[和](http://www.kimonolabs.com/)这样现代的基于图形用户界面的网页抓取器可以让你在没有任何编程知识的情况下完成这项任务。

如果你面临其他人从你的网站抓取内容的问题，有许多方法可以检测网页抓取器——谷歌网站管理员工具和 T2 Feedburner 工具等等。

在这篇文章中，我们将讨论一些方法，使这些铲运机的生活困难，使用。Apache 中的 htaccess 文件。

安。htaccess(超文本访问)文件是 web 服务器的纯文本配置文件，它覆盖文件所在目录的全局服务器设置。他们可以创新地用来防止网页刮擦。

在讨论具体方法之前，我先明确一个小事实:如果某个东西是公开可用的，[它是可以被刮掉的](http://webscraping.com/blog/Can-you-extract-data-from-this-website/)。我们在这里讨论的步骤只会使事情变得更加困难，而不是不可能。然而，如果有人聪明到可以绕过你所有的过滤器，你会怎么做？我们也有解决方案。

## 开始使用。htaccess

自从使用以来。htaccess 文件涉及 Apache 检查和读取所有。htaccess 文件，默认情况下它通常是关闭的。在 [Ubuntu](https://help.ubuntu.com/community/EnablingUseOfApacheHtaccessFiles) 、 [OS X](http://www.garron.me/en/mac/how-to-enable-htaccess-apache-mac-os-x.html) 和 [Windows](http://www.lancelhoff.com/enabling-htaccess-in-apache-on-windows/) 中有不同的进程来启用它。你的。只有在启用 htaccess 文件后，Apache 才会解释它们，否则它们会被忽略。

接下来，在我们的大多数用例中，我们将使用 Apache 的 [`RewriteEngine`](http://httpd.apache.org/docs/current/mod/mod_rewrite.html#rewriteengine) ，它是`mod_rewrite`模块的一部分。如果有必要，你可以查看关于[如何为 Apache 设置 mod_rewrite 的详细指南](https://www.digitalocean.com/community/tutorials/how-to-set-up-mod_rewrite)或[上的一般指南。htaccess](http://code.tutsplus.com/tutorials/the-ultimate-guide-to-htaccess-files--net-4757) 。

一旦您完成了这些，您就可以继续使用这里讨论的解决方案来处理内容抓取器了。如果您没有成功完成这两个步骤，Apache 将忽略您的。或者在更改后重新启动时引发错误。

## 防止热链接

如果有人抓取你的内容，你所有的内嵌 HTML 保持不变。这意味着作为你内容一部分的图片链接(很可能在你的域名上)保持不变。如果抓取者希望将内容放在不同的网站上，图像仍然会链接回原始来源。这叫做热链接。热链接会消耗你的带宽，因为每次有人打开 scraper 的网站，你的图片就会被下载。

您可以**通过在您的。htaccess 文件。**

```
RewriteEngine on
RewriteCond %{HTTP_REFERER} !^$

# domains that can link to your content (images here)
RewriteCond %{HTTP_REFERER} !^http(s)?://(www\.)?mysite.com [NC]

# show no image when hotlinked
RewriteRule \.(jpg|png|gif)$ – [NC,F,L]

# Or show an alternate image
# RewriteRule \.(jpg|png|gif)$ http://mysite.com/forbidden_image.jpg [NC,R,L]
```

关于代码的一些注释:

*   打开`RewriteEngine`让我们能够重定向用户的请求。
*   `RewriteCond`指定应该重定向哪些请求。`%{HTTP_REFERER}`是包含发出请求的域的变量。
*   然后我们把它和自己的域`mysite.com`匹配起来。我们添加了`(www\.)`来确保来自`mysite.com`和`www.mysite.com`的请求都被允许。同样，我们的代码涵盖了`http`和`https`。
*   接下来，我们检查是否请求了一个`jpg`、`png`或`gif`文件，并显示一个错误或将请求重定向到另一个映像。
*   `NC`忽略案例，`F`显示一个`403 Forbidden`错误，`R`重定向请求，`L`停止重写。
*   请注意，您应该只应用上述规则中的一个(要么是`403`错误，要么是备用图像)。这是因为一旦遇到`L`，Apache 就不会应用任何其他规则。在上面的代码示例中，替换图像方法被注释掉了。

### 网页抓取器如何绕过这一点？

web scraper 绕过这一障碍的一个方法是在 HTML 代码中遇到图像时下载它们。在这种情况下，可以应用正则表达式检查，下载图像，并且在将数据存储在系统中时改变图像的链接。

## 允许或阻止来自特定 IP 地址的请求

如果您碰巧确定了 web scraper 请求的来源(通常，来自同一个 IP 地址的请求数量非常多)，您可以阻止来自该 IP 地址的请求。

```
Order Deny
Deny from xxx.xxx.xxx.xxx
```

在上面的代码中(以及本文的其他例子中)，您可以用想要阻止的 IP 地址替换`xxx.xxx.xxx.xxx`。如果您真的担心安全性，您可以拒绝来自所有 IP 地址的请求，并有选择地允许来自 IP 地址白名单的请求:

```
order deny,allow
Deny from all
# IP Address whitelist 
allow from xx.xxx.xx.xx
allow from xx.xxx.xx.xx
```

这项技术的一个用例(与网络抓取无关)是阻止访问 WordPress 的`wp-admin`目录。在这种情况下，你应该只允许来自你的 IP 地址的请求，这样就排除了有人通过 wp-admin 入侵你的网站的可能性。

### 网页抓取器如何绕过这一点？

如果 web scraper 可以访问代理，它可以将其请求分布在 IP 地址列表上，以避免来自一个 IP 地址的异常活动。

解释一下:假设有人从 1.1.1.1 的 IP 地址抓取你的网站。所以您使用. htaccess 阻止了 1.1.1.1。现在，如果 scraper 可以访问代理服务器 2.2.2.2，它会通过 2.2.2.2 路由其请求，因此在您的服务器看来，请求来自 2.2.2.2。因此，尽管阻塞了 1.1.1.1，scraper 仍然能够访问资源。

因此，如果 scraper 可以访问数以千计的代理，如果它从每个代理发送的请求数量很少，它就会变得不可检测。

## 从 IP 地址重定向请求

您不仅可以阻止任何 IP 地址，还可以将它们重定向到不同的页面:

```
RewriteCond %{REMOTE_ADDR} xxx\.xxx\.xxx\.
RewriteRule .* http://mysite.com [R,L]
```

如果你把他们重定向到一个静态网站，很有可能抓取工具会发现这一点。然而，你可以更进一步，做一些更有创意的事情。为此，你需要了解你的内容是如何抓取的。

网络抓取是一个系统化的过程。它包括研究 URL 模式和向网站上所有可能的页面发送请求。例如，如果你是一个 WordPress 用户，URL 模式是`http://mysite.com/?p=[page_no]`，其中你将`page_no`从 1 增加到一个大的数字。

您可以创建一个专门用于重定向的页面，将请求重定向到多个预定义页面中的一个:

```
RewriteCond %{REMOTE_ADDR} xxx\.xxx\.xxx\.
RewriteRule .* http://mysite.com/redirection_page [R,L]
```

在上面的代码中，“redirection_page”是用于执行后续预定义重定向之一的页面。因此，当一个网页抓取程序运行时，它将被重定向到许多页面，这将很难检测到你已经确定了刮刀。

或者，“重定向 _ 页面”可以重定向到第三个页面“重定向 _ 页面 _1”，该页面然后将重定向回“重定向 _ 页面”。这将导致重定向循环，请求将在两个页面之间被无限期地退回。

### 网页抓取器如何绕过这一点？

web scraper 可以检查请求的重定向。如果有重定向，它将获得一个`301`或`302` HTTP 状态代码。如果没有重定向，它将获得正常的`200`状态代码。

## 马特·卡茨前来救援

马特·卡茨是谷歌网络垃圾邮件团队的负责人。他的部分工作是不断寻找垃圾网站。如果他不喜欢你的网站，他可以让它从谷歌的搜索结果中消失。最近熊猫和企鹅对谷歌搜索算法的更新影响了大量网站，包括一些 scraper 网站。

网站管理员可以使用[这个表格](https://docs.google.com/forms/d/1Pw1KVOVRyr4a7ezj_6SHghnX1Y6bp1SOVmy60QjkF0Y/viewform)向 Google 报告 scraper 站点，提供内容来源。如果你制作原创内容，你肯定会成为网络搜索引擎的目标。然而，如果他们重新发布你的内容，谷歌将确保他们从搜索结果中被删除。

## 分享这篇文章