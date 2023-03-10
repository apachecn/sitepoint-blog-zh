# 谷歌发布其 JavaScript 闭包工具

> 原文：<https://www.sitepoint.com/google-open-source-javascript-closure-library/>

新的 JavaScript 库和框架一直在出现，但谷歌并不是每天都发布支持搜索、GMail、谷歌地图、谷歌文档等的客户端代码。该公司已经开源了他们的闭包工具，现在可以从谷歌代码实验室(T2)下载。

为热切的客户端开发人员提供了三个系统:

## 1.Google 闭包编译器

“编译器”是一个令人困惑的术语——这是一个 Java 驱动的 JavaScript 优化器，它通过删除死代码、重命名变量以及删除空白和注释来减小文件大小。

您可以:

1.  [下载闭包编译器](http://code.google.com/p/closure-compiler/downloads/list)，或者
2.  使用在线闭包编译器工具。

在我的简短测试中，一个 28Kb 的 JavaScript 文件使用“简单”压缩删除了空白和注释，减少到了 15Kb(减少了 46%)。缩减系数与雅虎的 YUI 压缩器几乎相同。

使用变量和函数重命名的“高级”压缩将文件大小减少到 10Kb 以下(减少 64%)。代码继续正常工作，尽管产生了一些关于未初始化变量的 JavaScript 警告，这在未压缩版本中并不明显。

如果您想加快 web 页面的下载速度，闭包编译器当然值得一试。但是，要小心地全面测试生成的 JavaScript 代码。

## 2.Google Closure 库

[闭包库](http://code.google.com/closure/library/)是 [jQuery](http://jquery.com/) 或 [YUI 库](http://developer.yahoo.com/yui/)的替代。常见的功能是可用的，例如，助手功能，Ajax，DOM 操作，事件处理程序，CSS 控制，动画，效果等。

代码、注释和[文档](http://closure-library.googlecode.com/svn/trunk/closure/goog/docs/index.html)总体来说不错，尽管我找不到关于浏览器支持的信息。有些例子有点基础，但我怀疑他们会随着时间的推移而改进。

Kevin Yank 最近的文章中有 Dmitry Baranovskiy 的评论，他表达了对代码质量的担忧。它们是有效的批评，我怀疑关闭会赢得许多 jQuery 爱好者，但选择总是一件好事，现在它在公共领域，库将会改进。

## 3.Google 闭包模板

[闭包模板](http://code.google.com/closure/templates/)是客户端 JavaScript 和服务器端 Java 的模板系统。这是一个允许你添加小的语言无关的组件来创建一个完整的用户界面的系统。

文档很少，例子也很少。服务器端 Java 开发人员可能会采用这个系统，但我不认为它会吸引 ASP.NET 或 PHP 开发人员。

你会尝试谷歌的闭包工具吗？或者工具的选择变得如此令人困惑，你会坚持使用你所知道的？

另见: [Google Closure:如何不写 JavaScript](https://www.sitepoint.com/google-closure-how-not-to-write-javascript/)

## 分享这篇文章