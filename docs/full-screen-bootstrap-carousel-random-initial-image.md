# 具有随机初始图像的全屏引导转盘

> 原文：<https://www.sitepoint.com/full-screen-bootstrap-carousel-random-initial-image/>

在本文中，我将为[引导转盘](http://getbootstrap.com/docs/4.0/components/carousel/)构建两个简单的扩展。首先，我将创建一个全屏引导转盘幻灯片，然后我将向您展示如何在页面加载时随机化第一张幻灯片。

但是在深入研究这些扩展之前，让我们首先创建一个基于默认样式的旋转木马。

## 建造旋转木马

为了创建 carousel，我们将利用 Bootstrap 提供的 carousel 组件的基本代码:

```
<div id="carouselExampleIndicators" class="carousel slide" data-ride="carousel">

  <!-- Indicators -->
  <ol class="carousel-indicators">
    <li data-target="#carouselExampleIndicators" data-slide-to="0" class="active"></li>
    <li data-target="#carouselExampleIndicators" data-slide-to="1"></li>
    <li data-target="#carouselExampleIndicators" data-slide-to="2"></li>
  </ol>

  <!-- Wrapper for slides -->
  <div class="carousel-inner">
    <div class="carousel-item active">
      <img class="d-block w-100" src="1.jpg" data-color="lightblue" alt="First Image">
      <div class="carousel-caption d-none d-md-block">
        <h5>First Image</h5>
      </div>
    </div>
    <div class="carousel-item">
      <!-- slide content -->
    </div>
    <div class="carousel-item">
      <!-- slide content -->
    </div>

    <!-- more slides -->
  </div>

  <!-- Controls -->
  <a class="carousel-control-prev" href="#carouselExampleIndicators" role="button" data-slide="prev">
    <span class="carousel-control-prev-icon" aria-hidden="true"></span>
    <span class="sr-only">Previous</span>
  </a>
  <a class="carousel-control-next" href="#carouselExampleIndicators" role="button" data-slide="next">
    <span class="carousel-control-next-icon" aria-hidden="true"></span>
    <span class="sr-only">Next</span>
  </a>

</div>
```

注意，我们的每个图像都包含自定义的`data-color`属性。稍后，我们将使用它的值作为后备，以防相应的图像加载失败。

下一步是通过 JavaScript 初始化 carousel，并修改配置属性`interval`和`pause`的预定义值。请注意，我们选择将`pause`属性的值设置为`false`，因为我们总是希望循环处于活动状态:

```
$('.carousel').carousel({
  interval: 6000,
  pause: "false"
});
```

按照这些简单的步骤(当然也导入了所需的文件)，我们现在应该能够构建 carousel 的第一个版本了。以下是目前为止的情况:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看笔[基础引导转盘](https://codepen.io/SitePoint/pen/gapqjy/)。