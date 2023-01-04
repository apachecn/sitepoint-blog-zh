# Acrobat JavaScript 简介

> 原文：<https://www.sitepoint.com/an-introduction-to-javascript-for-acrobat/>

作为一个 JavaScript 爱好者，我总是对 JavaScript 在非浏览器平台上的工作方式感兴趣。您可能没有意识到这一点，但是 Adobe Acrobat 有一个完整的 JavaScript API，您可以使用它为 PDF 文件添加交互性。所以我想从 web 开发人员的角度研究一下 Acrobat 中可用的 JavaScript 支持。

PDF 文件中的 JavaScript 用于与书签、注释、链接、按钮、自定义对话框、嵌入媒体、表单、搜索等等进行交互。它可以在应用程序级别和批处理操作中使用，但也可以保存为 PDF 文件。当它保存在 PDF 文件中时，兼容的浏览器应用程序能够运行 JavaScript。

## 真的是 JavaScript 吗？

在 web 开发中，我们对 JavaScript 的使用与浏览器的 DOM API 交织在一起，我们通常不会将二者分开。 [Acrobat 9](http://www.adobe.com/products/acrobat/) ，最近发布的 [Adobe Creative Suite 4](http://www.adobe.com/products/creativesuite/) 的大部分版本的组件，支持 JavaScript 1.7，拥有所有你习惯喜欢的顶级对象`Date`和`RegExp`。您可以使用闭包，函数可以作为对象传递。对象属性可以通过它们的名称或点符号来访问；`app.language`和`app["language"]`都是指同一个物业。

Acrobat 有自己的文档 API 和各种支持对象。我发现的第一个奇怪之处是,`this`关键字总是指向一个引用当前 PDF 文档的`Doc`对象；类似于浏览器中的`window`对象。即使在`button`对象的`MouseUp`事件的上下文中，`this`仍然是对当前文档的引用。尽管如此，`Function`对象的`apply`方法仍然可以用来改变`this`关键字所指的内容。

## Acrobat JavaScript API

API 使用命名参数风格向函数传递参数，这种风格在 JavaScript 库中很流行。每个 API 函数都可以将一组键/值对作为单个参数。例如，我们调用`app.alert`函数——与浏览器的`alert`函数非常相似——如下所示:

```
var result = app.alert({
  cMsg: "Are you going to click it again?",
  cTitle: "You've clicked the Big Red Button!",
  nIcon: 2,
  nType: 2
});
```

值`cMsg`和`cTitle`分别设置警告对话框消息和标题。显示的图标和按钮由最后两个值指定。指定的按钮也决定了可能的返回值。我们已经指定了`2`的`nType`，所以对话框将显示一个是和一个否按钮，如果点击否则返回`3`，如果点击是则返回`4`。

![](img/a499ed8161015c104bc0eb7ba42147c5.png)

事件处理是使用动作实现的。例如，如果我们将上面对`app.alert`的调用包装在一个名为`getChoice`的函数中，那么我们可以将它设置为在单击按钮时调用:

```
button.setAction("MouseUp", "getChoice()");
```

## 程序员特征

Acrobat 有一个带有交互式控制台的 JavaScript 调试器，但是 JavaScript 编辑器还有许多不足之处。你得到一个纯文本框，在里面输入；没有语法突出显示，没有代码提示，没有代码完成——这就像使用 Windows 记事本编辑一样，因为也没有撤销，所以变得更糟。幸运的是，你可以使用外部编辑器。不过，它有一个可取之处，那就是语法检查。如果 JavaScript 有语法错误，您将无法保存它；我希望更多的文本编辑器拥有这个特性。

![](img/a87cda3aeb0cb3c34ec4ab9d275f4839.png)

## 高级 JavaScript

Acrobat 中的 JavaScript 有许多你在浏览器中找不到的功能。直接数据库访问由 ADBC 对象提供，SOAP 对象允许使用 SOAP 协议访问 web 服务。JavaScript for Acrobat 具有读写文件和数据流的能力，E4X 是 JavaScript XML 处理扩展。但是，这些功能中的大多数是为企业使用而不是一般的 web 使用而设计的，因为有些需要 Acrobat Professional，有些需要 Adobe Reader 中的安全级别提升。

## 和睦相处

兼容的查看应用程序包括 Adobe Acrobat 和 Adobe Reader。API 的一些更高级的部分仅适用于 Acrobat Professional。兼容的创作应用程序包括 Adobe Acrobat Professional 和开源桌面出版应用程序 [Scribus](http://www.scribus.net/) 。JavaScript 支持可以在其他 PDF 开发者库中找到，比如 [PDFDoc Scout](http://bytescout.com/pdfdocscout.html) for。NET，以及 Java 的 [JPedal](http://www.jpedal.org/index.php) 和 [iText](http://www.lowagie.com/iText/)

## 获得帮助

没有这些基本文档你将一事无成:*JavaScript for Acrobat API 参考*和*使用 JavaScript 开发 Acrobat 应用*。这些文档和其他资源都可以在[网页上找到。不幸的是，你可能会发现那里的信息有点过时。您也可以在 Acrobat 9 SDK 在线帮助网站中找到相同的信息。](http://www.adobe.com/devnet/acrobat/javascript.html)

## 分享这篇文章