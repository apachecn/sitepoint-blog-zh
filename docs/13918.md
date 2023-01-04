# AJAX 应用程序的书签和后退按钮历史

> 原文：<https://www.sitepoint.com/bookmarks-and-back-button-history-for-ajax-apps/>

除了确实很严重的可访问性问题，单页面 AJAX 应用程序(大部分或全部在单个页面内工作的 Web 应用程序，如 [GMail](http://mail.google.com/) )的最大问题是 Web 浏览器提供的标准导航工具——书签/收藏夹和后退/前进按钮——变得完全无用。

现在，同一个聪明的猴子开发了前面提到的客户端数据存储系统 I，为这些问题带来了一个非常有希望的部分解决方案。

[真正简单的历史](http://codinginparadise.org/projects/dhtml_history/README.html)是一个让你将页面锚名与应用状态关联起来的脚本(例如`page.html#*state*`)。每当应用程序进入一个新的状态(例如编辑一个博客条目)时，该脚本通过将用户链接到一个新的锚点来工作。然后，该脚本监视当前 URL 中对当前锚点的更改(由于后退/前进导航)，并触发您指定的函数，以便您可以相应地更新应用程序状态。

显然这并不像我理解的那么简单，但是有了这个库，你需要编写的支持它的代码实际上出奇的简单。目前，Safari 还不被支持，但看看它是如何工作的，我不会对不久后支持该浏览器感到惊讶。

如果你是一个 JavaScript 爱好者，脚本的网站展示了你需要的所有样本代码。经验不太丰富的开发人员会想看看脚本作者在 O'Reilly Network 上的[文章。](http://www.oreillynet.com/pub/a/onjava/2005/10/26/ajax-handling-bookmarks-and-back-button.html)

希望像 GMail 这样的大型 AJAX Web 应用程序能够引领潮流，将这一功能快速添加到它们的产品中。现在，在单页 AJAX 应用程序中，正确的书签和导航已经成为可能，我不会强烈反对以这种方式开发我的下一个 Web 应用程序。

## 分享这篇文章