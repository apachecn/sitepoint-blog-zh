# Python 服务器页面

> 原文：<https://www.sitepoint.com/python-server-pages/>

通过[西蒙的博客](http://simon.incutio.com/)……[Python 服务器页面](http://www.onlamp.com/pub/a/python/2004/02/26/python_server_pages.html)。

*终于！*

个人认为这是个好消息。到目前为止(忽略已经尝试过的第三方 Python 项目)，mod_python 有一个笨拙的部署机制(很像 mod_perl，如果你知道的话)，需要在 Apache 中定义处理程序。Python 服务器页面与创建 PHP 页面非常相似——只需告诉 Apache 注意*。psp* 文件，然后你就可以走了。

如果你想知道为什么我在 PHP 博客上谈论 Python，我没有明确的答案给你。Python 是一种设计非常好的语言。对我来说，可能是最令人愉快的工作，在动态类型家族之外(例如 Perl、PHP、JavaScript 和 Ruby)。

作为一个 web 应用程序的解决方案，PHP 比 Python 有一个显著的优势，它拥有主机提供商的大量支持和几乎所有你需要的功能。正如 John Lim 在这里建议的那样，支持 PHP 项目也很重要。

也就是说，Python 提供了所有让 PHP 开发人员感兴趣的东西，比如对名称空间的支持和一个内置的(即免费的)机制，它可以存储脚本的“编译”形式。像 [distutils](http://www.python.org/doc/current/lib/module-distutils.html) 这样的东西解决了许多分发你的作品的难题。

所以 Python 服务器页面是 PHP 的一个替代品。也许竞争是一件好事，PHP 现在更像是一个垄断。

原来[斯特林·休斯](http://edwardbear.org/serendipity/)，PHP 的核心开发者之一，为 [mod_python](http://www.modpython.org/) 的 PSP 插件提供了基础，正如他不久前提到的[这里](http://www.edwardbear.org/blog/archives/000224.html#000224)。你呢，畜生？；)

## 分享这篇文章