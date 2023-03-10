# “脚本错误”到底是什么意思？

> 原文：<https://www.sitepoint.com/what-the-heck-does-script-error-mean/>

*本文是与 [Sentry.io](https://synd.co/2KokRJ8) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

如果您以前使用过 JavaScript `onerror`事件，您可能会遇到以下情况:

> `Script error.`

“脚本错误”是当错误来自不同来源(不同的域、端口或协议)的 JavaScript 文件时，浏览器发送给 [onerror](https://blog.sentry.io/2016/01/04/client-javascript-reporting-window-onerror?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship) 回调的内容。这很痛苦，因为即使有错误发生，你也不知道*什么是*错误，也不知道错误来自*哪个*代码。这就是`window.onerror`的全部目的——深入了解应用程序中未被发现的错误。

## 原因:跨源脚本

为了更好地理解发生了什么，考虑下面的 HTML 文档示例，假设它来自 http://example.com/test:

```
<!doctype html>
<html>
<head>
  <title>example.com/test</title>
</head>
<body>
  <script src="http://another-domain.com/app.js"></script>
  <script> window.onerror = function (message, url, line, column, error) {
    console.log(message, url, line, column, error);
  }
  foo(); // call function declared in app.js </script>
</body>
</html> 
```

这是 http://another-domain.com/app.js.的内容，它声明了一个函数，foo，它的调用总是抛出一个 ReferenceError。

```
// another-domain.com/app.js
function foo() {
  bar(); // ReferenceError: bar is not a function
} 
```

当这个文档被加载到浏览器中，并且 JavaScript 被执行时，以下内容被输出到控制台(通过`window.onerror`回调记录):

> `"Script error.", "", 0, 0, undefined`

这不是一个 [JavaScript 错误](https://sentry.io/for/javascript/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship)——出于安全原因，浏览器有意隐藏来自不同来源的脚本文件的错误。这是为了避免脚本无意中将潜在的敏感信息泄露给不受其控制的`onerror`回调。出于这个原因，浏览器只让`window.onerror`了解来自同一个域的错误。我们只知道发生了一个错误—没有别的了！

## 我不是坏人，真的！

尽管浏览器的意图是好的，但是有一些真正好的理由让您了解来自不同来源的脚本所抛出的错误:

1.  您的应用程序 JavaScript 文件由不同的主机名提供(例如 static.sentry.io/app.js)。
2.  你使用的是社区 CDN 提供的图书馆，比如 cdnjs 或 Google 托管的图书馆。
3.  您正在使用一个只从外部服务器提供服务的商业第三方 JavaScript 库。

但是不用担心！深入了解由这些文件提供的 JavaScript 错误只需要一些简单的调整。

## 解决方法:CORS 属性和标题

为了了解不同来源的脚本抛出的 JavaScript 异常，您必须做两件事。

### 1.添加一个`crossorigin="anonymous"`脚本属性

```
<script src="http://another-domain.com/app.js" crossorigin="anonymous"></script> 
```

这告诉浏览器应该“匿名”获取目标文件这意味着在请求该文件时，浏览器不会向服务器传输任何潜在的用户标识信息，如 cookies 或 HTTP 凭据。

### 2.添加跨来源 HTTP 标头**

```
Access-Control-Allow-Origin: \* 
```

CORS 是跨来源资源共享的缩写，它是一组 API(主要是 HTTP 头),规定了文件应该如何跨来源下载和服务。

通过设置`Access-Control-Allow-Origin: \*`，服务器向浏览器表明任何来源都可以获取该文件。或者，您可以将其限制在您控制的已知原点:

```
$ curl --head https://ajax.googleapis.com/ajax/libs/jquery/2.2.0/jquery.js | \
    grep -i "access-control-allow-origin"

Access-Control-Allow-Origin: * 
```

一旦完成了这两个步骤，该脚本触发的任何错误都将报告给`window.onerror`，就像任何常规的同域脚本一样。因此，开头的`onerror`示例将产生以下结果，而不是“脚本错误”:

```
"ReferenceError: bar is not defined", "http://another-domain.com/app.js", 2, 1, [Object Error] 
```

嘣！您完成了—“脚本错误”将不再困扰您和您的团队。

## 备选方案:`try/catch`

有时我们无法调整 web 应用程序正在使用的脚本的 HTTP 头。在这些情况下，有一种替代方法:使用`try/catch`。

再次考虑最初的例子，这次是用`try/catch`:

```
<!-- note: crossorigin="anonymous" intentionally absent -->
<script src="http://another-domain.com/app.js"></script>
<script>
window.onerror = function (message, url, line, column, error) {
  console.log(message, url, line, column, error);
}

try {
  foo(); // call function declared in app.js
} catch (e) {
  console.log(e);
  throw e; // intentionally re-throw (caught by window.onerror)
}
</script> 
```

对后代来说，some-domain.com/app.js 再一次看起来是这样的:

```
// another-domain.com/app.js
function foo() {
  bar(); // ReferenceError: bar is not a function
} 
```

运行示例 HTML 将向控制台输出以下两个条目:

```
=> ReferenceError: bar is not defined
     at foo (http://another-domain.com/b.js:2:3)
     at http://example.com/test/:15:3

=> "Script error.", "", 0, 0, undefined 
```

第一个控制台语句——来自`try/catch`——设法获得一个错误对象，包括类型、消息和堆栈跟踪，包括文件名和行号。来自`window.onerror`的第二个控制台语句同样只能输出“脚本错误”。

现在，这是否意味着你需要`try/catch`你所有的代码？大概不会。如果您可以轻松地更改 HTML 并在 cdn 上指定 CORS 标题，那么这样做并坚持使用`window.onerror`会更好。

但是，如果您不控制这些资源，使用`try/catch`来包装第三方代码是一种获得跨来源脚本抛出的错误的可靠方法(尽管很乏味)。

注意:默认情况下，raven.js、 [Sentry 的 JavaScript SDK](https://github.com/getsentry/raven-js) ，小心翼翼地植入内置方法，试图自动将你的代码包装在`try/catch`块中。它这样做是为了试图从所有脚本中捕获错误消息和[堆栈跟踪](https://sentry.io/features/stacktrace/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship)，而不管它们是从哪个来源提供的。如果可能的话，仍然建议设置 CORS 属性和头。

当然，有许多商业和开源工具可以为您完成客户端报告的所有繁重工作。(Psst:你可能想[试试 Sentry](https://sentry.io/signup?trial&plan=medium&period=annual&utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship) 调试 JavaScript。)

就是这样！开心[错误监控](https://sentry.io/welcome/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship)。

## 分享这篇文章