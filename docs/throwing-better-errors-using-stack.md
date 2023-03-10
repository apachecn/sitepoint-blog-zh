# 使用堆栈抛出更好的错误

> 原文：<https://www.sitepoint.com/throwing-better-errors-using-stack/>

前阵子，我写过关于[使用`throw`语句调试](https://www.sitepoint.com/debugging-javascript-throw-away-your-alerts/)的文章。不幸的是，这篇文章的标题在某种程度上混淆了这个问题；我从来没有打算建议用`throw`语句代替`alert()`来调试你自己的脚本。我也不提倡用它来向终端用户发送消息。

关键是，当你编写第三方库或<abbr title="Application Programming Error">API——也就是说，一个供开发人员使用而不是供最终用户使用的脚本——作为验证机制的一部分，`throw`语句可能是有用的*。*它可以在 JavaScript 控制台中直接给出开发者的反馈，而不是不断抛出警告。</abbr>

自从写了那篇文章后，我已经多次使用这种技术。但是不断出现的问题是，当您手动抛出一个错误时，错误报告的文件名和行号通常是无用的。

用 error 对象接收的值指向抛出错误的文件和行*；然而，如果你手动抛出一个错误来验证开发人员的输入，你真正想告诉他们的是他们的*代码的*的文件名和行号，他们的错误实际在哪里。*

那么，我们能得到那个信息吗？

的确可以，至少在 Firefox 中可以。Firefox 和 WebKit 浏览器(Safari、Chrome 等)都公开了 error 对象的一个`stack`属性，它是错误的堆栈跟踪。它列出了导致错误的每个单独的语句:从它实际发生的地方，通过任何调用语句，到最高的抽象或事件。

我们可以解析该堆栈(单个字符串值)以获得我们想要的细节；位于堆栈底部的东西通常是开发者输入的地方。这个函数将完成这个任务:

```
function fail(message){   var inputerror = new Error();   inputerror.name = "nMyScript/ValidationError: ";   inputerror.message = message;   if(typeof inputerror.stack != "undefined")   {      var errorstack = inputerror.stack.split(/s*(@|at)s*/);      errorstack = errorstack[errorstack.length - 1]                   .replace(/^s+|s+$/g, '')                   .split(/:([0-9]+)/);      inputerror.fileName = errorstack[0];      inputerror.lineNumber = errorstack[1];   }   return inputerror;}
```

这将被这样使用:

```
throw(fail("Illegal value for foo"));
```

我说我们要找的是栈底的*通常是*，因为那代表了最高层次的抽象。如果开发人员的输入本身不是最高级别的(例如，如果它被抽象成一个函数文字)，那么跟踪的底部将会指向它。

至少在 Firefox 中是这样的！

Opera 已经在它的错误输出中显示了一个堆栈跟踪，所以我们想要的额外信息就在那里。但是 WebKit 浏览器不会在输出中显示文件名或行号，即使它们公开了必要的错误属性，并且上面的代码是有效的。

但是每个人都在 Firefox 中测试，不是吗？不管用来浏览什么！

*缩略图鸣谢:[kagey _ b](http://www.flickr.com/photos/kagey_b/11254536/)*

**note:**Want more?

如果你想阅读更多詹姆斯的作品，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章