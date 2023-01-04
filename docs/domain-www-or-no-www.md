# 上网还是不上网——这是个问题

> 原文：<https://www.sitepoint.com/domain-www-or-no-www/>

在网络出现的遥远的日子里，那些在离线媒体上发布网址的人会加上“www”前缀。它告诉每一个人你已经进入了 21 世纪，并且在万维网上拥有一处顶级房产。

快进到 2012 年。每个人都知道什么是 web 很少有组织发布带有 www 前缀的 URL。人们知道 Google.com、Facebook.com、Twitter.com 和 SitePoint.com 是网站。

在我们进一步讨论之前，你的网站**必须**在有或没有 www 的情况下都能工作。出于 SEO 和规范/重复内容问题的考虑，您应该选择一个域选项，并在使用另一个时进行重定向。如果你更喜欢裸域名，当访问者请求完整的 www 版本时，重定向到它。这并不难——一个三线阿帕奇。htaccess 文件就足够了:

```
 RewriteEngine on
RewriteCond %{HTTP_HOST} !^mydomain.com [NC]
RewriteRule ^/?(.*)$ http://mydomain.com/$1 [L,R=301] 
```

问题是:*你该选哪个？*

支持 www 阵营的人指出,‘www’并没有被弃用。这是明确的，技术上准确，并区分地址从 FTP，邮件或其他数据类型的类似网址。

反 www 阵营指出这完全没有必要。没人困惑。网址更短，更容易阅读，打字更快。

“啊”，支持 www 的游说团体说，“你只是在虚荣”。“嗯”，反团体回应道，“你太挑剔了，我的网站看起来比你的好多了”。“不”，专业人士喊道，“你的网站很臭。”
等等。

我的看法:*没关系*。挑一个坚持下去。有些单词组合有了 www 会更好看，有些没有会更好看。归根结底，这是你个人的品牌偏好，很少有人会注意到或在意。

请记住，你的网站应该加载，无论网址，并在必要时重定向。如果你违反了这条规则，那就去站在淘气的角落里，想想你的行为会带来什么后果。

对这篇文章的评论已经关闭。有关于域名的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?140-General-Web-Development-amp-Application-Design-Issues?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章