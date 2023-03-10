# 快速提示:延时后显示模式弹出

> 原文：<https://www.sitepoint.com/show-modal-popup-after-time-delay/>

在下面的快速技巧中，我将向您展示如何在短暂的延迟后打开网页上的模态窗口。这可能有助于强调特定的行动号召，如订阅时事通讯或在脸书上获得赞。一些网站也使用这种技术来显示广告。

![An untimely popup between racket and shuttle cock](img/bf0b9a81f18a752179b9428a3426db95.png)

但是在继续之前，花一点时间问问自己这是不是你真的需要做的事情。每当我浏览的网站在我没有点击任何东西的情况下打开一个模态，我几乎总是会立即关闭它，并对我的注意力被从我正在看的东西上转移开感到恼火。在我看来，这种技术会降低网站的整体体验，有更好的方法让访问者了解你的内容。

## 基本实现

还在读书？好吧，我想你已经决定这么做了，那我们就开始吧。对于不耐烦的人，在文章的最后有一个[工作演示](#demo)。

对于这个快速提示，我将使用 [Colorbox 插件](http://www.jacklmoore.com/colorbox/)来显示模态。Colorbox 依赖于 jQuery，所以您也必须将它添加到您的页面中。这里有一个模板。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset=utf-8 />
    <title>Delayed modal demo</title>
    <link rel="stylesheet" href="https://cdn.rawgit.com/jackmoore/colorbox/master/example1/colorbox.css" />
  </head>
  <body>

    <script src="https://code.jquery.com/jquery-2.2.4.min.js"></script>
    <script src="https://cdn.rawgit.com/jackmoore/colorbox/master/jquery.colorbox-min.js"></script>
    <script> <!-- Code here --> </script>
  </body>
</html> 
```

请注意，我在这里使用了各种 cdn 来包含脚本，但是您也可以使用诸如 npm 或 bower 之类的包管理器来安装依赖项。

### 显示模态

通常，我们会将 Colorbox 分配给 HTML 元素，并将任何设置作为键/值对传入对象:

```
$(selector).colorbox({
  key:value, 
  key:value
}); 
```

然而，我们想直接调用 colorbox(不将它赋给任何东西)，所以语法略有不同:

```
$.colorbox({
  key:value, 
  key:value
}); 
```

Colorbox 有一堆选项(很多与显示图像有关)，允许你自定义模式。在下面的例子中，我指定了它的维度，给了它一个类名(这允许我使用 CSS 对它进行样式化),并向它传递了一个要显示的 HTML 字符串。你可以在上面链接的页面上找到选项的完整列表。

```
$.colorbox({
  html:"<h2>Call For a Free Quote</h2>",
  className: "cta",
  width: 350,
  height: 150
}); 
```

然后我们需要做的就是使用 JavaScript 的`setTimeout`函数在所需的时间过后调用这段代码。`setTimeout()`是一个原生的 JavaScript 函数，在以毫秒为单位的指定延迟后调用一个函数或执行一个代码片段。如果你想了解`setTimeout()`的来龙去脉，那么你可以阅读[这篇 SitePoint 教程](https://www.sitepoint.com/jquery-settimeout-function-examples/)。

```
setTimeout(function(){
  $.colorbox({
    html:"<h2>Call For a Free Quote</h2>",
    className: "cta",
    width: 350,
    height: 150
  });
}, 10000); 
```

当你的访问者浏览网站十秒钟后，弹出窗口就会打开。

## 无障碍问题

有许多关于模态窗口的可访问性问题，例如:键盘用户能与它们交互吗？标记有语义吗？他们容易被解雇吗？你可以在这里找到对这个主题更全面的讨论:[让模态窗口对每个人都更好](https://www.smashingmagazine.com/2014/09/making-modal-windows-better-for-everyone/)。

尽管 Colorbox 自带了很多这些功能，但仍有一些地方我们可以改进。

### 转移焦点

当模式打开时，Colorbox 将焦点转移到窗口本身。这很好，但是如果我们在模态中有任何交互元素(例如一个`<input>`元素),我们可以考虑将焦点设置为这个。这将意味着鼠标用户少点击一次鼠标，使用键盘的人少按键。我们可以使用 JavaScript 的 [focus](https://developer.mozilla.org/en/docs/Web/API/HTMLElement/focus) 方法来做到这一点。

我们还需要利用 Colorbox 触发的`onComplete`事件，以确保我们的内容已经被加载。

```
$.colorbox({
  ...
  onComplete: function(){ $("#myInput").focus(); }
}); 
```

### 记住用户先前在哪里

当用户关闭弹出窗口后，礼貌的做法是将它们返回到页面上之前的位置。为此，我们需要跟踪用户最近交互的元素，并在模式关闭时将焦点重新设置到该元素上。

```
var lastFocus;

setTimeout(function(){
  lastFocus = document.activeElement;

  $.colorbox({
    ...
    onClosed: function(){ lastFocus.focus(); }

  });
}, 2000); 
```

## 每 X 小时显示一次弹出窗口

出于可用性的考虑，让用户每次访问你的站点时都打开模态并不是一个好主意。相反，可以考虑每 X 小时或每 X 天播放一次。

实现这一点的一种方法是，一旦显示了模态，就设置一个 cookie，它会在指定的时间后过期。然后，您可以在页面加载时检查 cookie 是否存在，并采取相应的措施。

为此，我们需要一组处理 cookies 的函数。对于这个任务，我推荐 [js-cookie](https://github.com/js-cookie/js-cookie) 。

将它包含在 Colobox 库之后的页面中:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/js-cookie/2.1.3/js.cookie.min.js"></script> 
```

至此，开始将各种功能转移到它们自己的功能中也是有意义的。

```
var lastFocus;

function onPopupOpen(){
  $("#myInput").focus();
}

function onPopupClose(){
  Cookies.set('colorboxShown', 'yes', { expires: 1 });
  lastFocus.focus();
}

function displayPopup(){
  $.colorbox({
    html:"<h2>Call For a Free Quote</h2>",
    className: "cta",
    width: 350,
    height: 250,
    onComplete: onPopupOpen,
    onClosed: onPopupClose
  });
}

setTimeout(function(){
  var popupShown = Cookies.get('colorboxShown');

  if(popupShown){
    console.log("Cookie found. No action necessary");
  } else {
    lastFocus = document.activeElement;
    displayPopup();
  }
}, 2000); 
```

## 演示

这是 CodePen 的全部工作。运行嵌入，弹出窗口将在三秒钟后打开。它每 24 小时只显示一次，因为它会像上面演示的那样设置一个 cookie。说到这里，我添加了一个 *Clear Cookies* 按钮，这样你就可以多次运行这个演示了。您可以通过点击右下角的*重新运行*按钮来重新运行嵌入。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [gwWpQX](http://codepen.io/SitePoint/pen/gwWpQX/) 。