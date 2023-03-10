# 5 个响应式设计陷阱以及如何避免它们

> 原文：<https://www.sitepoint.com/5-responsive-design-pitfalls-and-how-to-avoid-them/>

想象一下。您刚刚创建了一个响应式杰作，然后在移动设备上检查它。事情会以你意想不到的方式发展。文本乱糟糟的，动画断断续续，你的表单看起来一点也不像你设计的样子。你花了几个小时在网上搜索答案，却被人牵着鼻子走。无奈之下，你把电脑扔出窗外，不小心撞到了邻居家的猫。

好消息是，我在这里保护您免受移动开发陷阱的影响，这些陷阱会导致您将计算机扔出窗外。以下是我们将要涉及的主要陷阱:

1.  意外的字体大小更改
2.  不需要的表单样式
3.  仿真器错误可能会让您认为有问题，而实际上并没有
4.  动画在桌面上看起来不错，但在移动设备上看起来不连贯
5.  触摸坐标未正确记录的事件，即使没有显示错误

*我们开始吧！*

## 本教程的假设

本教程假设你对响应式 web 开发的基础有所了解，但是如果你需要提高速度，我已经写了一个[简要指南](http://simplestepscode.com/responsive-web-development-basics/)。

## 1.意外的字体大小更改

侧向旋转 iOS 设备会改变页面的文本大小，并破坏您的布局。这个问题经常发生在固定位置的元素上，比如导航条和菜单。一旦发生这种情况，唯一的解决方法就是刷新页面。幸运的是，有一种方法可以在不必要的文本大小改变发生之前阻止它们。

将此代码插入到您的 CSS 中:

```
html {
    /* Prevent font scaling in landscape while allowing user zoom */
    -webkit-text-size-adjust: 100%;
}
```

这段代码通过说，<q>嘿，浏览器。如果您要在横向模式下调整文本大小，请将文本调整为其当前大小的 100%，换句话说，相同的大小。</q>

问题解决了。

## 2.不需要的表单样式

平板电脑和移动设备通常带有默认样式，这会弄乱你的表单。要去除表单元素的默认样式，请将以下代码放入 CSS 中:

```
input[type=text], button, select, textarea{
    -webkit-appearance: none;
    -moz-appearance: none;
    border-radius: 0px;
}
```

根据您要重置的表单元素样式的类型，随意混合搭配。例如，如果您想要重置所有类型输入的样式，请将`input[type=text]`更改为`input`。预先警告，这将影响你的复选框和单选按钮，所以请确保你打算这样做。

## 3.仿真器错误会误导

如果你用基于浏览器的移动模拟器测试你的网站，有时模拟器本身会变得混乱。这是最棘手的错误之一，因为即使代码看起来没什么问题，你也会发现问题。

例如，我曾经有一个导航栏，它一直从我正在开发的一个页面的底部消失。在试图找出问题所在后，我发现 Chrome 模拟器的视窗在底部被切断了。揭示问题的是滚动条的一半在页面底部消失了。我解决这个问题的方法是在一个新标签页中打开页面，打开和关闭移动视图，然后刷新。

在这种情况下，首先确保代码没有任何问题是很重要的。我建议隔离问题区域，看看代码是否能自己工作。如果代码有效，那么就在真实的移动设备上尝试一下。如果它在那里工作，那么有可能你的模拟器就是问题的根源。

## 4.桌面上流畅的动画在手机上可能是粗糙的动画

如果您在移动设备上使用动画，请格外注意性能。一般来说，浏览器可以有效地动画显示以下属性:`translate`、`scale`、`rotate`和`opacity`。以下是这种情况的一些例子。

```
transform: translate(15px, 40px); /* shift left 15px and down 40px */
transform: scale(2); /* scale to 2 times original size */
transform: rotate(30deg); /* rotate 30 degrees */
opacity: 0.5; /* set opacity at 0.5 */
```

您想要动画显示这些属性的原因是它们位于浏览器渲染的顶层。如果你把一个网页想象成一张餐桌，制作顶层的动画就像移动一个勺子，而制作底层的动画就像试图移动整个桌布。你可以做到这一点，但这需要更多的工作，因为你要把所有更高的层都拖在一起。

有关高性能动画的更多信息，请参见本文。

为了最大化浏览器对动画的支持，尤其是在 iOS 设备上，请在变换属性中包含前缀`-webkit-`。这里有一个例子:

```
-webkit-transform: rotate(30deg);
transform: rotate(30deg);
```

有关兼容性的更多信息，您可以参考[该图表](http://caniuse.com/#feat=transforms2d)

值得注意的是，框阴影有时会使移动设备上的动画变慢，这取决于它们的使用量以及页面上发生的其他事情。对于方框阴影，我建议进行测试，看看你能得到什么。

## 5.触摸的坐标与点击的坐标的存储方式不同

获取触摸事件的坐标可能会很棘手，因为这些坐标可能会根据设备的不同存储在不同的位置。在某些设备上(比如 iOS)，你可以在和点击坐标相同的地方找到触摸坐标，但在另一些设备上(比如 Android)，你就要去别处找了。好消息是，触摸坐标可以在您实际需要支持的任何移动设备的专用触摸事件数据中找到。

对于触摸事件，使用`e.touches[0].pageX`而不是`e.pageX`来获取该触摸的 x 坐标。用 Y 替换 X 的所有实例以获取 Y 坐标。这里有一些例子，这样您就可以比较这些代码在实践中的样子。

对于鼠标点击，您可以像这样获取坐标:

```
document.onclick = function(e){
    var x = e.pageX; // get x coordinate of click
    var y = e.pageY; // get y coordinate of click
    console.log('x = ' + x + ', y = ' + y); // show coordinates in console
}
```

对于触摸事件，您可以像这样获取坐标:

```
document.ontouchstart = function(e){
    var x = e.touches[0].pageX; // get x coordinate of touch
    var y = e.touches[0].pageY; // get y coordinate of touch
    console.log('x = ' + x + ', y = ' + y); // show coordinates in console
}
```

即使在`e.pageX`和`e.pageY`不工作的设备上，它们仍然可用，但无论如何它们对于触摸事件都将是 0。我提到这一点的原因是，你不会认为检查它们的存在足以确保它们实际工作。

如果您忘记了在哪里找到事件的坐标，您可以使用控制台来查看。我建议使用 Chrome 的移动模拟器，因为它以可读的方式显示 JavaScript 对象。下面是查看`ontouchstart`事件输出何种数据的代码:

```
document.ontouchstart = function(e){
    console.log(e); // show data from ontouchstart event
}
```

如果您使用 jQuery 事件，请注意它们有自己的数据集。您可以看到以下代码的不同之处:

```
$(document).on('touchstart', function(ev){
    console.log(ev); // jQuery version of event data
    console.log(ev.originalEvent); // native JavaScript version of event data
});
```

无论你是检测鼠标点击或触摸的位置，关键点是要确保你是正确地访问你的坐标。*当有疑问时，测试一下*。

## 结论

我们已经讨论了一些重要的移动开发陷阱，包括意外的字体大小变化、不需要的表单样式、误导性的仿真器错误、不连贯的动画和不正确的触摸坐标检测。希望这篇文章中的信息能为你节省大量的时间和痛苦。

如果您有任何问题或需要补充的地方，请随时发表评论。

## 分享这篇文章