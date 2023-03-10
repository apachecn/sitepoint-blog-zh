# 用 greasemonkey 定制 gmail

> 原文：<https://www.sitepoint.com/customising-gmail-with-greasemonkey/>

GreaseMonkey 是 Firefox 的一个扩展，允许你添加“用户脚本”:附加到一个特定 URL 或一组 URL 上的 JavaScript 代码，当该 URL 被访问时运行。本质上，这就像有一个 bookmarklet 对一个页面做一些有用的事情，并让这个 bookmarklet 在某些页面被访问时自动运行。借助来自 DOM 的内省能力，JavaScript 可以对页面进行任何修改，这就是 GreaseMonkey 派上用场的地方。已经有[一个有用的用户脚本库](http://69.90.152.144/collab/GreaseMonkeyUserScripts)来实现对某些网站的定制，从在英国 EBay 上显示含税价格到在 MSDN 文档中修改 IE 特有的位。

不过，其中最强大的是 Mihai Parparita 的“在 GMail 中添加持久搜索功能”。该脚本为 GMail 提供了来自 Thunderbird、Evolution 或 Mail.app 等桌面邮件客户端的“保存的搜索”或“虚拟文件夹”功能。它的工作方式是向包含“保存的搜索”的页面添加一个新的 HTML 块:这些搜索本身保存在一个 cookie 中，cookie 由脚本加载。该脚本使用 [Ajax 技术](https://www.sitepoint.com/blog/)来获取每个保存的搜索中的消息数量，以便它们可以在 HTML 块中显示，并且每两分钟更新这些数字。我可以想象这将在未来的某个时候被引入 GMail，但在那之前，GreaseMonkey 提供了一种理想的方式来原型化这种功能(如“编辑样式” [bookmarklet](http://www.squarefree.com/bookmarklets/webdevel.html) 或 [web developer toolbar](http://www.chrispederick.com/work/firefox/webdeveloper/) 的编辑 CSS 部分【针对 CSS】)并将其分发给早期采用者。

## 分享这篇文章