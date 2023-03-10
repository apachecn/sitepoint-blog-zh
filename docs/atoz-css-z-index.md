# 掌握 CSS 中的 z 索引

> 原文：<https://www.sitepoint.com/atoz-css-z-index/>

`z-index`是一个属性，用于控制文档中各层的顺序。具有较高`z-index`值的元素出现在具有较低值的元素之上。就像页面上的 x 轴和 y 轴决定元素水平和垂直放置的位置一样，`z-index`控制着它们在 z 轴上的堆叠方式。

![z-index infographic](img/0622bbae400c0639888c07ecffc2d7a9.png)

### 默认堆叠

当编写我们的 HTML 时，出现在文档中较低位置的元素自然会堆叠在更高位置的元素之上。

```
<body>
<header class="site-header"></header>
<main class="site-content"></main>
<footer class="site-footer"></footer>
</body>
```

给定这个 HTML 片段，`footer`将堆叠在主内容区域的顶部，如果它们彼此重叠放置，主内容区域将堆叠在`header`的顶部。

可以通过使用`position`属性和偏移属性`top`、`right`、`bottom`和`left`的组合来重叠元素。

如果我在这些元素中的每一个上设置`position: absolute`,它们将会一个在另一个之上。`footer`出现在文档的最后，所以默认情况下堆叠在前两个元素之上。

```
.site-header, .site-content, .site-footer {
position: absolute;
width: 400px;
padding: 20px;
}
.site-header {top: 0; left: 0;}
.site-content {top: 50px; left: 50px;}
.site-footer {top: 100px; left: 100px;}
```

如果我使用偏移属性，`top`和`left`，我们可以更清楚地看到顺序。

### 堆叠上下文

在使用`position: absolute`创建相互重叠的元素时，我们还没有创建所谓的*堆叠上下文*。

堆叠上下文可通过以下任一方式创建:

*   具有位置`absolute`或`relative`和非`auto`的`z-index`值的元素
*   具有非`auto`的`z-index`值的 flexbox 项目
*   `opacity`小于 1 的元素
*   将`transform`设置为除`none`之外的任何值的元素

到目前为止，创建和使用堆栈上下文最常见的方式是这个列表中的第一个例子，所以让我们花点时间来关注它。

回到前面的例子，我们有三个元素放置在彼此之上，但是目前，它们没有一个`z-index`值。

属性允许我们控制堆叠的顺序。

如果我在`footer`上设置`z-index: 1`，在`main`上设置`z-index: 2`，在`header`上设置`z-index: 3`，默认堆栈的顺序可以完全颠倒。

这在表面上看起来很简单；`z-index`越高，元素堆栈就越高——所以`z-index: 9999`总是在`z-index: 9`的上面。不幸的是，事情要比这复杂一些。

### `z-index`在堆叠环境中

```
<header class="site-header blue">header</header>
<main class="site-content green">content
<div class="box yellow"></div>
</main>
<footer class="site-footer pink">footer</footer>
```

如果我在`site-content`容器内添加一个框，并把它放在右下角外面，我们可以看到它在绿色框的上面和粉色框的下面。

```
.box {
position: absolute;
bottom: -25px;
right: -25px;
z-index: 4; /* won't work :( */
width: 75px;
height: 75px;
border: 1px solid #000;
}
.site-header {top: 0; left: 0; z-index: -1;}
.site-content {top: 50px; left: 50px;}
.site-footer {top: 100px; left: 100px; z-index: 3;}
```

基于我们对`z-index`的了解，我们可能认为要让这个黄色的框出现在粉色的框上面，我们可以为`z-index`设置一个更大的值。

如果我设置大于 T1 的 T0，我们看不到变化。人们经常通过尝试像`9999`这样的非常大的数字来尝试和强制堆叠，但是这样做也没有效果。看到像这样的`z-index`值散布在整个代码库中有点像[代码的味道](https://en.wikipedia.org/wiki/Code_smell)，所以如果可以的话尽量避免。

我们之所以不能得到粉色方框顶部的黄色方框的预期结果，是因为`z-index`在堆栈环境中的行为方式。

为了证明这一点，让我们看一个稍微复杂一点的例子，这个例子是我从 [MDN 网站](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)借来的。

```
<header class="site-header blue">
<h1>Header</h1>
<code>position: relative;<br/>
z-index: 5;</code>
</header>

<main class="site-content pink">
<div class="box1 yellow">
<h1>Content box 1</h1>
<code>position: relative;<br/>
z-index: 6;</code>
</div>

<h1>Main content</h1>
<code>position: absolute;<br/>
z-index: 4;</code>

<div class="box2 yellow">
<h1>Content box 2</h1>
<code>position: relative;<br/>
z-index: 1;</code>
</div>

<div class="box3 yellow">
<h1>Content box 3</h1>
<code>position: absolute;<br/>
z-index: 3;</code>
</div>
</main>

<footer class="site-footer green">
<h1>Footer</h1>
<code>position: relative;<br/>
z-index: 2;</code>
</footer>
```

```
.blue {background: hsla(190,81%,67%,0.8); color: #1c1c1c;}
.purple {background: hsla(261,100%,75%,0.8);}
.green {background: hsla(84,76%,53%,0.8); color: #1c1c1c;}
.yellow {background: hsla(61,59%,66%,0.8); color: #1c1c1c;}
.pink {background: hsla(329,58%,52%,0.8);}

header, footer, main, div {
position: relative;
border: 1px dashed #000;
}
h1 {
font: inherit;
font-weight: bold;
}
.site-header, .site-footer {
padding: 10px;
}
.site-header {
z-index: 5;
top: -30px;
margin-bottom: 210px;
}
.site-footer {
z-index: 2;
}
.site-content {
z-index: 4;
opacity: 1;
position: absolute;
top: 40px;
left: 180px;
width: 330px;
padding: 40px 20px 20px;
}
.box1 {
z-index: 6;
margin-bottom: 15px;
padding: 25px 10px 5px;
}
.box2 {
z-index: 1;
width: 400px;
margin-top: 15px;
padding: 5px 10px;
}
.box3 {
z-index: 3;
position: absolute;
top: 20px;
left: 180px;
width: 150px;
height: 250px;
padding-top: 125px;
text-align: center;
}
```

这里我们像以前一样有一个`header`、`footer`和`main`、`content`容器，但是在`site-content`里面我们有三个盒子，它们都被定位并被赋予一个`z-index`。

让我们先来看看三个主要容器——`header`、`footer`和`main`。

`header`的`z-index`为 5，因此出现在具有`z index: 4`的`main`T3 的上方。`footer`的`z-index`为 2，因此出现在`main`下方，而`z-index`为 4。目前一切都好吗？很好。

对于`main`容器中的三个盒子，事情变得有点混乱。

内容框 1 的`z-index`为 6，但看起来在`header`的下方，其下方的`z-index`为 5。

内容框 2 的`z-index`为 1，但出现在`footer`的上方，后者的`z-index`更高，为 2。

这是怎么回事？

所有这些都可以通过以下事实来解释，即所有的`z-index`值都是在它们的父堆栈上下文中解析的。因为父容器`.site-content`的`z-index`比`footer`高，所以`.site-content`中的任何定位元素都在该上下文中进行评估。

在堆叠上下文中考虑堆叠顺序的一个好方法是将其视为嵌套有序列表中的子项。

可以这样写:

*   表头:`z-index: 5`
*   主:`z-index: 4`
    *   方框 1: `z-index: 4.6`
    *   方框 2: `z-index: 4.1`
    *   方框 3: `z-index: 4.3`
*   页脚:`z-index: 2`

所以即使`header`是`z-index: 5`并且`content`盒子 1 是`z-index: 6`，它的渲染顺序是 4.6，仍然小于 5。因此，`content`框 1 出现在`header`下方。

一开始有点混乱，但是随着练习，它确实开始有意义了！

 `### `z-index`仅适用于定位元素

如果你想控制元素的堆叠顺序，你可以用`z-index`来实现。但是只有当元素的`position`值为`absolute`、`relative`或`fixed`时，`z-index`才会生效。

精确地放置元素对于构建复杂的布局或有趣的 UI 模式来说是很棒的，但是通常希望控制堆叠顺序而不将元素从页面上的原始位置移开。

如果是这种情况，可以只设置`position: relative`，而不为`top`、`right`、`bottom`或`left`提供任何值。该元素将保留在页面上的原始位置，文档流不会中断，并且`z-index`值将生效。

### 你可以有负的`z-index`

分层元素通常是为了构建复杂的形状或 UI 组件。这通常意味着将元素层层叠加，不断增加`z-index`的值。要将一个元素放在另一个元素下面的层上，它只需要有一个更小的值`z-index`，但是这个更小的值*可以是负的*。

这很有用的一个地方是当使用[伪元素](https://www.sitepoint.com/atoz-css-pseudo-elements)并且想要将它们放置在它们的父元素的内容之后。

由于堆栈上下文的工作方式，如果要将任何`:before`或`:after`元素放置在其父元素的文本内容之后，则需要在这些元素上使用负值`z-index`。

看看下面的 Codepen，用`z-index`的各种值做实验。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [GNgvxO](http://codepen.io/SitePoint/pen/GNgvxO/) 。`