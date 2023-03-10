# 媒体查询:宽度与设备宽度

> 原文：<https://www.sitepoint.com/media-queries-width-vs-device-width/>

我经常看到人们在使用 CSS 媒体查询时，不太理解`width`和`device-width`之间的区别(在此基础上，还有其他例子，如`min-device-width`和`max-device-width`)。这种误解会导致糟糕的代码，并最终增加开发人员的工作量。我在 [SitePoint 论坛](https://community.sitepoint.com)上经常看到这个问题，所以我觉得是时候给出一些进一步的解释了。我将会谈到这一点，并进一步阐述在构建响应型网站时应该使用哪种选项。

## 基本定义

让我们根据“宽度”和“设备宽度”来定义媒体查询的含义。以下引文摘自 [MDN 关于媒体查询的文章](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries)，以下是“宽度”的定义:

> `width`媒体特性描述了输出设备呈现表面的宽度(如文档窗口的宽度，或打印机页面框的宽度)。

下面是 MDN 对“设备宽度”的定义。

> 描述输出设备的宽度(指整个屏幕或页面，而不仅仅是呈现区域，如文档窗口)。

那么这到底意味着什么呢？嗯，基本上，`width`和`device-width`指的是设备的宽度，而不是视窗的宽度，这可能是完全不同的东西。你真正感兴趣的是视窗*的宽度，而不是设备*。

然而，`width`和`device-width`的主要区别在于设备宽度并不总是与所述设备的布局视口相匹配。

许多平板电脑和移动设备并不总是每个 CSS 像素有 1 个设备像素。例如，iPhone 4 的每个 CSS 像素有 2 个设备像素。作为参考，你要知道 iPhone 4 的常规屏幕布局视口是 640×960。这意味着，在这个例子中，iPhone4 的`device-width`是 320×480。这是因为苹果意识到并不是每个网站都是响应性的(为他们感到羞耻)，并试图通过 980 像素的宽度来适应网站的桌面视图来取悦每个人。这意味着，如果没有 meta viewport 标签，iPhone4 将获取您的网站，将其呈现为 980px 宽，压缩为 320px 显示，结果是，将缩小到用户面前。

## 入门指南

首先，就像让所有的网站变得有响应性并遵守媒体查询一样，viewport `meta`标签必须放在你的网页的`<head>`部分。基本的标准版本如下所示。

```
<meta name="viewport" content="width=device-width,initial-scale=1">
```

一旦我们的网页中有了这段代码，如果我们用不同的设备查看页面，不同的媒体查询将能够产生正确的效果。如果没有这个，在特定设备上查看页面基本上只会显示页面的缩小版本；它会试图把你的整个网站塞进它 320 像素宽的显示屏。对你或者你的用户都没有好处！用户不喜欢必须缩放才能看到你的网站内容，他们当然也不想处理由此产生的垂直滚动。

比如说 iPhone 4，它有一个 [980px 宽度的默认视窗](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/UsingtheViewport/UsingtheViewport.html#//apple_ref/doc/uid/TP40006509-SW27)。如果没有`meta`标签，您对媒体查询的尝试将会出错，并且会错误地引用默认的视窗。大多数移动媒体查询的目标是 500 像素左右或以下。这通常通过简单的`max-width`媒体查询来完成。如果没有`meta`标签，你不会看到这个查询生效，因为 iPhone 4 仍然会显示你的网站的 980 像素宽度版本。看看下面的例子:

```
body {
  background: white;    
}

@media screen and (min-width: 980px) /* Desktop */ {
  body {
    background: red;
  }
}

@media screen  and (max-width: 979px) /* Tablet */ {
  body {
    background: blue;
  }
}

@media screen and (max-width: 500px) /* Mobile */ {
  body {
    background: green;
  }
}
```

下面有两个带有截图的演示链接，是用 iPhone 4 拍摄的。两个页面都有上面的 CSS，但是一个使用了 viewport `meta`标签，而另一个没有。请注意，如果您测试演示， ***您需要在小型移动设备上测试才能看到正确的结果*** 。

[**这里是第一个演示(没有 meta 标签)**](http://codepen.io/SitePoint/full/9d2ee535ca7a59095fef5b9bbab7755e/)

![Without viewport meta tag](img/f733c5a58791894428cb008adf41cff8.png)

上面的页面没有`meta`标签，因此，背景是红色的，并且模拟 980 像素宽的默认布局视窗，而不是使用 500 像素的移动媒体查询！

[**下面是第二个演示(带 meta 标签)**](http://codepen.io/SitePoint/full/b537cc8b0572bbef45bbefb77812450b/)

![With viewport meta tag](img/56e96977fed2c604221289ba6846835e.png)

现在 meta 标签已经就位，iOS 正确地显示了我们想要的页面:它基于 320 像素的宽度，允许我们的媒体查询按照我们期望的方式工作。

## 为什么选择`device-width`？

我从一开始就很诚实，让你知道这篇文章是针对*而不是*使用`device-width`。`device-width`确有其位；如果你打算瞄准特定的设备，那么当然可以使用`device-width`。

从 CSS 和 RWD 的角度来看，一个好的网站本质上是“设备无关的”,不会试图迎合每种设备的宽度。针对不同“类型”的设备(例如平板电脑或移动设备)的特定断点是通过关注内容在特定视口宽度下的显示方式，然后根据媒体查询进行适当的更改来实现的。因此，设备不决定断点，布局和内容决定断点，最终这将产生最好的结果；无论是从你的角度还是为了你的用户。

这也可能是为什么我看到人们在构建响应式设计时遇到问题，抱怨这有多难。但是，如果他们试图单独设计设备，他们正在打一场失败的战斗:你必须考虑的设备数量(要彻底)太多了，相当于糟糕的编码。我还应该提到，如果你使用`device-width`，你还必须提供单独的方向规则(纵向与横向):设备宽度不会因为你向侧面旋转设备而改变。这就增加了更多的代码，也更令人头疼！

然而，让我们看看我们的另一个选择。

## “宽度”呢？

我认为，[和许多其他经验丰富的开发人员](http://www.quirksmode.org/blog/archives/2010/09/combining_meta.html)一样，拥有一个在所有可能的屏幕尺寸下都能按预期运行的完全响应的页面是在所有设备上获得最有效布局的最简单和最有益的方式。

要构建一个完全响应和流畅的页面，开发人员只需确保考虑到所有的视窗，并根据需要调整页面的结构和大小，以适应内容和设计。

一个完全响应的网页所需要的是一个流畅的网站，以及一些目标明确的媒体查询，以移动设备、平板设备和桌面+视口的正常范围为目标。我更喜欢使用[基金会的媒体查询](http://foundation.zurb.com/docs/media-queries.html)，我发现它相当准确，包含了所有必要的视窗。

当然，与开发中的任何事情一样，单独使用基于宽度的查询并不能解决所有布局错误。可能仍然有必要在单个移动设备上测试随机错误和渲染怪癖，但就查看不同移动设备的外观而言，这就像拿起浏览器窗口并调整其大小一样简单。

为了测试，我使用 Chrome 扩展[响应式网页设计测试器](https://chrome.google.com/webstore/detail/responsive-web-design-tes/objclahbaimlfnbjdeobicmmlnbhamkg?hl=en)。它让你选择一个特定的设备，你会看到设备的大小和你的网页在该设备上的显示方式。

我想补充的另一个好处是，使用基于宽度的查询可以确保未来的设备仍然可以很好地显示网站。它本质上是面向未来的，因为你的目标是通用的东西——一般的视窗大小——而不是特定于设备的东西。

## 最后

你还打算在媒体查询中使用`device-width`吗？我的论点不够有说服力吗？使用常规的“宽度”和设备的视口简化了响应式设计，最终，我觉得 like 是更好的选择，也是应该专门使用的东西。欢迎在下面的讨论中留下你的评论，让我知道你的想法和你是否同意。

## 分享这篇文章