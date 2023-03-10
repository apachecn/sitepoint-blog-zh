# 用 window.onerror 捕获并报告 JavaScript 错误

> 原文：<https://www.sitepoint.com/capture-and-report-javascript-errors-with-window-onerror/>

*本文是与[哨兵](https://synd.co/2Juq2Xn)合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

`onerror`是一个特殊的浏览器事件，每当抛出一个未被捕获的 [JavaScript 错误](https://sentry.io/for/javascript/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship)时就会触发。这是记录客户端错误并将它们报告给服务器的最简单的方法之一。这也是 [Sentry 的客户端 JavaScript 集成](https://docs.sentry.io/hosted/clients/javascript/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship) (raven-js)工作的主要机制之一。

您通过给`window.onerror`分配一个函数来监听 onerror 事件:

```
window.onerror = function (msg, url, lineNo, columnNo, error) {
  // ... handle error ...

  return false;
} 
```

当抛出错误时，以下参数被传递给函数:

*   **消息**–与错误相关的消息，例如“未捕获的引用错误:foo 未定义”
*   **url**–与错误相关的脚本或文档的 URL，例如“/dist/app.js”
*   **行号**–行号(如果有)
*   **列号**–列号(如果有)
*   **错误**–与该错误相关联的错误对象(如果可用)

前四个参数告诉您错误发生在哪个脚本、行和列中。最后一个参数，错误对象，可能是最有价值的。我们来了解一下原因。

## 错误对象和 error.stack

乍一看，错误对象并不特别。它包含 3 个标准化属性:**消息**、**文件名**和**行号**。已经通过`window.onerror`提供给您的冗余值。

贵重部分为*非标准*财产:`Error.prototype.stack`。这个 stack 属性告诉你当错误发生时程序的每一帧在什么源位置。[错误堆栈跟踪](https://sentry.io/features/stacktrace/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship)可能是调试的关键部分。尽管是非标准的，但这一特性在所有现代浏览器中都可用。

下面是 Chrome 46 中 Error 对象的 stack 属性的一个例子:

```
"Error: foobar\n    at new bar (<anonymous>:241:11)\n    at foo (<anonymous>:245:5)\n    at <anonymous>:250:5\n    at <anonymous>:251:3\n    at <anonymous>:267:4\n    at callFunction (<anonymous>:229:33)\n    at <anonymous>:239:23\n    at <anonymous>:240:3\n    at Object.InjectedScript._evaluateOn (<anonymous>:875:140)\n    at Object.InjectedScript._evaluateAndWrap (<anonymous>:808:34)" 
```

很难读，对吧？stack 属性实际上只是一个无格式的字符串。

以下是格式化后的样子:

```
Error: foobar
    at new bar (<anonymous>:241:11)
    at foo (<anonymous>:245:5)
    at callFunction (<anonymous>:229:33)
    at Object.InjectedScript._evaluateOn (<anonymous>:875:140)
    at Object.InjectedScript._evaluateAndWrap (<anonymous>:808:34) 
```

一旦被格式化，就很容易看出 stack 属性在帮助调试错误时是多么重要。

只有一个障碍:stack 属性是非标准的，并且它的实现在不同的浏览器中是不同的。例如，下面是来自 Internet Explorer 11 的相同堆栈跟踪:

```
Error: foobar
   at bar (Unknown script code:2:5)
   at foo (Unknown script code:6:5)
   at Anonymous function (Unknown script code:11:5)
   at Anonymous function (Unknown script code:10:2)
   at Anonymous function (Unknown script code:1:73) 
```

不仅每帧的格式不同，而且帧的细节也较少。例如，Chrome 识别出已经使用了`new`关键字，并且对`eval`调用有更深入的了解。这只是 IE 11 与 Chrome 的对比——其他浏览器同样有不同的格式和细节。

幸运的是，有一些工具可以标准化堆栈属性，使其在不同浏览器间保持一致。例如，raven-js 使用 TraceKit 来规范化错误字符串。还有 stacktrace.js 和其他几个项目。

## 浏览器兼容性

已经在浏览器中使用了一段时间——你会在 IE6 和 Firefox 2 等老浏览器中找到它。

问题是每个浏览器实现`window.onerror`的方式不同，特别是发送给 onerror 监听器的参数数量以及这些参数的结构。

下表列出了大多数浏览器中传递给 onerror 的参数:

| **浏览器** | **消息** | **网址** | **lineNo** | **列号** | **错误对象** |
| --- | --- | --- | --- | --- | --- |
| 火狐浏览器 | -好的 | -好的 | -好的 | -好的 | -好的 |
| 铬 | -好的 | -好的 | -好的 | -好的 | -好的 |
| 边缘 | -好的 | -好的 | -好的 | -好的 | -好的 |
| IE 11 | -好的 | -好的 | -好的 | -好的 | -好的 |
| IE 10 | -好的 | -好的 | -好的 | -好的 |  |
| IE 9，8 | -好的 | -好的 | -好的 |  |  |
| Safari 10 及以上版本 | -好的 | -好的 | -好的 | -好的 | -好的 |
| Safari 9 | -好的 | -好的 | -好的 | -好的 |  |
| 安卓浏览器 4.4 | -好的 | -好的 | -好的 | -好的 |  |

Internet Explorer 8、9 和 10 对 onerror 的支持有限，这可能不足为奇。但你可能会惊讶，Safari 只是在 Safari 10(2016 年发布)中增加了对 error 对象的支持。此外，仍然使用传统 Android 浏览器(现在被 Chrome Mobile 取代)的旧手机仍然存在，并且没有传递错误对象。

没有 error 对象，就没有堆栈跟踪属性。这意味着这些浏览器不能从 onerror 捕获的错误中检索有价值的堆栈信息。

## 使用 try/catch 多填充 window.onerror

但是有一个解决方法——您可以将应用程序中的代码包装在 try/catch 中，并自己捕捉错误。这个错误对象将包含我们在所有现代浏览器中梦寐以求的`stack`属性。

考虑下面的 helper 方法`invoke`，它用一个参数数组调用对象上的函数:

```
function invoke(obj, method, args) {
    return obj[method].apply(this, args);
}

invoke(Math, 'max', [1, 2]); // returns 2 
```

这里是`invoke`,这次是用 try/catch 包装的，目的是捕获任何抛出的错误:

```
function invoke(obj, method, args) {
  try {
    return obj[method].apply(this, args);
  } catch (e) {
    captureError(e); // report the error
    throw e; // re-throw the error
  }
}

invoke(Math, 'highest', [1, 2]); // throws error, no method Math.highest 
```

当然，在任何地方手动操作都很麻烦。您可以通过创建一个通用包装器实用函数来简化这一过程:

```
function wrapErrors(fn) {
  // don't wrap function more than once
  if (!fn.__wrapped__) {
    fn.__wrapped__ = function () {
      try {
        return fn.apply(this, arguments);
      } catch (e) {
        captureError(e); // report the error
        throw e; // re-throw the error
      }
    };
  }

  return fn.__wrapped__;
}

var invoke = wrapErrors(function(obj, method, args) {
  return obj[method].apply(this, args);
});

invoke(Math, 'highest', [1, 2]); // no method Math.highest 
```

因为 JavaScript 是单线程的，所以不需要到处使用 wrap 只需要在每个新栈的开始使用。

这意味着您需要包装函数声明:

*   在应用程序启动时(例如，如果使用 jQuery，在`$(document).ready`中)
*   在事件处理程序中(如`addEventListener`或`$.fn.click`)
*   基于计时器的回调(例如，`setTimeout`或`requestAnimationFrame`)

例如:

```
$(wrapErrors(function () { // application start
  doSynchronousStuff1(); // doesn't need to be wrapped

  setTimeout(wrapErrors(function () {
    doSynchronousStuff2(); // doesn't need to be wrapped
  });

  $('.foo').click(wrapErrors(function () {
    doSynchronousStuff3(); // doesn't need to be wrapped
  });
})); 
```

如果这看起来工作量很大，不要担心！大多数错误报告库都有增加内置函数的机制，比如`addEventListener`和`setTimeout`，这样你就不必每次都自己调用包装工具。是的，raven-js 也是这样做的。

## 将错误传送到服务器

好了，您已经完成了您的工作——您已经插入了`window.onerror`,并且您还在 try/catch 中包装了一些函数，以便捕获尽可能多的错误信息。

还有最后一步:将错误信息传输到您的服务器。为了实现这一点，您需要建立某种类型的报告 web 服务，该服务将通过 HTTP 接受您的错误数据，将其记录到文件中和/或存储在数据库中。

如果这个 web 服务与您的 web 应用程序在同一个域中，只需使用 XMLHttpRequest。在下面的例子中，我们使用 jQuery 的 AJAX 函数将数据传输到我们的服务器:

```
function captureError(ex) {
  var errorData = {
    name: ex.name, // e.g. ReferenceError
    message: ex.line, // e.g. x is undefined
    url: document.location.href,
    stack: ex.stack // stacktrace string; remember, different per-browser!
  };

  $.post('/logger/js/', {
    data: errorData
  });
} 
```

请注意，如果您必须跨不同来源传输您的错误，您的报告端点将需要支持跨来源资源共享(CORS)。

## 摘要

如果您已经做到了这一步，那么现在您已经拥有了开发自己的基本错误报告库并将其与您的应用程序集成所需的所有工具:

*   如何工作，它支持什么浏览器
*   如何使用 try/catch 来捕获缺少`window.onerror`的堆栈跟踪
*   将错误数据传输到服务器

当然，如果您不想为所有这些烦心，有很多商业和开源工具可以为您完成客户端报告的所有繁重工作。(Psst:你可能想[试试哨兵](https://sentry.io/signup?trial&plan=medium&period=annual&utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship)去[调试 JavaScript](https://sentry.io/for/javascript/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship) 。)

就是这样！开心[错误监控](https://sentry.io/features/?utm_source=syndication&utm_medium=blog&utm_content=sitepoint&utm_campaign=sponsorship)。

## 分享这篇文章