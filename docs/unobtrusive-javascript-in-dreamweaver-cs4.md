# Dreamweaver CS4 中不引人注目的 JavaScript

> 原文：<https://www.sitepoint.com/unobtrusive-javascript-in-dreamweaver-cs4/>

最近发布的 Adobe Dreamweaver CS4 的副本最近出现在 SitePoint 总部(查看 Kevin 在第 218 期的评论)，所以我想我应该试用一下，看看它能为现代 JavaScripter 提供什么。

当我想到 dreamweaver 和 javaScript 时，我会想起 JavaScript 函数，它在所有 Dreamweaver 创建的网站上都无处不在。自那以后，Dreamweaver 已经成熟了很多，很明显，Adobe 试图跟上不引人注目的 JavaScript 的现代趋势。Dreamweaver 在几个重要方面支持 JavaScript 属于外部文件的现代概念。

如果打开一个 HTML 文件，相关文件工具栏将为每个引用的外部文件添加一个按钮；甚至对于远程文件，例如来自 [Google AJAX Libraries API](http://code.google.com/apis/ajaxlibs/) 的 JavaScript(尽管远程文件是不可编辑的)。

![](img/2624baad0390d78a133185f781bcdfc8.png)

更好的是，当你使用实时视图时——一个嵌入式的 [Webkit](http://webkit.org/) 窗口，Safari 和 [Chrome](http://www.google.com/chrome) 中使用的引擎——你可以使用相关的文件工具栏将代码视图切换到外部文件，而不会中断你的实时视图会话:打开一个外部 javascript 文件，进行更改，你的更改会立即反映在实时视图窗口中，而不必保存 JavaScript 文件。

更好的是，Live View 还有一个实时代码模式，在这种模式下，HTML 源代码视图变成了计算源代码视图；它显示当前 DOM 的源，而不是原始的网页源。使用实时代码，您可以在与实时视图窗口中的网页交互时看到 DOM 中的变化，类似于 Firebug。如果需要停下来检查 DOM 的当前状态，实时代码还允许您冻结 JavaScript。

JavaScript 编辑器具有语法检查和代码提示功能。语法检查在您键入时执行，为语法问题提供即时反馈。Dreamweaver CS4 中的代码提示包括所有引用的 JavaScript 文件中的函数和对象，这意味着它可以与任何 JavaScript 库一起工作。当你输入函数和对象的时候，它也会包含它们。

![](img/61e453eeb52f3a15e0a43c4f9728c5c7.png)

不过有一个缺点；它不适用于远程文件；JavaScript 必须是本地的，才能包含在代码提示中。

一个非常有趣的特性是外部化 JavaScript 命令。假设您一直在使用内联 javaScript 模仿一个网页，或者您继承了一个充满嵌入式脚本标签的站点；使用此命令，Dreamweaver 将获取 HTML 文件中的所有 JavaScript 代码，并创建一个外部 JavaScript 文件，然后向 HTML 添加一个新的脚本标记。它还更进一步。如果你有任何内联的 javascript even 处理程序，比如`onclick` HTML 属性，Dreamweaver 会把它们转换成一个使用 [Adobe Spry JavaScript 框架](http://labs.adobe.com/technologies/spry/)的低调版本。

![](img/2c13b466078d700e9c356ac29912099d.png)

例如，如果你有一个这样的内联事件处理程序:

```
<a href="#" onclick="myObj.functionOne();">...</a>
```

运行该命令后，HTML 被转换为:

```
<a href="#" id="a1">...</a>
```

然后，将以下内容放在外部 JavaScript 文件中，不引人注目地将事件侦听器添加到 anchor 标记中:

```
Spry.Utils.addLoadListener(function() {
	Spry.$$("#a1").addEventListener('click', function(e){ myObj.functionOne(); }, false);
});
```

这不是一个完美的特性；您受限于 Spry 框架(如果您可以提名使用您选择的 JavaScript 框架，那将是理想的)，脚本标签被放置在文档头中(目前的最佳实践是将它们放在结束的 body 标签之前)；但这无疑说明了对不引人注目的 JavaScript 的日益重视。

## 分享这篇文章