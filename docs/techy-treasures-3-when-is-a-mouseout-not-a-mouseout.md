# 科技珍宝#3:什么时候鼠标被释放不是鼠标被释放？

> 原文：<https://www.sitepoint.com/techy-treasures-3-when-is-a-mouseout-not-a-mouseout/>

我的工具箱里有这个小工具已经有一段时间了，当我处理**鼠标事件**时，我总是觉得它非常有价值。它评估两个事件目标以确定一个是否包含另一个:

```
function contains(node1, node2)
{
	if(node2 == node1) { return true; }
	if(node2 == null) { return false; }
	else { return contains(node1, node2.parentNode); }
}
```

## 祝福和诅咒

JavaScript 事件模型最漂亮的地方之一是**事件冒泡**——如果触发事件的元素没有捕获到事件，事件<q>就会冒泡</q>到它的父元素。从那里它可能被捕获，或者它可能再次冒泡，并且这继续沿着 <abbr title="Document Object Model">DOM</abbr> 一路向上，直到事件被捕获或者它从顶部冒泡。

然而，尽管这很优雅也很有用，但它确实使鼠标事件变得更加复杂；事件的目标可能不是事件侦听器实际绑定到的元素，或者事件可能看起来在“错误”的时间触发。一个常见的例子是当鼠标从一个容器元素移动到里面的一个元素*—*时，一个 mouseout 事件将在容器元素上触发，即使鼠标实际上并没有离开它*。*

在大多数现代浏览器中，我们可以使用事件的`eventPhase`属性来区分，或者通过在`addEventListener`方法中指定一个捕获标志(将第三个参数设置为`true`)。还有像`originalTarget`和`explicitOriginalTarget`这样的特殊属性，可以给我们所需的信息来区分这些事件。

但这些在 Internet Explorer 中都是不可能的(至少在人们现在实际使用的版本中是不可能的)。

## 找到真正有用的东西

Internet Explorer 有一个内置的评估事件目标的方法，叫做…(你猜对了)… `contains()`。所以让我们做一个这样的东西，它可以在所有的浏览器中工作。这样我们就可以省去一些代码分叉:

```
container.onmouseout = function(e)
{
	var target = e ? e.relatedTarget : event.toElement;

	if(!contains(this, target))
	{
		//Mouse has left the container element
	}
	else
	{
		//Mouse is still inside
	}
};
```

如果我们试图忠实地重新创建 IE 的方法，我们会将我们的方法原型化为`Object`或`HTMLElement`。但是*我们不应该将定制方法原型化为内置对象*，尤其是在其他人将要使用的代码中，因为那些方法会出现在枚举器中，可能会给不期望它们的脚本带来重大问题。

不过有趣的是，如果我们将它用作原型，它只需要一个参数，因为容器对象本身可以作为`this`引用:

```
Object.prototype.contains = function(node)
{
	if(node == this) { return true; }
	if(node == null) { return false; }
	else { return this.contains(node.parentNode); }
}
```

无论如何，我们不会在这里这样做。撇开别的不说，我们想要能在所有浏览器中工作的东西，包括 Internet Explorer(它不支持原生原型；即使这样做，也可能会覆盖本机方法)。

所以我在实践中倾向于使用它的双参数形式，作为我正在处理的任何主要对象的方法；我发现这非常方便，而且肯定不会覆盖任何本机方法:

```
var myObject = {

	...,

	contains: function(node1, node2)
	{
		if(node2 == node1) { return true; }
		if(node2 == null) { return false; }
		else { return this.contains(node1, node2.parentNode); }
	},

	...

};
```

这就是你要做的——一小段掩盖了它的有用性的代码(正如短代码经常做的那样)。事实上，以这样或那样的形式，我可以说这段代码占了我所写的所有脚本的四分之一。为此欢呼吧；还有对[杰森·戴维斯](http://www.jasonkarldavis.com/)的称赞，他在很多年前就有了最初的对象原型的想法——T4 的踢腿和傻笑——帮助我戒掉了对网景 4 的依赖。

## 分享这篇文章