# Linkalicious Friday:会话劫持，AWS 免费，以及丢失 24 秒的情况

> 原文：<https://www.sitepoint.com/linkalicious-friday-session-jacking-aws-for-free-and-the-case-of-the-missing-24-seconds/>

又到了周五，是时候从网络上获取更多美味的链接了:

*   W3C 的 HTML5 草案规范的最新修订版已经发布。人们欢欣鼓舞。*   如果你还没有听说本周的 Firesheep，那你就没有在 Twitter 上关注正确的人。这是一个非常容易使用的 Firefox 扩展，可以通过开放的 Wi-Fi 网络[劫持](http://en.wikipedia.org/wiki/Sidejack)基于 cookie 的会话。Firesheep 引起了人们对许多认证系统(包括像脸书和 Twitter 这样的大公司)中这一重大缺陷的强烈关注。如果你想保护你的应用免受这种会话劫持，看看 GitHub 博客上的[这篇关于他们如何在 Firesheep 发布后保护登录的文章。](http://github.com/blog/737-sidejack-prevention)*   亚马逊网络服务宣布了一项免费服务。从 11 月 1 日起，新的 AWS 用户将可以使用免费层一年。它包括 EC2 微实例、5GB 的 S3 存储和 30GB 的互联网数据传输。如果你一直在阅读 [*在云中托管你的网站*](https://www.sitepoint.com/books/cloud1) ，但对注册 AWS 有点害羞，这是一个不花任何钱玩一玩的完美方式。*   24 秒内消失:[PHP 和 MySQL 处理时间戳的一个奇怪的怪癖](http://pumka.net/2010/10/24/why-mysql-timestamp-is-24-seconds-different-from-php/)。原来 MySQL 的`UNIX_TIMESTAMP`和`FROM_UNIXTIME`方法占了[闰秒](http://en.wikipedia.org/wiki/Leap_second)，而 PHP 的日期和时间函数却没有。因此，在 MySQL 中为相同的日期/时间字符串生成的时间戳将与 PHP 中生成的时间戳相差 24 秒。至少值得注意，因为如果你不知道是什么引起的，这种错误似乎会导致几个小时的沮丧！(经由[phpdeveloper.org](http://phpdeveloper.org))

这星期就这些了。如果你在这一周里看到任何你认为会在我的 Linkalicious 周五帖子中出现的有趣的故事，请发送给我:louis@sitepoint.com。

周末愉快！
 *特写影像由[特雷西·亨特](http://www.flickr.com/photos/tracyhunter/)拍摄。*

## 分享这篇文章