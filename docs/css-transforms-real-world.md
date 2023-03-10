# 在现实世界中使用 CSS 变换

> 原文：<https://www.sitepoint.com/css-transforms-real-world/>

在本文中，我们将学习如何在现实世界中使用 CSS 转换来解决各种任务并获得有趣的结果。具体来说，您将学习如何垂直调整元素，创建好看的箭头，构建加载动画和创建翻转动画。

HTML 元素的转换在 2012 年成为 CSS3 标准，在此之前的一些浏览器中也可以使用。转换允许你转换网页上的元素——正如我们在关于 CSS 转换的 101 篇文章中所解释的。只需一行代码就可以轻松旋转、缩放或倾斜元素，这在以前是很难做到的。2D 和 3D 变换都可用。

至于浏览器支持，所有主流浏览器都支持 2D 变换[——包括从版本 9 开始就支持的 Internet Explorer。至于 3D 转换，IE](https://caniuse.com/#feat=transforms2d) [从版本 10 开始只部分支持](https://caniuse.com/#feat=transforms3d)。

*本文不会关注转换的基础。如果你对变换不太有信心，我推荐你先阅读一下 [2D](https://www.sitepoint.com/css3-transformations-2d-functions/) 和 [3D 变换](https://www.sitepoint.com/css3-transformations-3d/)。*

## 垂直对齐的子项

任何网页设计师都知道垂直对齐元素是多么的乏味。对于一个不熟悉 CSS 的人来说，这个任务可能听起来非常简单，但实际上，在几代开发人员之间，有一大堆精心保留的技术。一些人建议将`display: inline`和`vertical-align: middle`一起使用，一些人投票支持`display: table`和伴随的风格，然而真正的老派程序员仍然用表格设计他们的站点(只是开玩笑，不要那么做！).此外，可以用 Flexbox 或网格来解决这个任务，但是对于较小的组件，转换可能是一个更简单的选择。

当元素高度可变时，垂直对齐会更加复杂。然而，CSS 转换提供了一种解决问题的方法。让我们看一个非常简单的例子，它有两个嵌套的`div`:

```
<div class="parent">
  <div class="child">
    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore
  </div>
</div>

<div class="parent">
  <div class="child">
    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam
  </div>
</div> 
```

没什么复杂的:只是两个嵌套的块，带有不同长度的 Lorem Ipsum 文本。

让我们为父对象设置宽度、高度和边框，以及一些间距，使它看起来更好:

```
.parent {
  height: 300px;
  width: 600px;
  padding: 0 1em;
  margin: 1em;
  border: 1px solid red;
} 
```

此外，将儿童字体放大一点:

```
.child {
  font-size: 1.2rem;
} 
```

结果，您会看到类似这样的内容:

![Our initial layout](img/3d6c08a2adb0972a800b98052c12a378.png)

现在，您的客户说:“请垂直对齐文本，使其显示在这些红框的中间”。不要！。但是不要害怕:我们用转变武装起来了！第一步是相对定位我们的孩子，将他们移动 50%到底部:

```
.child {
  font-size: 1.2rem;
  position: relative;
  top: 50%;
} 
```

之后，应用一个秘密成分——`translateY`函数——它将垂直重新定位元素:

```
.child {
  font-size: 1.2rem;
  position: relative;
  top: 50%;
  transform: translateY(-50%);
} 
```

那么，这里发生了什么？`top: 50%`将子元素的顶部移动到父元素的中心:

![Moving the top of the child element to the center of the parent](img/60196f0d0c30b66ce48a61f6a7d0489b.png)

但这还不够，因为我们希望孩子的中心与父母的中心对齐。因此，通过应用`translateY`,我们将孩子向上移动其身高的 50%:

![Centering vertically with translateY](img/7520cbe247abd4f46a48e9173647c500.png)

[一些开发人员报告说](https://stackoverflow.com/a/26356771/1501938)这种方法会由于元素被放置在“半像素”上而导致儿童变得模糊。对此的解决方案是设置元素的透视:

```
.child {
  // ...
  transform: perspective(1px) translateY(-50%);
} 
```

就是这个！您的孩子现在甚至可以正确对齐可变文本，这真的很好。当然，这种解决方案有点粗糙，但与 CSS Grid 相比，它可以在较旧的浏览器中工作。最终结果可以在 CodePen 上看到:

参见 [CodePen](https://codepen.io) 上的 Pen[CSS Transforms:Vertical-Align](https://codepen.io/SitePoint/pen/pOzQOm/)by site point([@ site point](https://codepen.io/SitePoint))。