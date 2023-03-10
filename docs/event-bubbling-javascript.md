# JavaScript 中的事件冒泡是什么？事件传播解释

> 原文：<https://www.sitepoint.com/event-bubbling-javascript/>

事件冒泡是您在 JavaScript 旅行中可能会遇到的一个术语。它与当一个元素嵌套在另一个元素中时调用事件处理程序的顺序有关，并且两个元素都为同一个事件注册了侦听器(例如，单击)。

但是事件冒泡只是拼图的一部分。它经常与事件捕获和事件传播一起被提及。对这三个概念的深刻理解对于在 JavaScript 中处理事件至关重要——例如，如果您希望实现[事件委托模式](https://www.sitepoint.com/javascript-event-delegation-is-easier-than-you-think/)。

在这篇文章中，我将解释每一个术语，并演示它们是如何结合在一起的。我还将向您展示对 JavaScript 事件流的基本理解如何让您对应用程序进行细粒度的控制。请注意，这不是关于事件的初级读本，因此假设您熟悉该主题。如果你想了解更多关于一般事件的信息，为什么不看看我们的书: [JavaScript:新手到忍者](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja)。

## 什么是事件传播？

让我们从事件传播开始。这是事件冒泡和事件捕获的总称。以缩略图画廊为例，考虑构建链接图像列表的典型标记:

```
<ul>
    <li><a href="..."><img src="..." alt=""></a>
    <li><a href="..."><img src="..." alt=""></a>
    ...
    <li><a href="..."><img src="..." alt=""></a>
</ul> 
```

点击一个图像不仅会为相应的`IMG`元素生成一个`click`事件，还会为父元素`A`、祖父元素`LI`等等生成一个`click`事件，一路向上直到所有元素的祖先，最后在`window`对象处终止。

在 DOM 术语中，图像是*事件目标*，即点击所源自的最里面的元素。事件目标加上它的祖先，从它的父对象一直到`window`对象，形成了 DOM 树中的一个分支。例如，在图库中，这个分支将由节点组成:`IMG`、`A`、`LI`、`UL`、`BODY`、`HTML`、`document`、`window`。

> 注意，`window`实际上不是一个 DOM 节点，但是它实现了`EventTarget`接口，因此，为了简单起见，我们把它当作文档对象的父节点来处理。

这个分支很重要，因为它是事件传播(或流动)的路径。这个传播是调用给定事件类型的所有侦听器的过程，这些侦听器附加到分支上的节点。每个监听器都将被调用一个`event`对象，该对象收集与事件相关的信息(稍后将详细介绍)。

请记住，对于同一事件类型，可以在一个节点上注册多个侦听器。当传播到达一个这样的节点时，侦听器按照它们注册的顺序被调用。

还应该注意，分支确定是静态的，也就是说，它是在事件的初始分派时建立的。事件处理期间发生的树修改将被忽略。

传播是双向的，从窗口到事件目标，然后返回。这种传播可以分为三个阶段:

1.  从窗口到事件目标父:这是*捕获阶段*
2.  事件目标本身:这是*目标阶段*
3.  从事件目标父窗口返回到*气泡阶段*

这些阶段的区别在于被调用的侦听器的类型。

### 事件捕获阶段

在这个阶段，只有*捕获器*监听器被调用，即那些使用 [addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) 的第三个参数的值`true`注册的监听器:

```
el.addEventListener('click', listener, true) 
```

如果省略此参数，则其默认值为 false，并且侦听器不是捕获器。

因此，在这个阶段，只调用在从窗口到事件目标父对象的路径上找到的捕获器。

### 事件目标阶段

在这个阶段，将调用事件目标上注册的所有侦听器，而不管它们的捕获标志的值。

### 事件冒泡阶段

在事件冒泡阶段，只有非捕获者会被调用。也就是说，只有监听器注册了第三个参数`addEventListener()`的值`false`:

```
el.addEventListener('click', listener, false) // listener doesn't capture
el.addEventListener('click', listener) // listener doesn't capture 
```

请注意，虽然所有事件都流向捕获阶段的事件目标，但是`focus`、`blur`、`load`和其他一些事件不会冒泡。也就是说，它们的行程在*目标*阶段之后停止。

因此，在传播结束时，分支上的每个侦听器只被调用一次。

事件冒泡并不是每种事件都会发生。在传播过程中，侦听器可以通过读取`event`对象的`.bubbles`布尔属性来知道事件是否冒泡。

W3C UIEvents 规范中的三个事件流阶段如下图所示。

![Event bubbling: a graphical representation of an event dispatched in a DOM tree using the DOM event flow](img/91ec2c781a4b6d566f556107898b75e8.png)

[版权所有 2016 环球网联盟，(MIT，ERCIM，Keio，北航)](https://www.w3.org/Consortium/Legal/2015/doc-license)。

## 访问传播信息

我已经提到了`event`对象的`.bubbles`属性。此对象提供了许多其他属性，侦听器可以使用这些属性来访问与传播相关的信息。

*   [e.target](https://developer.mozilla.org/en-US/docs/Web/API/Event/target) 引用事件目标。
*   [e.currentTarget](https://developer.mozilla.org/en-US/docs/Web/API/Event/currentTarget) 是正在运行的侦听器在其上注册的节点。这与侦听器调用上下文的值相同，即由关键字`this`引用的值。
*   我们甚至可以用 [e.eventPhase](https://developer.mozilla.org/en-US/docs/Web/API/Event/eventPhase) 找出当前相位。是一个整数，表示三个`Event`构造器常量`CAPTURING_PHASE`、`BUBBLING_PHASE`和`AT_TARGET`中的一个。

## 付诸实践

让我们看看上述概念的实践。在下面的笔中，有五个嵌套的方盒子，命名为`b0` … `b4`。最初，只有外盒`b0`可见；当鼠标指针停留在它们上面时，内部的图标会显示出来。当我们单击一个框时，右边的表格中会显示一个传播流日志。

通过[码笔](https://codepen.io)上的 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看笔 [jmXdpz](https://codepen.io/SitePoint/pen/jmXdpz/) 。