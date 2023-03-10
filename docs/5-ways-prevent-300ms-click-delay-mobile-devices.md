# 防止移动设备 300 毫秒点击延迟的 5 种方法

> 原文：<https://www.sitepoint.com/5-ways-prevent-300ms-click-delay-mobile-devices/>

大多数基于触摸的移动浏览器在你点击屏幕和浏览器触发相应的事件处理程序之间会等待 300 毫秒。它的实现是因为你可以双击来缩放页面到全幅。因此，浏览器会等待三分之一秒——如果你没有再次点击，那么“点击”就会被激活。

在响应式网页设计和多点触控缩放出现之前，这种延迟是一种明智的预防措施。不幸的是，这是用户认为 web 应用程序比本地应用程序速度慢、功能差的主要原因之一。那么我们能对延迟做些什么呢？

## 1.不要为此烦恼

300 毫秒的延迟是否是一个问题很大程度上取决于你的应用和目标受众。例如，具有标准导航菜单的纯内容网站不太可能受到不利影响，因为用户花在阅读文本上的时间比与控件交互的时间多。

## 2.禁用缩放(Chrome 和 Firefox)

如果使用 HTML `head`中的以下视窗设置禁用缩放，Android 上的 Chrome 和 Firefox 将不会等待 300 毫秒:

```
<meta name="viewport" content="width=device-width, user-scalable=no">
```

或者

```
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=1">
```

然而，这种解决方案在 Safari 上失败了，并为那些有视觉或运动障碍的人提出了几个可访问性问题。幸运的是，供应商已经开始解决这个问题…

## 3.将视口设置为设备宽度(Chrome 32+)

在 Chrome 32 中，将视口宽度设置为小于或等于设备宽度将禁用双击缩放。你可能已经在你的响应网站的 HTML `head`中有了合适的设置，例如

```
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=3">
```

## 4.使用指针事件(IE10+)

微软已经在 [PointerEvents 规范](https://dvcs.w3.org/hg/pointerevents/raw-file/tip/pointerEvents.html)中解决了许多基于触摸的问题。例如，如果用户滚动页面，就不会触发`pointerup`事件。

还有一个非标准的 [CSS touch-action](http://msdn.microsoft.com/en-us/library/windows/apps/Hh767313.aspx) 属性，允许您在不禁用缩放的情况下消除特定元素或整个文档的延迟:

```
a, button, .myelements
{
	-ms-touch-action: manipulation;	/* IE10  */
	touch-action: manipulation;		/* IE11+ */
}
```

我希望其他供应商采用 PointerEvents，但不要抱太大希望。

## 5.实现触摸端事件处理程序

与`click`和`touchstart`不同，`touchend`事件会立即触发，没有 300 毫秒的延迟。如果你正在开发一个基于触摸的 WebGL 或 canvas 游戏，这可能是可行的，但是，你不能仅仅依靠标准网页中的`touchend`,因为:

1.  用户在不同于`touchend`的元素上触发`touchstart`不应该触发点击。
2.  在`touchend`事件滚动之前，用户触发`touchstart`然后触发*相当大的*T1——点击不应该被触发。
3.  对于那些使用非触摸设备的人来说，你仍然需要标准的`click`处理程序——但是要小心不要两次触发同一个事件。

有很多事情要处理，但是我们已经完成了艰苦的工作:

*   FT 实验室的 [**FastClick**](https://github.com/ftlabs/fastclick) 在需要的时候在移动浏览器上附加适当的监听器，也就是说，上面的解决方案 2 到 4 还没有被使用。该脚本压缩到大约 10Kb。
*   [**Tappy！灯丝组的**](https://github.com/filamentgroup/tappy/) 是一个规格化的 tap 事件库，概念上类似于 PointerEvents，压缩到 1Kb。

请注意，向多个元素添加事件处理程序会对性能产生负面影响。这可能比你试图解决的 300 毫秒的延迟问题更糟糕。

## 最优解？

形势一片混乱，开发者要构建一个跨浏览器的解决方案并不容易。我不认为这是我们需要在每个应用程序中解决的问题，因为这是一个特定于浏览器的策略，应该由供应商来解决。

将视口宽度设置为设备宽度是最简单的选择，因为您可能已经这样做了。在 Chrome 32 发布之前，它不会起作用，但 Opera、Firefox 和 IE 可能会紧随其后。我希望能在 Webkit 中看到它，但数百万 iPhone 和 iPad 用户收到它可能需要几年时间。

给 CSS 添加`touch-action: manipulation;`属性没什么坏处——它会优雅地降级。如果 PointerEvents 是由微软之外的任何人设计的，我敢肯定其余的厂商会立即跟进。这是 W3C 规范，也是 T2 的一部分，所以我们可能不需要等太久。

如果你只关心少数几个链接或按钮，你自己定制的`touchend`处理程序可能是有效的。对于其他一切，值得考虑的是[快速点击](https://github.com/ftlabs/fastclick)。

## 分享这篇文章