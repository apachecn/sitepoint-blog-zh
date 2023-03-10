# 关于丰富的 web 用户界面的更多信息

> 原文：<https://www.sitepoint.com/more-on-rich-web-uis/>

一些链接和对向后兼容的 XmlHttpRequest 的搜索…

在[一个更好的网络应用界面](http://thatwebthing.com/articles/2004/07/19/1)和[一个更好的网络应用界面](http://thatwebthing.com/articles/2004/09/14/3)中，John Wilger 有一些有趣的想法。

Kevin 从细节上后退一步，总结了 PHP / Javascript 在探索客户端界面选项和跨越巨大鸿沟方面的进展。

Stephan 谈论[一个他为了好玩而开发的网络应用](http://blog.php-tools.de/archives/69_Hunting_for_Galactic_Civilizations_perfect_starting_location.html)，它使用了 DOM 操作。你可以在这里看到结果。

亚马逊的搜索引擎 [A9](http://www.unto.net/unto/work/a9_launch.html) 是开放的——更多客户端技巧。

开始为 XmlHttpRequest [开发一个适配器，这个适配器使用文档操作技巧，任务是为没有 XmlHttpRequest 实现的浏览器至少提供*一些东西*。基本思想是有两个隐藏的 iframes。第一个获取插入其中的生成表单(表示请求),提交目标是第二个 iframe，从中收集结果。通过“轮询”第二帧字符串长度的变化来收集服务器响应(避免让远程服务器生成 Javascript 来触发客户端回调函数)。不幸的是，现在这个问题已经很严重了，我不是 Javascript 兼容性技巧的大师，而且时间太短了。可能是因为不同的浏览器需要不同的版本——如果有足够疯狂的人去尝试的话…](http://cvs.sourceforge.net/viewcvs.py/xmlrpccom/scriptserver/ScriptServer/js/pseudoxmlhttp.js?view=auto)

## 分享这篇文章