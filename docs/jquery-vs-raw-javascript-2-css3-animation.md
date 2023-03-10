# jQuery 方法的本地 JavaScript 等价物:CSS 和动画

> 原文：<https://www.sitepoint.com/jquery-vs-raw-javascript-2-css3-animation/>

[*真的需要 jQuery 吗？*](/do-you-really-need-jquery/) 有时候——特别是如果你想使用 jQuery 1.x 支持 IE6/7/8 的话，然而，现代浏览器 API 现在提供了很多我们在 jQuery 中认为理所当然的功能。在这篇文章中，我们将看看与 CSS 相关的方法。

## 类操作

最常见的 jQuery 任务之一是将类应用于特定的元素:

```
$("#myelement").addClass("myclass");
```

我们可以在原生 JavaScript 中实现同样的事情:

```
document.getElementById("myelement").className = "myclass";
```

这还不是故事的全部:

1.  jQuery 可以将该类应用于任意数量的节点。
2.  jQuery 将新类附加到现有的类定义中，但是本地示例将覆盖它们。

在原生 JavaScript 中，`className`属性只是一个字符串。因此，我们需要一个函数来复制 jQuery 的工作方式，例如

```
function addClass(node, class) {
	if (!node.length) node = [node];
	for (var n = 0, m = node.length; n < m; n++) {
		if ((" " + node[n].className + " ").indexOf(" "+class+" ") >= 0) {
			node.className += " " + class;
		}
	}
}
// apply myclass to all nodes
addClass(document.getElementById("myelement"), "myclass"); 
```

虽然这段代码比 jQuery 更小更快，但是我们复制了库中已经可用的内容——这没有什么意义。

幸运的是，现代浏览器现在提供了一个新的 classList 属性，它实现了 domtokelist——一个应用于节点的所有类的类似数组的集合。以下是可用的属性:

*   **长度** —应用的类名的数量
*   **项(*索引* )** —特定索引处的类名
*   **包含(*类* )** —如果节点应用了该类，则返回 true
*   **添加(*类* )** —向节点应用新的类
*   **删除(*类* )** —从节点中删除类
*   **切换(*类别* )** —删除或添加类别，如果该类别已应用或未应用

我们可以优先使用这个属性，而不是 clunkier className 属性:

```
document.getElementById("myelement").classList.add("myclass");
```

除了 IE9，大多数浏览器都支持 classList。幸运的是，[有几个垫片可用](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-Browser-Polyfills#classlist)，它们可以有条件地加载到浏览器中。

## 风格处理

jQuery 提供了许多应用特定样式的方法，例如

```
$("#myelement").css({
	color: "#c00",
	backgroundColor: "#eee",
	width: "200px",
	height: "100px",
	borderColor: "#f00"
});
```

本机等效项:

```
var m = document.getElementById("myelement"), c = m.style;
c.color = "#c00";
c.backgroundColor = "#eee";
c.width = "200px";
c.height = "100px";
c.borderColor = "#f00";
```

使用缓存的选择器进行 10，000 多次迭代，jQuery 代码在 6，670 毫秒内执行。原生 JavaScript 用了 330 毫秒——快了 20 倍。

当然，除非需要以某种方式计算某个值，否则不应该使用这两种方法。更有效的方法是在 CSS 中定义一个样式类，然后将其名称应用到元素中。

## 动画

jQuery 提供了各种现成的动画效果，包括滑动和淡入淡出。原生 JavaScript 可以更快，但这都不重要: *CSS3 动画击败了两者*。

我最初对 CSS3 动画持怀疑态度。它永远无法提供细粒度的控制(比如在 N 帧后停止动画)，并且侵犯了 JavaScript 的行为责任。然而，利大于弊:

1.  CSS3 动画由浏览器处理；它总是比 JavaScript 执行速度快。
2.  CSS3 动画更容易使用，需要的代码也更少。
3.  CSS3 提供了 3D 转换等效果，这在单独的 JavaScript 中是不切实际的，甚至是不可能的。

IE9 和更低版本不会显示效果，但它们可以优雅地降级，IE10 应该在几个月内成为主导版本。

CSS3 精灵再也不会回到灯里了。如果您仍然在现代浏览器中使用 jQuery 或 JavaScript 制作 DOM 动画，那么您可能是在浪费时间。

也就是说，当 CSS3 动画开始、停止或进行下一次迭代时，JavaScript 可以相应地使用`animationstart`、`animationend`和`animationiteration`来做出反应。更多信息请参考[如何在 JavaScript](/css3-animation-javascript-event-handlers/) 中捕获 CSS3 动画事件。

在我的下一篇文章中，我们将通过查看事件、Ajax 和实用函数来结束这个系列。

## 分享这篇文章