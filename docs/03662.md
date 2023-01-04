# 用 CSS 媒体查询切芥末

> 原文：<https://www.sitepoint.com/cutting-the-mustard-with-css-media-queries/>

切芥末是英国广播公司的汤姆·马斯伦创造的一个术语。该方法使用 JavaScript 来检查浏览器功能，然后加载更多的 CSS 和 JavaScript 来为用户提供“增强的”体验，否则浏览器将只加载“核心”体验所需的文件。

最近出现了一股削减芥末的兴趣，例如[通过削减芥末](https://www.sitepoint.com/migrating-flexbox-cutting-mustard/)和[服务器端芥末削减](https://css-tricks.com/server-side-mustard-cut/)迁移到 Flexbox，以及[总体上的渐进增强](https://www.sitepoint.com/javascript-dependency-backlash-myth-busting-progressive-enhancement/)。

## 做得更好

然而，根据我的经验，即使是非常基本的“核心”体验也会在一些非常旧的浏览器上引起问题，所以我想基于这个想法，看看是否有可能拒绝真正旧的浏览器的任何 CSS，只给它们留下 HTML。

当然，显而易见的解决方案是使用 JavaScript 有条件地加载*所有*CSS，如果浏览器能满足要求的话，但是这对于我来说感觉太重了；现代的、有能力的、没有加载 JavaScript 的浏览器将会因为没有任何样式而受到惩罚。所以我寻找一个只有 CSS 的方法来解决这个问题，我发现了一个由 Craig Buckler 写的[旧帖子。经过相当多的实验和调整，我想到了这个:](https://www.sitepoint.com/support-old-browsers-responsive-web-design/)

```
<!-- in the <head> -->
<link rel="stylesheet" href="css/your-stylesheet.css"
      media="only screen and (min-resolution: 0.1dpcm)">
<link rel="stylesheet" href="css/your-stylesheet.css" 
      media="only screen and (-webkit-min-device-pixel-ratio:0) 
      and (min-color-index:0)">
```

让我们来看看这里发生了什么。

## 它是如何工作的

第一个`<link>`元素的媒体查询只允许样式表在以下浏览器中加载:

*   IE 9+
*   FF 8+
*   歌剧 12
*   铬 29+
*   安卓 4.4+版本

第二个`<link>`元素的媒体查询只允许样式表加载:

*   铬 29+
*   Opera 16+
*   Safari 6.1 以上版本
*   iOS 7+
*   安卓 4.4+版本

结合使用时，这种技术将不会加载样式表，除非浏览器:

*   IE 9+
*   FF 8+
*   歌剧 12，16 岁以上
*   铬 29+
*   Safari 6.1 以上版本
*   iOS 7+
*   安卓 4.4+版本

注意:不管有多少个`<link>`元素，样式表只加载一次。

通过用逗号分隔声明，可以将媒体查询合并到一个 link 元素中，如下所示:

```
<link rel="stylesheet" href="css/your-stylesheet.css"
      media="only screen and (min-resolution: 0.1dpcm),
      only screen and (-webkit-min-device-pixel-ratio:0)
      and (min-color-index:0)">
```

然而，我个人喜欢将它们分开，因为我发现这样更容易看到发生了什么。

因此，如果你以一种语义和可访问的方式构造你的标记，旧的浏览器应该仍然能够看到你的纯 HTML 格式的无样式内容。

这当然很固执，但我认为任何使用这些浏览器的人都应该能够找到他们需要的东西。很有可能的是，通过给这些浏览器提供为新浏览器设计的 CSS，一些东西会被破坏，这可能意味着一个完全不可用的页面。使用这种方法，他们至少可以洗心革面。更重要的是，*你再也不需要在那些浏览器中测试了*。你和他们玩完了！至少，理论上是这样。

当然，您的支持需求可能会有所不同，但是这项技术的优点是您可以在它的基础上进行构建。例如，如果需要添加对 IE8 的支持，可以使用条件注释只为该浏览器加载相同的样式表:

```
<!--[if IE 8]>
<link rel="stylesheet" href="css/your-stylesheet.css">
<![endif]-->
```

或者，如果您需要支持像素比率大于 1 的旧 WebKit 设备，您可以添加另一个带有目标媒体查询的`<link>`元素，如下所示:

```
<link rel="stylesheet" href="css/your-stylesheet.css"
      media="only screen and (-webkit-min-device-pixel-ratio:1.1)">
```

就其本身而言，这将只加载 Android 2.2+的样式表(我无法测试更早的版本)，但是由于除了和其他`<link>`元素之外，它还包括了*，这实际上只是增加了对其他浏览器组的支持。*

您也可以改变 main `<link>`元素的媒体查询，以获得您需要的定制支持。然而，它花费了相当多的测试来得到这个权利，所以要小心！

## “但是，他们是黑客！”

是的，我想是的，尽管这取决于你的定义。媒体查询旨在应用 CSS 之前测试浏览器的功能，这正是我们在这种情况下想要做的，尽管是间接的。

尽管如此，不管是不是黑客，我对这种技术都很满意，到目前为止，它在我所做的所有测试中都运行良好，我计划很快在一个生产站点上使用它。

## 它不起作用的地方

在我迄今为止的所有测试中，我只发现了一种情况，事情没有像预期的那样工作。在 Android 4.4 上，UC 浏览器不响应媒体查询。据我所知，UC 浏览器使用的是旧版本的 WebKit，这可以解释一些事情。

如果你想支持这个浏览器，可以借助一个小的用户代理嗅探来强制加载 CSS:

```
if (navigator.userAgent.indexOf('UCBrowser') > -1) {
  var link  = document.createElement('link');
  link.rel  = 'stylesheet';
  link.href = 'css/your-stylesheet.css';
  document.getElementsByTagName('head')[0].appendChild(link);
}
```

另外，据我所知，没有办法在 Android UC 浏览器中禁用 JavaScript，所以样式表应该总是被加载，除非网络故障之类的。

当然，如果你发现你需要支持其他特定的浏览器，你总是可以添加用户代理嗅探条件，但是我提醒你**谨慎使用**，因为它违背了这是一个 *CSS 专用*技术的目的。

我制作了一个测试页面，在不同的浏览器上测试基本方法。如果您发现任何浏览器的工作不尽如人意，或者如果您发现任何其他媒体查询可以增加对特定浏览器或浏览器范围的支持，请在评论中告诉我，或者在[GitHub 库](https://github.com/Fall-Back/CSS-Mustard-Cut)上提出问题。

## 加载脚本呢？

好的，如果你使用 CSS 来检测浏览器支持，很有可能你想在你支持的浏览器中只加载或运行部分或全部脚本*。那么我们该怎么做呢？*

 *有几种方法可以实现这一点，但我发现的最简单的方法是这样的:

*   在样式表中，在`body`元素上插入一个无害的非默认 CSS 属性。
*   在`body`元素上使用 JavaScript 和`getComputedStyle`来确定您的属性是否已设置。
*   如果是，运行或加载您的其他 JavaScript。

例如，`clear`属性的默认值为`none`。在`body`上将它设置为`both`不太可能对页面的显示有任何影响(如果有影响，那么您必须使用不同的属性)。因此，样式表中的代码如下所示:

```
body {
  clear: both;
}
```

在您的页面上(或在脚本文件中):

```
var is_supported = false
  , val = '';
if (window.getComputedStyle) {
  val = window.getComputedStyle(document.body, null).getPropertyValue('clear');
} else if (document.body.currentStyle) {
  val = document.body.currentStyle.clear;
}

if (val == 'both') {
  is_supported = true;
}
```

## 最终代码

不管是不是黑客，我在这里试图展示的是一种可重用的方法来检测相对现代的浏览器，并防止旧浏览器应用样式或运行脚本，导致它们只显示页面的 HTML。所需的代码非常少，它应该允许您集中精力使用更现代的技术为更现代的浏览器构建精彩的东西，而不用担心。

虽然您可能不需要我在这里介绍的所有内容，但是将所有内容放在一起，我们会得到以下结果:

```
<head>
  <link rel="stylesheet" href="mq-test.css"
        media="only screen and (min-resolution: 0.1dpcm)">
  <link rel="stylesheet" href="mq-test.css"
        media="only screen and (-webkit-min-device-pixel-ratio:0)
        and (min-color-index:0)">
  <link rel="stylesheet" href="mq-test.css"
        media="only screen and (-webkit-min-device-pixel-ratio:1.1)">

  <!--[if IE 8]>
  <link rel="stylesheet" href="mq-test.css">
  <![endif]-->

  <script> if (navigator.userAgent.indexOf('UCBrowser') > -1) {
      var link  = document.createElement('link');
      link.rel  = 'stylesheet';
      link.href = 'mq-test.css';
      document.getElementsByTagName('head')[0].appendChild(link);
    } </script>
</head>
<body>
<!-- content here... -->
  <script> var is_supported = false
      , val = '';
    if (window.getComputedStyle) {
      val = window.getComputedStyle(document.body, null).getPropertyValue('clear');
    } else if (document.body.currentStyle) {
      val = document.body.currentStyle.clear;
    }

    if (val == 'both') {
      is_supported = true;
    }

    if (is_supported) {
      // Load or run JavaScript for supported browsers here.
    } </script>
</body>
```

我用所有的额外功能制作了另一个测试页面。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。)。

## 信用

如果没有使用 [BrowserStack](https://www.browserstack.com) 、[我能使用](http://caniuse.com)和 [Browserhacks](http://browserhacks.com/) 和 [Jeff Clayton](https://github.com/jeffclayton/css_hack_testing) 的工作人员，我不可能做到这一点，所以感谢所有参与的人，如果您有任何想法或反馈，请告诉我。

## 分享这篇文章*