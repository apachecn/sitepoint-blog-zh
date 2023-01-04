# 使用 Twitter Bootstrap 3 创建 JavaScript 滑块

> 原文：<https://www.sitepoint.com/creating-javascript-sliders-using-twitter-bootstrap-3/>

Twitter Bootstrap 3 是开发和设计内容管理系统的最佳 CSS 框架之一。使用 Twitter Bootstrap 的网格布局很容易设计博客、作品集和个人资料。许多类型的 CMS 都有一个共同点，那就是“滑块”。图像的连续自动显示，滑块可以是任何东西:展示你的最新项目，炫耀你的客户的资产，描述特别优惠，链接到新闻项目，或突出你的最新博客文章。

在本文中，我们将学习如何使用 Twitter Bootstrap 3 的 Carousel 组件创建一个 JavaScript 滑块。

## 了解 Twitter Bootstrap 3 轮播组件

轮播组件的标记如下所示:

```
<div id="carousel-example-generic" class="carousel slide" data-ride="carousel">

  <!-- Indicators -->
  <ol class="carousel-indicators">
    <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
    <li data-target="#carousel-example-generic" data-slide-to="1"></li>
    <li data-target="#carousel-example-generic" data-slide-to="2"></li>
  </ol>

  <!-- Slider Content (Wrapper for slides )-->
  <div class="carousel-inner">
    <div class="item active">
      <img src="..." alt="...">
      <div class="carousel-caption">
        ...
      </div>
    </div>
    ...
  </div>

  <!-- Controls -->
  <a class="left carousel-control" href="#carousel-example-generic" data-slide="prev">
    <span class="glyphicon glyphicon-chevron-left"></span>
  </a>
  <a class="right carousel-control" href="#carousel-example-generic" data-slide="next">
    <span class="glyphicon glyphicon-chevron-right"></span>
  </a>
</div>
```

从上面的代码中，我们了解到 Bootstrap 3 carousel 分为三个主要部分:

1.  指示器
2.  滑块内容
3.  控制

[查看使用默认 Bootstrap 3 结构创建的滑块的现场演示](http://extremecss.com/demos/bs_carousel/)。

要将任何 div 设置为滑块，我们必须应用类`carousel`和`slide`。`carousel`类将元素定义为具有“旋转木马”特征的元素。`slide`用于添加滑块变化时的滑动效果。

**指示器**是位于滑块底部中心的小圆圈，指示当前滑块位置和滑块总数。使用有序列表来声明指示器。

```
<ol class="carousel-indicators">
    <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
    <li data-target="#carousel-example-generic" data-slide-to="1"></li>
    <li data-target="#carousel-example-generic" data-slide-to="2"></li>
  </ol>
```

有序列表被赋予类`carousel-indicators`以使子元素看起来像小圆圈。每个`li`元素必须有一个`data-target`属性，其值作为父 carousel div 的 id。它们还必须有一个唯一的`data-slide-to`属性，以从“0”开始的递增顺序将整数值作为字符串。

**滑块内容**是滑块的主要部分。我们将在这个空间放置滑块的内容。使用类`carousel-inner`定义滑块内容区域。这个 div 可以再次拥有无限的`item`div。第一个`item` div 必须定义一个活动类。

```
<div class="carousel-inner">
    <div class="item active">
      <img src="..." alt="...">
      <div class="carousel-caption">
        ...
      </div>
    </div>
    ...
  </div>
```

每个`item` div 有两个子部分:`image`和`carousel-caption`。该图像用作滑块中的全幅显示内容。`carousel-caption`浮动在各自的图像上。你可以在`carousel-caption`里面放置一个`<h3></h3>`元素或者一个`<p></p>`元素——或者两个都放。

**控件**是左右箭头标记，用于手动改变滑块。

```
<!-- Controls -->
  <a class="left carousel-control" href="#carousel-example-generic" data-slide="prev">
    <span class="glyphicon glyphicon-chevron-left"></span>
  </a>
  <a class="right carousel-control" href="#carousel-example-generic" data-slide="next">
    <span class="glyphicon glyphicon-chevron-right"></span>
  </a>
</div>
```

应该有两个元素:每个箭头一个。第一个元素将使用`glyphicon glyphicon-chevron-left`类显示左箭头，第二个元素将使用`glyphicon glyphicon-chevron-right`显示右箭头。因为 Bootstrap 使用字体而不是图像来显示图标，所以我们必须遵循 glyphicon 类来显示图标。

就是这样！您已经成功地为您的网站创建了一个滑块。另外，我们没有编写一行 JavaScript 代码来让它工作。bootstrap.js 会自动为您做一些事情。

## 传送带选项

您可以通过向父 carousel div 添加更多的`data-*`属性来进一步定制滑块的默认功能。

`data-interval`用于表示每张幻灯片之间的时间间隔。它以一个数字作为值，这个数字代表毫秒。

`data-pause`用于定义何时暂停滑动活动。如果值为“hover ”,当鼠标放在滑块上时，滑块停止滑动。

`data-wrap`是一个布尔属性，告诉滑块到达终点后是否应该继续从起点滑动。

## 对于 JQuery 爱好者来说

好吧，如果你喜欢使用 jQuery 并且不喜欢属性，那么 Bootstrap 也允许你使用 JavaScript 初始化滑块！

要使 carousel div 像 carousel 一样工作，您必须编写以下代码片段:

```
$('.carousel').carousel()
```

然后可以通过在 carousel 函数内部传递一个 JSON 对象来设置 Carousel 选项。

例如:

```
$('.carousel').carousel({
  interval: 2000,
  pause: “hover”,
    wrap: true
})
```

您也可以使用如下代码片段手动触发滑块事件:

```
.carousel('pause') // to pause the slider
.carousel('cycle') // to cycle through the slider items
.carousel(2) // to forcibly display the the 3rd slide in the slider
.carousel('prev') // to show the previous slide
.carousel(‘next’) // to show the next slide in the slider
```

可以从任何 JavaScript 代码中调用上述方法来操纵滑块的正常工作。当我想显示自己的按钮而不是 Bootstrap 的箭头时，我发现`prev`和`next`方法非常有用。尤其是当它们被放置在 carousel 的父 div 之外时。

## 结论

希望您喜欢学习如何创建 JavaScript 滑块，而不需要编写成千上万行 JavaScript 代码。

我认为，如果使用得当，Twitter Bootstrap 的 JavaScript 组件将被证明是开发过程中必不可少的一部分。

他们一定会为你节省最多的开发时间。最重要的是，它们是跨浏览器兼容的，所以你不必为了让它们在老浏览器上工作而着急。

[访问演示页面。](http://extremecss.com/demos/bs_carousel/)

阅读更多关于 Twitter Bootstrap 的 JavaScript 组件的信息。

如果你是新手，你可以从这里开始学习 Twitter Bootstrap。

如果您对本文有任何建议或反馈，请在下面的评论部分提出。我很乐意听到他们的意见，并尽我所能提供帮助。

## 分享这篇文章