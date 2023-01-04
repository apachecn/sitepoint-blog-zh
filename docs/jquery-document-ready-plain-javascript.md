# 用 JavaScript 替换 jQuery 文档就绪函数

> 原文：<https://www.sitepoint.com/jquery-document-ready-plain-javascript/>

**jQuery document ready(`$(document).ready()`)方法被实现来在 DOM 完全加载时执行代码。因为它在所有 DOM 元素都可用时执行给定的函数，所以您可以确信尝试访问或操作元素是可行的。**

在 jQuery 3.0 之前，匿名函数的典型用法如下:

```
$(document).ready(function() {
  // Handler for .ready() called.
}); 
```

## jQuery 3.0 ready()的变化

在版本 3 发布之前，有几种方法可以调用`ready`方法:

*   在文档元素上:`$(document).ready(handler);`
*   在空元素上:`$().ready(handler);`
*   或者直接(即不在特定元素上):`$(handler);`

所有以上命名的变体在功能上是等同的。当 DOM 完全加载时，指定的处理程序将被调用，不管它是在哪个元素上被调用的。换句话说，在 image 元素`$("img")`而不是 document 元素上调用它并不表示在加载指定元素时触发了回调。相反，它将在整个 DOM 完全加载时被调用。

在 jQuery 3.0 中，除了`$(handler);`之外的所有其他语法方法都被弃用。官方的理由是:

> 这是因为选择与`.ready()`方法的行为没有关系，这是低效的，会导致对方法行为的错误假设。

## 就绪和加载事件之间的差异

当 DOM 完全加载并且对元素的访问安全时，触发`ready`事件。另一方面，`load`事件是在 DOM 和所有资产加载完毕后触发的。

load 事件可以按如下方式使用:

```
$(window).on("load", function(){
  // Handler when all assets (including images) are loaded
}); 
```

这不仅要等待 DOM 为交互做好准备，还要等待图像完全加载(这可能需要一些时间，取决于图像的大小)。

对于普通的 DOM 操作，您可能不需要`load`事件，但是如果您想要显示一个加载微调器直到所有资源都被加载，或者如果您想要对图像大小进行一些计算，那么它可能是正确的选择。

## 您可能不需要 jQuery.ready()

`ready`方法确保只有当所有 DOM 元素都可以安全操作时才执行代码。但这意味着什么呢？当您在 HTML 文档的`<head>`部分中执行 JavaScript 代码时，这将确保当浏览器已经加载了所有下列元素(例如`<body>`元素)时代码也被执行:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>.ready() tutorial</title>
    <script src="https://cdn.jsdelivr.net/jquery/latest/jquery.min.js"></script>
    <script> $(function(){ // .ready() callback, is only executed when the DOM is fully loaded
        var length = $("p").length;
        // The following will log 1 to the console, as the paragraph exists.
        // This is the evidence that this method is only called when the
        // DOM is fully loaded
        console.log(length);
      }); </script>
  </head>
  <body>
    <p>I'm the content of this website</p>
  </body>
</html> 
```

如果您将 JavaScript 作为`<body>`中的最后一项来执行，那么您可能不需要将它包装在`ready()`中，因为您可能试图操作或访问的所有元素都已经被加载了:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>.ready() tutorial</title>
  </head>
  <body>
    <p>I'm the content of this website</p>
    <script src="https://cdn.jsdelivr.net/jquery/latest/jquery.min.js"></script>
    <script> var length = $("p").length;
      // The following will log 1 to the console, as the paragraph exists.
      console.log(length); </script>
  </body>
</html> 
```

## 普通 JavaScript ready()替代

对于现代浏览器和 IE9+，您可以监听 [`DOMContentLoaded`](https://developer.mozilla.org/en/docs/Web/Events/DOMContentLoaded) 事件:

```
document.addEventListener("DOMContentLoaded", function(){
  // Handler when the DOM is fully loaded
}); 
```

但是，请注意，如果事件已经触发，则不会执行回调。为了确保回调总是运行，jQuery 检查文档的`readyState`([引用](https://github.com/jquery/jquery/blob/ad6a94c3f1747829082b85fd53ee2efbae879707/src/core/ready.js#L80-L93))，如果已经是`complete`，则立即执行回调:

```
var callback = function(){
  // Handler when the DOM is fully loaded
};

if (
    document.readyState === "complete" ||
    (document.readyState !== "loading" && !document.documentElement.doScroll)
) {
  callback();
} else {
  document.addEventListener("DOMContentLoaded", callback);
} 
```

您还可以包括已经实现了这个解决方案的 [domReady](https://github.com/ded/domready) 库。

### 旧版本的 Internet Explorer

对于低于或等于 8 的 IE 版本，您可以使用`onreadystatechange`事件来检测文档的`readyState`:

```
document.attachEvent("onreadystatechange", function(){
  // check if the DOM is fully loaded
  if(document.readyState === "complete"){
    // remove the listener, to make sure it isn't fired in future
    document.detachEvent("onreadystatechange", arguments.callee);
    // The actual handler...
  }
}); 
```

或者，您可以像 jQuery 一样使用 load 事件，因为这可以在任何浏览器中工作。这也会导致时间延迟，因为它会等待所有的资产都被加载。请注意，在这个解决方案中，您还必须检查`readyState`，就像上面解释的那样，以确保即使事件已经被触发，回调仍然会被执行。

## 结论

如果您正在为`ready`方法寻找一个普通的 JavaScript 替代方法，您可以继续使用`DOMContentLoaded`事件。如果你的系统需求包括 IE < 9，你可以使用`onreadystatechange`事件。

如果您在项目中使用 jQuery，您可以安全地继续使用 jQuery 文档就绪函数，但是要记住避免对元素(例如`$(document).ready()`)使用`ready()`方法，如前所述。

最后，不要忘记，在许多情况下，您可能不需要这些解决方案中的任何一个——只需将您的 JavaScript 移动到结束的`</body>`标记之前，您就可以确保 DOM 已经完成加载了！

## 分享这篇文章