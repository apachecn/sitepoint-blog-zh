# AJAX 陷阱

> 原文：<https://www.sitepoint.com/ajax-gotchas/>

Alex Bosworth 整理了一份[的“陷阱”清单](http://www.sourcelabs.com/blogs/ajb/2006/04/rocky_shoals_of_ajax_developme.html)，新的 AJAX 开发人员似乎经常成为这些陷阱的牺牲品。他还在维基上公布了这个列表，这样读者就可以贡献出他们自己常见的 AJAX 缺陷。

在这些陷阱中，有一个对我最近关于字符编码的报道更有帮助:

> Ajax 使用的是 UTF 8。使用父页面的编码发送普通表单。因此，SJIS 编码的页面将默认发送用 SJIS 编码的表单内容。另一方面，Ajax 提交的表单将作为 UTF-8 发送。如果由于某种奇怪的原因，UTF-8 不是服务器选择的字符集，这将需要一种解决方案，例如服务器识别 UTF-8 响应并将其翻译成所需的字符编码。

特别是对于 PHP 开发人员来说，[对 UTF-8](https://www.sitepoint.com/php-utf-8-01/) 的开箱即用支持有限，这使得许多网站采用单字节字符编码，这个问题可能会导致令人讨厌的意外。例如，如果您想添加通过 AJAX 提交表单的功能，并保留标准提交方法作为备用，那么您可能最终不得不支持提交数据的两种不同编码！

## 分享这篇文章