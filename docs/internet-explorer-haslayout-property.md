# Internet Explorer 具有布局属性

> 原文：<https://www.sitepoint.com/internet-explorer-haslayout-property/>

在一个完美的世界里，我们不需要知道任何关于`hasLayout`属性的事情——毕竟，它是 Windows Internet Explorer 渲染引擎的一个内部组件。然而，它的影响是深远的，并对元素的外观和行为产生重大影响，影响元素如何限制其内容并与其邻居反应。

本主题仅与 Windows 的 Internet Explorer 有关。

## 什么是 hasLayout 属性？

在 Internet Explorer 中，一个元素或者负责调整和排列它自己的内容，或者依赖父元素来调整和排列它的内容。

为了适应这两个不同的概念，渲染引擎使用了一个名为`hasLayout`的属性，该属性可以为相关元素提供 true 或 false 值。当`hasLayout`属性的值为 true 时，我们说一个元素获得了一个布局或者拥有了一个布局。

当一个元素有一个布局时，它负责调整自己的大小和定位，可能还有任何后代元素。简而言之，这意味着元素更加关心自己及其内容，而不是依赖祖先元素来完成所有工作。因此，一些元素在默认情况下会有一个布局，尽管大多数元素没有。

默认情况下，负责排列自身内容的元素将有一个布局，包括以下内容(此列表并不详尽):

*   正文和 html(在标准模式下)
*   表，tr，th，td
*   img
*   人力资源
*   输入，按钮，文件，选择，文本区，字段集
*   选取框
*   框架集，框架，iframe
*   对象，小程序，嵌入

微软给出的并非所有元素都有默认布局的主要原因是“性能和简单性”如果默认情况下所有元素都有一个布局，将会对性能和内存使用产生不利影响。

那么，我们中的任何人为什么要关心`hasLayout`财产呢？因为许多 Internet Explorer 显示的不一致可归因于此属性。

在大多数情况下，由缺少布局的元素引起的问题很容易发现:内容经常放错地方或完全丢失。例如，当默认情况下没有布局的元素(如 div)包含浮动或绝对定位的内容时，它通常会表现出奇怪的错误行为。可能出现的奇怪行为的类型是多种多样的，包括丢失或放错位置的内容，或者在移动或滚动窗口时无法完全重绘的元素。

如果您注意到一部分内容出现又消失，并且页面的某些部分只画了一半，这是元素需要布局的良好迹象。当关键元素获得布局时，问题奇迹般地消失了。事实上，您每天遇到的 99%的 Internet Explorer CSS 错误都可以通过在正确的位置使用 hasLayout 来修复。一个`hasLayout`修复只涉及声明一个 CSS 属性，该属性导致一个元素获得一个布局，而在默认情况下它通常没有一个布局。

元素获得布局的最简单方法是应用一个维度 CSS 属性，例如，`width`或`height`。但是，在您不希望对元素应用特定宽度或高度的情况下，有几个其他 CSS 属性，当您将它们应用到元素时，将导致该元素获得布局。

这些其他属性是:

*   显示:内嵌块
*   高度:(除自动以外的任何值)
*   浮动:(左或右)
*   位置:绝对
*   宽度:(除自动以外的任何值)
*   写入模式:tb-rl
*   缩放:(除正常外的任何值)

Internet Explorer 7 有一些导致元素获得布局的附加属性(这不是一个详尽的列表):

*   最小高度:(任意值)
*   最大高度:(除无以外的任何值)
*   最小宽度:(任意值)
*   最大宽度:(除无以外的任何值)
*   溢出:(除可见值外的任何值)
*   overflow-x:(除可见值以外的任何值)
*   溢出-y:(除可见值外的任何值)5
*   位置:固定

声明这些 CSS 属性中的任何一个都会导致元素获得一个布局——当然，假设该属性对相关元素有效。例如，除非文档在[模式](https://reference.sitepoint.com/css/doctypesniffing)下运行，否则我们不能对内嵌元素应用高度。

让所有元素都有一个布局并不是一个好主意——不仅仅是因为已经提到的性能和内存问题，还因为会出现许多其他不必要的 CSS 副作用。例如:

当绝对定位或浮动元素的子元素有布局时，子元素不会收缩以包装其内容。位于浮动旁边的静态内容不会环绕浮动，而是在浮动的旁边形成一个矩形块。
更多不受欢迎的行为的例子记录在 [MSDN 网站](http://msdn2.microsoft.com/en-us/library/bb250481.aspx)上。

## 调试有布局问题

如果您注意到您的网页在 Internet Explorer 中行为异常，请尝试为某个元素设置 CSS 属性以使其获得布局，然后看看问题是否会消失。

识别属性应该应用到的正确元素需要一些技巧。有了经验，识别问题就变得容易了——它通常是一个没有设置明确宽度的父容器，或者其宽度仅由边距定义。如果这个父元素包含浮动元素或绝对元素，它很可能就是导致问题的元素；问题很可能存在，因为它没有很好地照顾它的子元素。

调试布局问题的一个有用方法是将文档中元素的专有 CSS 属性 [zoom](https://reference.sitepoint.com/css/zoom) 设置为 1，一次一个，以便隔离导致问题的元素。如果您在元素上设置了属性，并且问题得到了解决，那么您就知道您的思路是正确的。zoom 属性很有用，因为它不仅是一个触发元素获取布局的属性，而且在大多数情况下，设置它不会以任何其他方式改变页面的外观(除了可能修复您遇到的错误)。排除法可以用来迅速缩小问题的范围。

一旦您找到了导致问题的元素，您就可以应用必要的修复。首选方法是在元素上设置一个或多个维度 CSS 属性。但是，如果不能正常应用尺寸，就必须采用一种变通方法。

对于 Internet Explorer 7，最好的方法是将 min-height 属性设置为 0；这种技术是无害的，因为无论如何 0 是属性的初始值。没有必要对其他浏览器隐藏该属性——这绝对不是我们下一个建议的情况！

在 Internet Explorer 6 和早期版本中，触发元素以获得布局的标准方法是将 height 属性设置为 1%，只要 overflow 属性没有设置为 visible 以外的任何值。这种方法利用了这些浏览器版本中的一个缺陷，即如果 overflow 属性设置为默认值 visible，则无论 height 属性的值是多少，包含框的高度都会扩展以适应其内容。然而，大多数其他浏览器将尊重 1%的高度值，这通常不是您希望他们做的，所以这个声明将需要对所有其他浏览器隐藏。

在前几年，将高度设置为 1%，并对除 Internet Explorer 6 和早期版本之外的所有浏览器隐藏声明的技术被称为 [Holly hack](http://stackoverflow.com/questions/6789635/what-is-holly-hack-in-css-design) 。目前，推荐的只为 Internet Explorer 指定 CSS 声明的方法是使用条件注释。

好消息是，Internet Explorer 7 比以前的版本更强大，许多(不幸的是，虽然不是全部)关于布局的问题已经消失——您需要的修复比以前版本的浏览器少得多。有关布局问题的更多信息，请参见 Satzansatz 网站上的“[关于使用布局](http://www.satzansatz.de/cssd/onhavinglayout.html)”。

## 分享这篇文章