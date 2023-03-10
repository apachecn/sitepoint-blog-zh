# 高级 Snap.svg

> 原文：<https://www.sitepoint.com/advanced-snap-svg/>

在[之前的一篇文章](https://www.sitepoint.com/introdution-snap-svg/)中，我们已经看到了如何开始使用 Snap.svg。在这篇文章中，我们将进一步了解第一篇文章中提到的新特性。

## 掩饰

让我们先回忆一下如何创建一个简单的图形表面，然后加载一个图像:

```
var paper = Snap(800, 600),
    img = paper.image('bigImage.jpg', 10, 10, 300, 300),
    bigCircle = s.circle(150, 150, 100);
```

目前，圆圈覆盖了图像的中心。

不过，有点遗憾的是，你只能看到矩形的图像。也许你的设计师创造了漂亮的圆形按钮或图像。当然，有几种解决方案，但它们都给你留下了另一个问题:最好的情况是，设计师可以给你一个外部与页面背景匹配的图像，这样它看起来会是圆形的。然而，假设你有一个纯色背景，如果你必须改变它的颜色，你必须编辑图像。你可以使用透明，但是你要么需要更重的格式，比如 PNG，要么需要松散质量的 gif。也许再过几年， [WebP](http://en.wikipedia.org/wiki/WebP) 将被所有浏览器完全支持，这将结束这个难题。无论哪种方式，如果你的图像需要交互性，你将会被一个矩形形状所困，以响应诸如`mouseenter`、`mouseout`、`click`等事件。

在过去与 Flash 打交道很长一段时间后，SVG 中最令人沮丧的事情之一是无法使用 SVG 1.1 中引入的[蒙版](https://www.w3.org/TR/SVG/masking.html#Masking)。在 Snap 中，对任何元素(包括图像)应用蒙版非常简单:

```
bigCircle.attr('fill', '#fff'); //This is IMPORTANT

img.attr({
    mask: bigCircle
});
```

基本上，我们只需要将 mask 属性赋给我们的元素。我们必须小心使用作为实际遮罩的元素。由于最终元素的不透明度将与蒙版元素中的白色水平成比例，如果我们希望图像完全不透明，我们必须用白色填充圆形。虽然一开始这可能看起来很烦人，但它为惊人的效果打开了许多可能性，正如我们将在下一节中看到的。

很明显，你可以将不同的形状组合在一起，创造出复杂的面具。Snap 提供了一些语法糖来帮助您:

```
var smallRect = paper.rect(180, 30, 50, 40),
    bigCircle = paper.circle(150, 150, 100),
    mask = paper.mask(bigCircle, smallRect);

mask.attr('fill', 'white');

img.attr({
    mask: mask
});
```

[`Paper.mask()`](http://snapsvg.io/docs/#Paper.mask) 方法相当于`Paper.g()`，实际上可以被它无缝替代。

## 剪报

[裁剪路径](https://www.w3.org/TR/SVG/masking.html#ClippingPaths)限制了可应用绘画的区域，因此当前激活的裁剪路径所包围的区域之外的任何绘图部分都不会被绘制。剪贴路径可以视为一个遮罩，其中可见区域(在剪贴路径内)的 alpha 值为 1，隐藏区域的 alpha 值为 0。唯一的区别是，虽然被遮罩隐藏的区域仍然会对事件做出响应，但被裁剪掉的区域不会。

Snap 没有用于剪辑的快捷方式，但是您可以使用`attr()`方法设置任何元素的`clip`、`clip-path`和`clip-route`属性。

## 梯度

SVG 1.1 允许使用渐变来填充形状。当然，如果我们使用这些形状来填充蒙版，我们可以通过改变蒙版的填充来利用这种可能性来指定最终绘图的 alpha 级别，并创建惊人的效果。Snap 提供了创建渐变的快捷方式，稍后可以将渐变分配给其他元素的`fill`属性。如果我们稍微修改一下前面的代码，例如:

```
var gradient = paper.gradient('r()#fff-#000');
mask.attr('fill', gradient);
```

如果您测试这段代码，最终的效果将不会完全符合您的预期。那是因为我们使用了**相对辐射梯度类型**，用上面小写的‘r’表示。相对渐变是为组中的每个元素单独创建的(作为复合蒙版)。如果您喜欢整个群组使用单一渐层，您可以使用绝对版本的指令。`'R()#fff-#000'`是一个**绝对辐射梯度**，从中心的白色填充开始，在边缘逐渐变为黑色。

我们可以通过为任何元素的`fill`属性指定 SVG 渐变来获得相同的结果:

```
mask.attr('fill', 'L(0, 0, 300, 300)#000-#f00:25-#fff');
```

在最后一个例子中，我们展示了一个更复杂的渐变。除了不同类型(**绝对线性**)之外，这个渐变从(0，0)到(300，300)，从黑色到 25%的红色到白色。

`gradient()`方法接受一个字符串。更多细节在 Snap 的[文档](http://snapsvg.io/docs/#Paper.gradient)中解释。

也可以使用页面中任何 svg 元素的现有渐变:

```
<svg id="svg-test">
    <defs>
      <linearGradient id="MyGradient">
        <stop offset="5%" stop-color="#F60" />
        <stop offset="95%" stop-color="#FF6" />
      </linearGradient>
    </defs>
  </svg>
```

```
paper.circle(50, 50, 50, 50).attr('fill', Snap('#svg-test').select('#MyGradient'));
```

## 模式

图案允许通过重复出现另一个 svg 形状、渐变或图像来填充形状。Snap 提供了 [`Element.toPattern()`](http://snapsvg.io/docs/#Element.toPattern) 方法(以前是`pattern()`，现在已经废弃了)，可以从任何 Snap 元素中创建一个模式。

创建一个模式并用它填充一个元素非常简单:

```
var p = paper.path("M10-5-10,15M15,0,0,15M0-5-20,15").attr({
                      fill: "none",
                      stroke: "#bada55",
                      strokeWidth: 5
                  }).toPattern(0, 0, 10, 10),
    c = paper.circle(200, 200, 100).attr({
                                            fill: p
                                        });
```

相反，如果我们想要结合渐变和图案，那就是另一回事了，而且稍微复杂一点！
作为一个例子，让我们来看看如何创建一个结合了辐射渐变和类似上图的图案的蒙版:

```
//assuming the shapes bigCircle and smallRect have already been defined, as well as 'paper'
var mask = paper.g(bigCircle, smallRect),
    gradient = paper.gradient("R()#fff-#000"),
    pattern = paper.path("M10-5-10,15M15,0,0,15M0-5-20,15").attr({
        fill: "none",
        stroke: "#bada55",
        strokeWidth: 5
    }).toPattern(0, 0, 10, 10);

mask.attr('fill', pattern); //we need to set this before calling clone!
mask.attr({
    mask: mask.clone()      //makes a deep copy of current mask
});

img.attr({
    mask: mask
});
```

我们基本上必须创建一个两级地图。在我们的图像上使用的最终贴图，我们用渐变填充，有一个贴图本身也用渐变填充。结果相当可观！原来这也是一个向您介绍`clone()`方法的好机会，该方法做了您想象的事情——创建它所调用的元素的深层副本。

## 动画片

动画是 Snap.svg 最优秀的特性之一。有几种处理动画的方法，行为略有不同。

### `Element.animate()`

我们先从最简单的动画方法开始， [`Element.animate()`](http://snapsvg.io/docs/#Element.animate) 。这种方法允许用户同步制作任意数量的元素属性的动画。当然，属性的初始值是它的当前值，而最终值是在`animate()`的第一个参数中指定的。除了要更改的属性之外，还可以传递动画的持续时间、它的轻松程度以及动画完成后将被调用的回调。

一个例子会让一切更清楚:

```
bigCircle.animate({r: 10}, 2000);
```

这将简单地在两秒钟内把我们面具上的大圆缩小到一个更小的半径。

### `Set.animate()`

您可以单独激活组(集)中的元素。但是，如果你想同步制作整套动画呢？轻松点。可以用 [`Set.animate()`](http://snapsvg.io/docs/#Set.animate) 。这将对集合中的所有元素应用相同的转换，确保各种动画之间的同步性，并通过将所有更改收集在一起来提高性能。

```
mask.animate({'opacity': 0.1}, 1000);
```

您也可以独立但同步地激活集合中的每个元素。`Set.animate()`接受可变数量的参数，因此您可以传递一个数组，其中包含您需要制作动画的每个子元素的参数:

```
var set = mask.selectAll('circle');  //Create a set containing all the circle elements in mask's subtree (1 element)
paper.selectAll('rect')                //Select all the rect in the drawing surface (2 elements)
        .forEach(function(e) {set.push(e);}); //Add each of those rectangles to the set previously defined
set.animate([{r: 10}, 500], [{x: 20}, 1500, mina.easein], [{x: 20}, 1500, mina.easein]); //Animate the three elements in the set
```

假设到目前为止你已经正确地遵循了我们的示例代码(在 [CodePen](http://codepen.io/mlarocca/pen/gtlbx) 上试试)，在你的浏览器控制台上运行上面的代码，你会看到这三个元素是如何同步但独立地动画化的。上面的代码也是一个引入集合(作为`select()`和`selectAll()`方法的结果)的机会，以及在它们上面定义的一些有用的方法。

创建集合的另一种方法是将元素数组传递给 Snap 构造函数方法:

```
var set2 = Snap([bigCircle, smallRect]);
```

### `Snap.animate()`

你可以制作任何数字属性的动画，但是`animate()`不能用于其他类型，例如，如果你试图制作`text`元素的`text`属性的动画，它会弄乱你的`text`元素。然而，还有另一种方法可以获得这样的效果，即在 Snap 中调用`animate()`的第三种方法。

通过调用 Snap 对象的 animate 方法，可以更详细地指定将在动画的每一步执行的动作。这有助于将复杂的动画组合在一起，并同步运行它们(尽管`Set.animate()`方法是处理这个问题的正确方法)，以及制作复杂的非数字属性的动画。

例如，让我们创建一个文本元素并制作动画:

```
var labelEl = paper.text(300, 150, "TEST"),
    labels = ["TEST", "TETT","TEUT","TEVT","TEXT","TES-","TE--","T---", "----", "C---", "CH--", "CHE-", "CHEC-", "CHECK"];
Snap.animate(0, 13, function (val) {
    labelEl.attr({
        text: labels[Math.floor(val)]
    });
}, 1000);
```

## 事件处理

回到蒙版和图像的最初比较，你可以获得和我们在上一节展示的动画 gif 相同的效果。但是，如果您希望在响应用户交互时重现这种行为，那么使用 SVG 的改进就更有意义了。您仍然可以找到一种方法来使用多个 gif，但是，除了失去灵活性之外，您将无法轻松获得相同的质量:

```
img.click(function(evt) {
    this.minified = !this.minified;
    bigCircle.animate({
        r: !this.minified ? 100 : 10
    }, 1500);
});
```

稍后可以使用 [`Element.unclick()`](http://snapsvg.io/docs/#Element.unclick) 方法删除点击处理程序。

同样可以处理的事件还有 [`dblclick`](http://snapsvg.io/docs/#Element.dblclick) 、 [`mousedown`](http://snapsvg.io/docs/#Element.mousedown) 和 [`mouseup`](http://snapsvg.io/docs/#Element.mouseup) 、 [`mousemove`](http://snapsvg.io/docs/#Element.mousemove) 、 [`mouseout`](http://snapsvg.io/docs/#Element.mouseout) 和 [`mouseover`](http://snapsvg.io/docs/#Element.mouseover) ，以及许多面向移动的事件，如 [`touchstart`](http://snapsvg.io/docs/#Element.touchstart) 和 [`touchend`](http://snapsvg.io/docs/#Element.touchend) 。

对于那些习惯 jQuery 或 D3 接口的读者来说，Snap 中没有手动处理其他事件的`on()`方法。如果您需要一个超出 Snap 提供的处理程序的自定义行为，您可以检索任何元素的`node`属性，该属性又包含对相关 DOM 元素的引用，并且(可能在 jQuery 中包装它之后)您可以直接向它添加处理程序和属性:

```
img.node.onclick = function () {
    img.attr("opacity", 0.1);
};
```

### 拖放

使用 [`Element.drag()`](http://snapsvg.io/docs/#Element.drag) 方法，捕捉使激活任何元素、组或集合的拖放变得特别容易。如果不需要任何自定义行为，可以不带任何参数调用它:

```
labelEl.drag();   //handle drag and drop for you
```

但是，如果您需要一些特殊的行为，您可以为`onmove`、`ondragstart`、`ondragend`事件传递定制的回调和上下文。要知道，如果你想通过接下来的几个，就不能省略`onmove`回调。

添加一个拖动处理程序不会隐藏`click`事件，该事件将在`ondragend`事件之后被触发，除非被明确禁止。

## 加载现有 SVG

这个伟大的库的最大优点之一是它支持现有 SVG 代码的重用。您可以将它作为一个字符串“注入”,或者更好的是，您可以读取一个现有的文件，然后更改它。

你可以自己试试。下载并保存这张[漂亮的 svg 图](http://www.clker.com/cliparts/a/A/V/h/a/w/ringing-phone.svg)到你的项目根目录。接下来将它加载到您的页面中，根据我们的喜好更改它的样式或结构，甚至在将它添加到我们的 DOM 树之前，添加事件处理程序等等。

```
Snap.load('ringing-phone.svg', function (phone) {
    // Note that we traverse and change attr before SVG is even added to the page (improving performance)
    phone.selectAll("path[fill='#ff0000']").attr({fill: "#00ff00"});
    var g = phone.select("g");
    paper.append(g);    //Now we add the SVG element to the page
});
```

*注意*:由于浏览器中的同源策略，您需要在本地服务器上运行这个例子来测试 load 方法。

## 性能改进

操作 DOM 时提高性能的一种方法是使用[文档片段](https://developer.mozilla.org/en/docs/Web/API/DocumentFragment)。片段是 DOM 节点的最小容器。几年前引入的，它们允许您廉价地操作整个子树，然后用两个方法调用而不是`n`克隆并添加一个带有`n`节点的整个子树到我们的页面。实际的区别在[约翰·瑞西格的博客](http://ejohn.org/blog/dom-documentfragments/)中有详细解释。

Snap 也允许本地使用片段，有两种方法:

1.  `Snap.parse(svg)`接受一个参数，一个带有 SVG 代码的字符串，解析它并返回一个片段，该片段可以在以后附加到任何绘图表面。

2.  `Snap.fragment(varargs)`获取可变数量的元素或字符串，并创建一个包含所有提供元素的片段。

特别是对于大型 svg 图形，如果使用得当，片段可以节省大量的性能。

## 结论

我们关于 advanced Snap.svg 的文章到此结束。现在读者应该清楚地知道他们可以用这个库做什么，以及如何做。如果您有兴趣了解更多，Snap 文档是一个很好的起点。

几个有用的链接:

*   Snap.svg [教程](http://snapsvg.io/start/)。
*   Snap.svg [文档](http://snapsvg.io/docs/)。

## 分享这篇文章