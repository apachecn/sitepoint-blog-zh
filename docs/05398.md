# jQuery animate()方法指南

> 原文：<https://www.sitepoint.com/guide-jquery-animate-method/>

jQuery 是一个可爱的库，多年来它已经改变了成千上万开发人员处理项目的方式。当 jQuery 被创建时，CSS 不能创建复杂的动画，JavaScript 是唯一的出路。jQuery 对动画的帮助很大，这要感谢为此目的而创建的几个方法。尽管它附带了一些简单的动画(`fadeIn()`、`hide()`、`slideDown()`等等)，为了保持它的低重量，该库提供了一个非常灵活的方法，称为`animate()`，它允许我们创建任何我们想要的动画。这种方法就是本文的主题。

jQuery 的`animate()`是一个*包装器方法*，这意味着它对一组先前选择的 DOM 元素进行操作，这些元素由 jQuery 包装。此方法允许您将自己的自定义动画效果应用于集合中的元素。为此，我们必须提供一组 CSS 样式属性和值，这些属性将在动画结束时到达。样式在效果期间达到的中间值(由动画引擎自动处理)由效果的持续时间和缓动功能决定，这两个选项我们将很快讨论。

可以动画显示的 CSS 样式属性列表仅限于那些接受数值的属性。该值可以是绝对值(如 200)，也可以是起点的相对值。对于绝对值，jQuery 假设像素为默认单位。我们还可以指定其他单位，如 em、rem 或百分比。要指定相对值，我们必须在它前面加上+=或-=来分别表示正方向或负方向的相对目标值。

现在我们对`animate()`有了一些了解，是时候看看它的签名和参数了。

## 签名和参数

这个方法有两种主要形式，它的大多数参数都是可选的(用通常的方括号表示):

*   `animate(properties[, duration][, easing][, callback])`
*   `animate(properties[, options])`

关于参数也有很多要说的:

*   `properties` (Object):一个 hash，包含动画结束时应该达到的值。
*   `duration` (Number|String):以毫秒为单位的效果持续时间或预定义字符串之一:“慢速”(600ms)、“正常”(400ms)或“快速”(200ms)。默认为“正常”。
*   `easing`(字符串):执行过渡时使用的缓动函数名。默认值为“swing”。
*   `callback` (Function):为每个动画元素完成动画时执行的函数。
*   `options` (Object):包含一组要传递给方法的选项的 hash。可用的选项如下:
    *   `always` (Function):动画完成或未完成即停止时调用的函数。
    *   `complete` (Function):动画完成时执行的功能。
    *   `done` (Function):动画完成时调用的函数。
    *   `duration`(字符串|数字):同上。
    *   `easing`(字符串):同前所述。
    *   `fail`(函数):动画失败时执行的函数。
    *   `progress` (Function):动画每一步后运行的函数。每个动画元素只调用一次该函数。
    *   `queue` (Boolean):如果动画必须放在效果队列中(稍后会有更多相关内容)。默认值为`true`。
    *   `specialEasing` (Object):一个或多个 CSS 属性的散列，其值是缓动函数。
    *   `start` (Function):动画开始时执行的功能。
    *   `step` (Function):为每个动画元素的每个动画属性调用的函数。

术语*缓动*用于描述处理动画帧的方式和速度。`queue`选项允许我们在设置为`true`时按顺序运行动画，或者在设置为`false`时并行运行动画。我们手中有相当大的权力，可以随心所欲地使用。

在本文的剩余部分，我们将演示一些实际的参数，让您体验一下使用`animate()`的可能性。

## 示例用途

在这一部分，我们将构建一些演示来释放`animate()`的力量。记住[这种方法不太适合非常非常复杂的动画](https://www.sitepoint.com/easily-improving-jquery-animations/)，因为动画的性能和流畅度有问题。

### 运行单个动画

运行单个动画非常简单，只需要调用一次方法。例如，我们可能想把一个元素从盒子的一边移到另一边。为了演示这个动画，我们将设置两个`div`元素，一个在另一个里面。我们将设计它们的样式，使内部的`div`具有红色背景。完成此操作的代码如下所示。

HTML:

```
<div class="rectangle">
   <div class="square-small"></div>
</div>
```

CSS:

```
.rectangle
{
   width: 300px;
   height: 20px;
   display: block;
   position: relative;
   border: 1px solid black;
   margin: 20px 0;
}

.square-small
{
   display: block;
   width: 20px;
   height: 20px;
   position: absolute;
   background-color: red;
}
```

有了这些标记和 CSS，利用`animate()`的力量，我们将把小方块从一边移到另一边:

```
$('.rectangle')
   .find('.square-small')
   .animate({
      left: 280
    }, 'slow');
```

在这段代码中，我们指定`left`属性是唯一要动画的属性。我们将动画的持续时间设置为预设值`slow`(600 毫秒)。我们使用绝对值来移动内部的`<div>`(有类`.square-small`)。该值基于我们使用前面列出的 CSS 代码设置的容器的宽度。这个解决方案不是很灵活，因为如果我们改变容器的宽度，内部的`<div>`不会到达另一边(如果我们在容器上设置了更宽的宽度)，或者会通过它(如果我们设置了更窄的宽度)。一种解决方案是基于外部和内部`<div>`的当前宽度的计算来设置`left`属性的值，如下所示:

```
left: $('.rectangle').width() - $('.rectangle').find('.square-small').width()
```

[这个例子是一个 JSbin](http://jsbin.com/pitelu/1/) :

[使用 jQuery 的 animate()](http://jsbin.com/pitelu/1/embed?html,css,js,output)