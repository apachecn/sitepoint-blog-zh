# JavaScript MIME 类型:做就该死，不做就该死！

> 原文：<https://www.sitepoint.com/javascript-mime-type-damned-if-you-do-damned-if-you-dont/>

在他不久前发布的一篇[文章](https://www.sitepoint.com/web-site-optimizer-tool/)中，Alex Walker 提到了他在试图添加 [`type`属性](https://reference.sitepoint.com/html/script/type)时遇到的一些麻烦。Alex 嵌入的 Google 脚本没有`type`属性，但是当他添加一个属性时就无法工作了。道格拉斯·克洛克福特在他的[高级 JavaScript](http://video.yahoo.com/watch/111585/1027823) 视频中建议我们完全放弃类型属性，因为浏览器默认使用 JavaScript。问题出在 [HTML 4.01](https://www.w3.org/TR/html401/interact/scripts.html#edef-SCRIPT) 和 [XHTML 1.0](https://www.w3.org/TR/xhtml1/dtds.html#dtdentry_xhtml1-strict.dtd_script) 下`type`属性是必需的。所以如果你像 Alex 一样关心验证，那么你会想要添加它。

但是，对于 JavaScript 来说什么是正确的`type`值呢？Crockford 先生提出放弃该属性的另一个原因是，大多数人使用的值`text/javascript`是错误的！据 [rfc4329](http://www.ietf.org/rfc/rfc4329.txt) 称，已经过时了。安妮·范·凯斯特伦也证实了这一点，她已经在[报道过这个问题](http://annevankesteren.nl/2006/05/javascript-mime-type)(早在 2006 年 5 月——是的，安妮是大师之一，而我不是),以及[site point HTML 参考](https://reference.sitepoint.com/html/script/type)。

JavaScript 的正确的`type`值实际上是`application/javascript`。但是你难道不知道，如果你使用那个`type`值，Internet Explorer 将不会执行代码。所以这里有一个糟糕的情况，要生成一个带有脚本标签的有效 HTML 或 XHTML 文档，我们需要使用一个`type`属性，但是要让它在所有浏览器上运行，我们必须使用一个不正确的值。

这表明，网络实际上仍然只是一个 alpha 版本。等不及第一个测试版了！

## 分享这篇文章