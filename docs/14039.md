# Greasemonkey 中的“严重安全漏洞”

> 原文：<https://www.sitepoint.com/serious-security-vulnerability-in-greasemonkey/>

火狐浏览器高级用户的流行扩展软件 grease monkey T1 的制造商[发布了一个警告，称当前版本存在严重的安全漏洞。此漏洞可能会允许访问运行 Firefox 中 Greasemonkey 扩展的系统上存储的任何和所有文件。](http://greaseblog.blogspot.com/2005/07/mandatory-greasemonkey-update.html)

Greasemonkey 扩展提供了安装和运行与特定站点或互联网上所有站点相关联的脚本的工具。这些脚本使用标准的 JavaScript 特性和语法，但是扩展还提供了一组可供用户脚本使用的扩展函数。这些功能是安全漏洞的来源。

一旦用户脚本与站点相关联，这些扩展功能不仅对用户脚本可用，而且对站点本身内的任何脚本代码都可用。恶意站点可能会等到用户带着为该站点启用的 Greasemonkey 脚本出现，然后使用扩展功能访问存储在用户系统上的私有文件和数据。由于许多 Greasemonkey 脚本旨在增强 Web 上的所有站点(因此对所有站点都启用)，这是一个非常严重的问题。

最令人担心的扩展函数是 GM_xmlhttpRequest 函数，它允许用户脚本(由于这个安全漏洞，这是一个恶意站点)对任何 URL 发出 GET 和 POST 请求，甚至在当前站点的域之外。通过使用它来请求 file:// URL，恶意站点可以读取系统上任何文件的内容，甚至获得本地目录列表。然后，该脚本可以发出 POST 请求，将该信息发送到任何 URL。

在开发人员寻找这些安全问题的良好解决方案时，他提供了一个新版本的扩展， [Greasemonkey 0.3.5](http://atrus.org/hosted/greasemonkey-0.3.5.xpi) ，它删除了对所有扩展函数的支持，包括 GM_xmlhttpRequest。任何依赖于这个特性的脚本都将无法与这个“中性”版本一起工作，但是仅仅调整现有站点布局/功能的简单脚本应该可以正常工作。

## 分享这篇文章