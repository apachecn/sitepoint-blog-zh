# 当 JavaScript 功能检测失败时

> 原文：<https://www.sitepoint.com/javascript-feature-detection-fails/>

曾几何时，浏览器检测是 JavaScript 程序员的惯用伎俩。如果我们知道某些东西可以在 IE5 中工作，但不能在 Netscape 4 中工作，我们会测试那个浏览器并相应地派生代码。大概是这样的:

```
if(navigator.userAgent.indexOf('MSIE 5') != -1)
{
  //we think this browser is IE5
}
```

但是当我第一次加入这个行业的时候，军备竞赛已经开始了！厂商们正在给用户代理字符串添加额外的值，所以他们看起来是他们竞争对手的浏览器，也是他们自己的浏览器。例如，这是用于 Mac 的 Safari 5:

```
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/534.59.10 (KHTML, like Gecko) Version/5.1.9 Safari/534.59.10

```

这将匹配对 <q>Safari</q> 和 <q>Webkit</q> 以及<q>KHTML</q>(Webkit 基于的 Konqueror 代码库)的测试；但也配 <q>Gecko</q> (也就是火狐的渲染引擎)，当然还有 <q>Mozilla</q> (因为几乎每个浏览器都自称 Mozilla，[因为历史原因](http://webaim.org/blog/user-agent-string-history/ "History of the browser user-agent string (webaim.org)"))。

添加所有这些值的目的是**避开浏览器检测**。如果一个脚本假设只有 Firefox 可以处理一个特定的功能，它可能会将 Safari 排除在外，即使它可能会工作。别忘了，用户自己也可以改变他们的用户代理——我曾经将我的浏览器设置为 <q>Googlebot/1.0</q> ，这样我就可以访问网站所有者认为只能抓取的内容了！

因此，随着时间的推移，这种浏览器检测已经成为一种不可能的纠结，并且在很大程度上已经不再使用，被更好的东西取代——特征检测。

特征检测简单地测试我们想要使用的特征。比如我们需要`getBoundingClientRect`(获取一个元素相对于视口的位置)，那么重要的是*浏览器是否支持*，而不是那个是什么浏览器；因此，我们测试的不是受支持的浏览器，而是特性本身:

```
if(typeof document.documentElement.getBoundingClientRect != "undefined")
{
  //the browser supports this function
}
```

不支持该功能的浏览器将返回一种类型的`"undefined"`，因此不会通过该条件。无需我们在任何特定的浏览器中测试脚本，我们就知道它要么正确工作，要么无声地失败。

## 还是我们……？

但是事情是这样的——**特征检测也不是完全可靠的**——它也有失败的时候。所以现在让我们来看一些例子，看看我们能做些什么来解决每个案例。

### ActiveX 对象

也许最著名的特征检测失败的例子是测试`ActiveXObject`在 Internet Explorer 中发出 [Ajax 请求](https://www.sitepoint.com/the-best-way-to-instantiate-ajax/ "The Best Way to Instantiate Ajax? (sitepoint.com)")。

ActiveX 是一个[后期绑定](http://en.wikipedia.org/wiki/Late_binding "Late binding (wikipedia.org)")对象的例子，它的实际意义是你*无法知道*它是否会被支持*，直到你尝试使用它*。因此，如果用户禁用了 ActiveX，像这样的代码将抛出一个错误:

```
if(typeof window.ActiveXObject != "undefined")
{
  var request = new ActiveXObject("Microsoft.XMLHTTP");
}
```

为了解决这个问题，我们需要使用**异常处理** — *尝试*实例化对象，*捕捉*任何失败，并相应地处理它:

```
if(typeof window.ActiveXObject != "undefined")
{
  try
  {
    var request = new ActiveXObject("Microsoft.XMLHTTP");
  }
  catch(ex)
  {
    request = null;
  }
  if(request !== null)
  {
    //... we have a request object
  }
}
```

### 映射到 DOM 属性的 HTML 属性

属性映射通常用于测试对带有 <abbr title="HyperText Markup Language Version 5">HTML5</abbr> 属性的 <abbr title="Application Programming Interface">API</abbr> 的支持。例如，通过查找`draggable`属性，检查带有`[draggable="true"]`的元素是否支持[拖放 API](http://www.html5rocks.com/en/tutorials/dnd/basics/ "Native HTML5 Drag and Drop (html5rocks.com)") :

```
if("draggable" in element)
{
  //the browser supports drag and drop
}
```

这里的问题是 <abbr title="Internet Explorer 8">IE8</abbr> 或者更早的版本会自动将*所有的* <abbr title="HyperText Markup Language">HTML</abbr> 属性映射到 <abbr title="Document Object Model">DOM</abbr> 属性。这也是为什么 [`getAttribute`在这些老版本里这么乱](https://reference.sitepoint.com/javascript/Element/getAttribute#compatibilitysection "getAttribute (reference.sitepoint.com)")的原因，因为它根本不返回一个属性，而是返回一个 <abbr title="Document Object Model">DOM</abbr> 属性。

这意味着如果我们使用一个已经有属性的元素:

```
<div draggable="true"> ... </div>
```

然后可拖动测试将在 <abbr title="Internet Explorer 8">IE8</abbr> 或更早的时候返回 true，即使他们不支持它。

该属性可以是任何内容:

```
<div nonsense="true"> ... </div>
```

但结果将是相同的——IE8 或更早的版本将为 T0 返回 true。

这种情况下的解决方案是用一个没有属性的元素进行**测试，最安全的方法是使用一个创建的元素:**

```
if("draggable" in document.createElement("div"))
{
  //the browser really supports drag and drop
}
```

### 关于用户行为的假设

您可能见过这样的代码，用于检测触摸设备:

```
if("ontouchstart" in window)
{
  //this is a touch device
}
```

大多数触摸设备在触发`click`事件之前都会人为延迟一段时间(通常大约 300 <abbr title="milliseconds">毫秒</abbr>，这样一来，即使不点击元素也可以双击它们。但是这可能会使应用程序感觉迟钝和反应迟钝，因此开发人员有时会使用该特性测试来派生事件:

```
if("ontouchstart" in window)
{
  element.addEventListener("touchstart", doSomething);
}
else
{
  element.addEventListener("click", doSomething);
}
```

然而，这种情况源于*错误的假设*——因为设备支持触摸，所以将使用触摸。但是触屏笔记本电脑呢？用户可能正在触摸屏幕，也可能正在使用鼠标或触控板；上面的代码不能处理这个问题，所以用鼠标点击什么也做不了。

这种情况下的解决方案是**根本不测试事件支持**——相反，一次绑定*两个*事件，然后使用`preventDefault`来阻止触摸产生点击:

```
element.addEventListener("touchstart", function(e)
{
  doSomething();

  e.preventDefault();

}, false);

element.addEventListener("click", function()
{
  doSomething();

}, false);
```

### 那些简单的东西不起作用

承认这一点很痛苦，但有时这并不是我们需要测试的特性——*而是浏览器*——因为一个特定的浏览器声称支持一些不工作的东西。最近的一个例子是 Opera 12 中的`setDragImage()`(这是一个拖放 [`dataTransfer`对象](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer "Data Transfer — Web API interfaces (developer.mozilla.org)")的方法)。

这里功能测试失败，因为 Opera 12 声称支持；异常处理也无济于事，因为它不会抛出任何错误。这显然行不通:

```
//Opera 12 passes this condition, but the function does nothing
if("setDragImage" in e.dataTransfer)
{
  e.dataTransfer.setDragImage("ghost.png", -10, -10);
}
```

现在，如果您想要的只是*尝试*添加一个自定义拖动图像，并且如果不支持的话，很乐意保留默认设置(这就是将要发生的)。但是，如果你的应用程序真的*需要*一个自定义图像，以至于不支持它的浏览器应该得到一个完全不同的实现(即使用自定义 JavaScript 来实现所有的拖动行为)，该怎么办呢？

或者，如果浏览器实现了某些东西，但是有无法阻止的渲染错误，该怎么办？有时，我们别无选择，只能**明确地检测出有问题的浏览器**，并阻止它使用它原本试图支持的功能。

所以问题变成了——实现浏览器检测最安全的方式是什么？

我有两个建议:

1.  优先使用**专有对象测试**而不是`navigator`信息。
2.  用它来**排除浏览器**而不是包含它们。

例如，Opera 12 或更早的版本可以用`window.opera`对象来检测，所以我们可以用那个排除来测试可拖动的支持:

```
if(!window.opera && ("draggable" in document.createElement("div")))
{
  //the browser supports drag and drop but is not Opera 12
}
```

最好使用专有对象而不是标准对象，因为测试结果不太可能在新浏览器发布时改变。以下是我最喜欢的一些例子:

```
if(window.opera)
{
  //Opera 12 or earlier, but not Opera 15 or later
}
if(document.uniqueID)
{
  //any version of Internet Explorer
}
if(window.InstallTrigger)
{
  //any version of Firefox
}
```

对象测试也可以*与功能测试*相结合，在特定浏览器中建立对特定功能的支持，或者在必要时，定义更精确的浏览器条件:

```
if(document.uniqueID && window.JSON)
{
  //IE with JSON (which is IE8 or later)
}
if(document.uniqueID && !window.Intl)
{
  //IE without the Internationalization API (which is IE10 or earlier)
}
```

我们已经注意到`userAgent`字符串是一团不可靠的乱麻，但是`vendor`字符串实际上是可以预测的，并且可以用于可靠地测试 Chrome 或 Safari:

```
if(navigator.vendor == 'Google Inc.')
{
  //any version of Chrome
}
if(navigator.vendor == 'Apple Computer, Inc.')
{
  //any version of Safari (including iOS builds)
}
```

所有这些的黄金法则是极其小心。确保你在尽可能多的浏览器中测试条件，并从*向前兼容性*的角度仔细考虑它们——目标是使用*的浏览器条件，因为一个已知的错误排除*浏览器，而不是因为一个已知的特性包括它们(这就是特性测试的目的)

从根本上来说，总是从**假设完全符合特性测试**开始——假设一个特性将按预期工作，除非你知道其他情况。

## 选择测试语法

在我们开始之前，我想检查一下我们可以用于对象和特性测试的不同种类的语法。例如，以下语法近年来变得很常见:

```
if("foo" in bar)
{
}
```

我们在过去不能使用它，因为 IE5 和它的同时代人在语法上犯了一个错误；但是现在这不再是一个问题，因为我们不需要支持这些浏览器。

本质上，它与此完全相同，但写起来更短:

```
if(typeof bar.foo != "undefined")
{
}
```

然而，测试条件通常依赖于自动类型转换:

```
if(foo.bar)
{
}
```

我们之前在一些浏览器对象测试中使用了这种语法(比如对`window.opera`的测试),这是安全的，因为对象是如何求值的——任何已定义的对象或函数将总是求值为`true`,而如果它未定义，它将求值为`false`。

但是我们可能正在测试有效返回`null`或空字符串的东西，这两个值都等于`false`。例如，`style.maxWidth`属性有时被用来排除 <abbr title="Internet Explorer 6">IE6</abbr> :

```
if(typeof document.documentElement.style.maxWidth != "undefined")
{
}
```

如果支持*并且*有一个作者定义的值，那么`maxWidth`属性只能计算为`true`，所以如果我们像这样编写测试，它可能会失败:

```
if(document.documentElement.style.maxWidth)
{
}
```

一般规则是这样的:依靠自动类型转换对于对象和函数来说是安全的，但是对于字符串和数字或者可能为空的值来说不一定是安全的。

话虽如此——如果你能安全地使用它，那么就这样做吧，因为它在现代浏览器中通常要快得多(大概是因为它们正是为那种情况而优化的)。

关于这个的更多信息，请看:[现实世界中的自动类型转换](https://www.sitepoint.com/automatic-type-conversion/)。

## 分享这篇文章