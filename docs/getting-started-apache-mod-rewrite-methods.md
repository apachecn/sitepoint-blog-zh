# 入门:Apache mod_rewrite 方法

> 原文：<https://www.sitepoint.com/getting-started-apache-mod-rewrite-methods/>

Apache 的 mod_rewrite 模块似乎是 Apache 管理中产生最多问题的一个领域。说到 url 重写，我似乎是一个永远的初学者，并且我继续定期梦想不可能的愿望。

我也烦我自己的同行，让他们提供最好的重写方法来建立我的军火库。然而，在这个强大的模块上看到了大量的查询，我认为分享一些我自己最喜欢的(和基本的)重写用法将有助于那些寻求答案的人。

此外，我确信我们在 SitePoint 社区周围有一些隐藏的重写忍者，他们会添加一些我们都可以利用的缺失的魔法。

最后，我的重写知识几乎完全来自阅读和重读(和重读！)Apache HTTP docs 以及这些年来关于这个主题的一些严肃的谷歌搜索。正则表达式可以是你的朋友，也可以是你的敌人！；>

这些通常被添加到 httpd.conf 文件或。htaccess 文件。如果你正在使用 htaccess 的话，可以参考一些很好的建议。

将子域重定向到域:

 `RewriteEngine on
RewriteCond %{HTTP_HOST} ^(.*).mydomain.com$
RewriteCond %{HTTP_HOST} !^www.mydomain.com$
RewriteRule (.*) http://mydomain.com/%1$1 [P]`

或者，如果你想强制 www.mydomain.com——即使访问者使用 mydomain.com 访问你的网站:

 `RewriteEngine On
RewriteCond %{HTTP_HOST} !^www.mydomain.com$ [NC]
RewriteRule ^(.*)$ http://www.mydomain.com/$1 [R,L]`

也许您需要在包含您的入口页面的 DocumentRoot 中为访问者指出一个目录(就像一个 beta 测试站点):

 `RewriteEngine On
RewriteCond %{HTTP_HOST} ^www.betadomain.com$
RewriteCond %{REQUEST_URI} !^/beta/
RewriteRule ^(.*)$ /beta/$1`

在迁移期间，将用户(和搜索引擎)从旧域永久重定向到新域(并让他们访问他们想要的页面，而不仅仅是新站点的根索引页面)。

 `RewriteEngine on
RewriteCond %{HTTP_HOST} ^olddomain.com$
RewriteRule ^(.*)$ http://newdomain.com$1 [R=301,L]
RewriteCond %{HTTP_HOST} ^www.olddomain.com$
RewriteRule ^(.*)$ http://newdomain.com$1 [R=301,L]`

最后，我看到很多关于搜索引擎友好的 url 和动态页面的问题。我使用了我从一个朋友那里学来的简单的改写——然而——当我在论坛上一遍又一遍地看到这些问题时，这是一些贡献会很大的地方。想尽一切办法添加您处理动态页面的最佳重写方法。我不经常使用这最后一个策略，它相当初级。

允许`www.domain.com/news5.php`重写为`www.domain.com/news.php?id=5`

 `RewriteEngine on
RewriteBase /articles
RewriteRule ^news([^\.]+)\.php$ news.php?id=$1` 

## 分享这篇文章