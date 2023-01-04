# 服务器端 JavaScript 将和 PHP 一样普遍

> 原文：<https://www.sitepoint.com/server-side-javascript-will-be-as-common-as-php/>

通读 Craig Buckler 的博客文章 *[的评论，服务器端 JavaScript 会流行起来吗？](https://www.sitepoint.com/server-side-javascript/)* 证实了[道格拉斯·克洛克福特对 JavaScript](http://www.crockford.com/javascript/javascript.html) 的描述:它已经定型了。很多人只能在浏览器中看到它。很大一部分原因是由于语言和浏览器 DOM 之间的混淆。DOM 接口实际上是大多数 JavaScript 程序员花费时间的地方。

这也证实了另一个事实:很多人讨厌 JavaScript。不过，我相信——对于欣赏 JavaScript 的优秀特性并能接受其粗糙部分的程序员来说——JavaScript 正在向服务器端转移的消息并不令人惊讶，而且可能是受欢迎的。我确信这只是开始。

## 那么现在在服务器端哪里可以找到 JavaScript 呢？

[Jaxer](http://www.aptana.com/jaxer/) 是一个 JavaScript web 应用框架和服务器。服务器端 API 的能力不亚于任何 API，包括对数据库、文件和网络套接字的访问。服务器端和客户端脚本之间的界限越来越模糊；例如，服务器端脚本可以操作网页 DOM。

脚本以 ASP 风格嵌入到您的 HTML 中:

```
<p id="msg"></p>
<script runat="server">
  var nme = document.createTextNode(
      "Hello my name is Jaxer.");
  var para = document.getElementById("name");
  para.appendChild(nme);
</script>
```

`runat`属性可以是`server`、`both`或`server-proxy`。如果设置为`server`，，则在页面发送到浏览器之前会对脚本进行评估。如果没有设置，脚本将在客户端执行。如果设置为`server-proxy`，那么函数可以通过名字从客户端脚本调用，但是通过 Ajax 代理到服务器端的对等函数。

Helma 是另一个使用 JavaScript 进行服务器端脚本编写的 web 应用程序框架。Helma 有一个模板系统，这意味着它避免了混合服务器端和客户端 JavaScript 代码。取而代之的是用 JavaScript 编写*动作*，然后这些动作呈现模板，将数据注入到呈现过程中。每个 HTTP 请求都会触发一个已配置的操作。

这里有一个 Helma 术语中的模板或*皮肤*的例子，名为“hello”:

```
<p>Hello, my name is <% response.name %>.</p>
```

以及呈现它的动作:

```
res.data.name = 'Helma';
this.renderSkin('hello');
```

维基百科上的[服务器端 JavaScript](http://en.wikipedia.org/wiki/Server-side_JavaScript) 主题展示了更多服务器端 JavaScript 的例子。他们几乎都使用[犀牛](http://www.mozilla.org/rhino/)或[蜘蛛猴](http://www.mozilla.org/js/spidermonkey/)来执行 JavaScript。

## 服务器端 JavaScript 是一个严肃的命题吗？

虽然 JavaScript 在服务器上的实现正在出现，但它远没有 PHP 托管那么普遍。公平的评论是，服务器端 JavaScript 目前被绑定到它所在的框架。因此，在一种环境中编写的 JavaScript 不太可能是可移植的，因为缺乏标准的 API。这是一个已经被确认的需求，所以 ServerJS 集团的工作已经开始。像 [jslibs](http://code.google.com/p/jslibs/) 这样的项目也是为了解决这个问题。

缺乏托管服务也是一个问题，尽管 Jaxer 和 T2 app jet 提供了他们自己的托管平台。Helma 应用程序可以由支持 Java 的服务托管。一旦标准库的问题被解决，我确信我们会看到主机支持的改进——你会要求你的主机提供“mod_javascript”支持。

最后，JavaScript 有太多粗糙的部分，让许多人对在服务器端使用它感到紧张。我真的认为 ECMAScript 3.1 和 ECMAScript Harmony 最终会解决这个问题。

我们看到 JavaScript 支持出现在许多平台上，包括 web 和桌面、本地和服务器。托管包中提供的服务器端 JavaScript 支持会像 PHP 一样普遍吗？我觉得这是必然的。

## 分享这篇文章