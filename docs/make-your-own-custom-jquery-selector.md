# 制作您自己的定制 jQuery 选择器

> 原文：<https://www.sitepoint.com/make-your-own-custom-jquery-selector/>

在所有的 JavaScript 库中，jQuery 可以说具有最接近 CSS 的选择器语法，这使得 CSS 作者可以轻松地钻研，即使有 52 个选择器可供选择。如果你喜欢 CSS 的`:first-child`、`:hover`，或者`:only-child`，菜单上有 34 个类似的“伪”伪类选择器。说到被宠坏的选择！如果你想要更多的 jQuery 原生选择器，比如`:animated`、`:even`、`:not()`和`:visible`，那么社区中有很多自定义选择器(比如`:exactIgnoreCase()`、`:nothidden`或`:loaded()`)可以填补这些空白。所以这一切都意味着您没有必要担心学习编写自定义选择器——对吗？不对！任务越容易完成，完成的机会就越大。这里有一个测试供您尝试:请一位新开发人员使用(更不用说理解)这段代码:

```
if ($(el).offset().top > $(window).scrollTop() - $(el).outerHeight(true) &&    $(el).offset().top < $(window).scrollTop() + $(el).outerHeight(true) + $(window).height()){  //do something}
```

现在请他们试穿这件衣服，看看大小:

```
if ($(el).is(":inview")) {  //do something}
```

这些代码片段都做了同样的事情，而且第二个版本将会有更多的用途，这并不值得奖励。两者都检查元素当前是否在视口中。如果您发现自己有一个冗长的条件来获得一个布尔值，或者您的真/假布尔值一再出现，那么您可能就要为您的选择器创建一个自定义表达式了。制作它们再简单不过了:

```
jQuery.extend(jQuery.expr[':'], { //$.extend($.expr[':'] is just as good  expression: function () {    //establish boolean to be returned, or    return false;  }});
```

*表达式*将在您的选择器中被调用……它可能是`inview`、`loaded`或`nothidden`。这个函数就是你所期望的。我们添加了`return false`，因为这是您希望该函数最少要做的事情。除此之外，使用您需要的任何方法来返回将为您的选择器提供动力的布尔值。让我们先来看看我们的`inview`选择器:

```
jQuery.extend(jQuery.expr[':'], {  inview: function (el) {    if ($(el).offset().top > $(window).scrollTop() - $(el).outerHeight(true) &&        $(el).offset().top < $(window).scrollTop() + $(el).outerHeight(true) + $(window).height()) {      return true;    }    return false;  }});
```

代码还远未优化，但它正在工作。为了测试，我喜欢在 jQuery include 和我的工作脚本块之间的脚本块中添加选择器。但是，对于产品，您可以像插件一样包含它们。继续将它添加到页面中，您可以通过多种方式使用它:

```
$("p:inview").css("color","orange");
```

或者

```
$("p").filter(":inview").each(function(){  //do something});
```

或者

```
if ($("p#desc").is(":inview")) {  //do something}
```

现在我们来看看一些优化。开箱后，我们可以看到一些可以缓存的重复，包括一些重复的对象。因为我们只返回`true`或`false`，我们可以简单地返回我们的条件，然后我们就上路了:

```
jQuery.extend(jQuery.expr[':'], {  inview: function (el) {    var $e = $(el),    $w = $(window),    top = $e.offset().top,    height = $e.outerHeight(true),    windowTop = $w.scrollTop(),    windowScroll = windowTop - height,    windowHeight = windowTop + height + $w.height();    return (top > windowScroll && top < windowHeight);  }});
```

我们到了！我们已经扩展了 jQuery 的表达式引擎，并且我们的代码是优化的和可读的。它不是你在网上唯一能找到的“视图”风格的选择器，但是我们的是一个进化。它将元素的高度考虑在内，因此只要元素的一部分在视口中，它就会一直报告`true`。jquery 的选择器对于定制来说是一个自然的选择，并且在开始使用`.extend()`制作插件之前，它会给你一个很好的机会。

**Note:** Want more?

如果你想读更多 Craig 的作品，可以看看他和 Earle Castledine 合著的《jQuery:新手到忍者 T3》。

## 分享这篇文章