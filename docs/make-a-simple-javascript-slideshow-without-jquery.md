# 不用 jQuery 制作一个简单的 JavaScript 幻灯片

> 原文：<https://www.sitepoint.com/make-a-simple-javascript-slideshow-without-jquery/>

*这篇文章由[丹·普林斯](https://www.sitepoint.com/author/dprince/)和[克里斯·佩里](https://www.sitepoint.com/author/cperry/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

"我只想做一个简单的 JavaScript 幻灯片，不需要 jQuery . "

幻灯片——也称为图像转盘、滑块或旋转横幅——是学习 JavaScript 的人经常要求的教程。

在本教程中，我们将讨论以下主题:

*   制作一个基本的幻灯片，不需要任何像 jQuery 这样的外部库
*   了解 UX 和可访问性问题，包括是否应该使用幻灯片
*   将控制添加到您的幻灯片显示。

不使用库来制作幻灯片的主要好处是，由于代码更少，您的页面执行得更好，并且您可以在任何地方使用幻灯片，而不必担心加载任何额外的文件。

本教程假设您了解一些 JavaScript，包括函数、点击事件和样式改变。对于那些觉得有帮助的人，我写了一个[快速路线图，告诉他们要尽快学会用 JavaScript 做实际的事情](https://simplestepscode.com/learn-javascript/)。

## 制作基本的幻灯片

### HTML

对于 HTML，我们需要一个幻灯片容器，以及幻灯片本身。这就是它的样子:

```
<ul id="slides">
    <li class="slide showing">Slide 1</li>
    <li class="slide">Slide 2</li>
    <li class="slide">Slide 3</li>
    <li class="slide">Slide 4</li>
    <li class="slide">Slide 5</li>
</ul> 
```

### CSS

核心 CSS 需要完成这些事情:

*   定义幻灯片的容器
*   将载玻片在容器中上下叠放
*   定义幻灯片显示或隐藏时的外观
*   为淡入淡出效果转换不透明度。

在查看 CSS 之前，请记住您可能需要更改类名和 id 名，以避免在您自己的站点中发生冲突。这篇文章中的名字被挑选得简短易读。

下面是核心 CSS 的样子:

```
/*
essential styles:
these make the slideshow work
*/

#slides {
    position: relative;
    height: 300px;
    padding: 0px;
    margin: 0px;
    list-style-type: none;
}

.slide {
    position: absolute;
    left: 0px;
    top: 0px;
    width: 100%;
    height: 100%;
    opacity: 0;
    z-index: 1;

    -webkit-transition: opacity 1s;
    -moz-transition: opacity 1s;
    -o-transition: opacity 1s;
    transition: opacity 1s;
}

.showing {
    opacity: 1;
    z-index: 2;
} 
```

现在，您可以添加其他样式来更改幻灯片显示的外观。我在这个演示中使用了以下内容:

```
/*
non-essential styles:
just for appearance; change whatever you want
*/

.slide {
    font-size: 40px;
    padding: 40px;
    box-sizing: border-box;
    background: #333;
    color: #fff;
}

.slide:nth-of-type(1) {
    background: red;
}
.slide:nth-of-type(2) {
    background: orange;
}
.slide:nth-of-type(3) {
    background: green;
}
.slide:nth-of-type(4) {
    background: blue;
}
.slide:nth-of-type(5) {
    background: purple;
} 
```

### JavaScript

JavaScript 只有一个任务:隐藏当前幻灯片，显示下一张。要做到这一点，我们只需改变相关幻灯片的类别。

JavaScript 看起来是这样的:

```
var slides = document.querySelectorAll('#slides .slide');
var currentSlide = 0;
var slideInterval = setInterval(nextSlide,2000);

function nextSlide() {
    slides[currentSlide].className = 'slide';
    currentSlide = (currentSlide+1)%slides.length;
    slides[currentSlide].className = 'slide showing';
} 
```

让我们来分析一下这里发生了什么。

1.  首先，我们使用`querySelectorAll`从容器中获取幻灯片。
2.  接下来，我们设置一个变量来跟踪当前幻灯片。
3.  然后，我们创建一个间隔，每两秒钟放映一张幻灯片(表示为 2000 毫秒)。

让我们更深入地看看`nextSlide`函数内部发生了什么:

*   首先，我们改变当前幻灯片的类别，使其不显示。CSS 过渡自动处理淡出。
*   然后我们给当前幻灯片加 1，但是如果我们已经到达幻灯片的末尾，我们使用%操作符循环回到 0。快速提醒一下,%运算符将两个数相除，然后将余数吐出。这对于那些需要像时钟或日历这样的循环进行数学运算的情况来说非常有用。在这种情况下，我们有 5 张幻灯片，所以下面是每个数字发生的情况:1%5=1，2%5=2，3%5=3，4%5=4，5%5=0。
*   一旦我们有了新幻灯片的编号，我们就更改该幻灯片的类别，以便显示该幻灯片。同样，我们的 CSS 不透明度过渡自动处理渐变效果。

恭喜你。现在你有了一个基本的幻灯片。

兼容性注意:
CSS 转场与 IE9 及以下版本不兼容，因此幻灯片显示将通过显示下一张幻灯片来优雅地降级，而不是淡入淡出效果。

这是基本的幻灯片放映:

在 [CodePen](http://codepen.io) 上用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看不含 jQuery 的 Pen [基础 JavaScript 幻灯片。](http://codepen.io/SitePoint/pen/KzjpGj/)