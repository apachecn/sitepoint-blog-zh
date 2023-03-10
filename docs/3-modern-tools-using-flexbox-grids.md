# 3 种现代工具如何使用 Flexbox 网格

> 原文：<https://www.sitepoint.com/3-modern-tools-using-flexbox-grids/>

有大量有价值的资源(文章、教程和在线工具)讨论 [flexbox 布局模块](http://dev.w3.org/csswg/css-flexbox/)。Flexbox 获得了如此大的吸引力，以至于越来越多的网格和框架支持这一强大的布局特性。

在本文中，我们将研究 3 种工具是如何整合基于 flexbox 的网格的，并且我将展示一些演示来说明这些特性。

*注意:本文期望您对 flexbox 有所了解。*

## [柔性盒网格](http://flexboxgrid.com/)

Flexbox 网格是建立在 flexbox 之上的网格系统。如果您熟悉 [Bootstrap](http://getbootstrap.com/) ，这个网格使用相同的预定义网格类和媒体查询断点。下表总结了网格的工作方式:

| 屏幕 | Viewport Size | 集装箱宽度 | 类别前缀 |
| :-: | :-: | :-: | :-: |
| 超小屏幕 | < 30em(768 像素) | 汽车 | `.col-xs-*` |
| 小屏幕 | ≥48em (768px) | 46rem (736px) | `.col-sm-*` |
| 中等屏幕 | ≥62em (992px) | 61rem (976px) | `.col-md-*` |
| 大屏幕 | ≥75em (1200px) | 71rem (1136px) | `.col-lg-*` |

要开始使用 Flexbox Grid，您必须在项目中安装并包含所需的 CSS 文件(即`flexboxgrid.css`)。关于这个过程的更多信息可以在这个项目的 GitHub 页面上找到。

现在，让我们基于这个网格创建一个响应式导航菜单。这是我们将要建造的:

![Flexbox grid example](img/04493489aacfcfedf133d311934b98b6.png)

首先，我们将`ul`元素定义为一个 flex 容器，为它分配了`row`类。然后我们使用`col-xs-2`类来指定伸缩项的宽度。该类确保所有设备的列表项的最大宽度等于`16.667%`。最后，我们通过向 flex 包装器添加`center-xs`和`middle-xs`类来水平和垂直居中我们的项目。

下面是所需的 HTML:

```
<nav>
  <ul class="row center-xs middle-xs">
    <li class="col-xs-2">
      <a href="#">Home</a>
    </li>
    <li class="col-xs-2">
      <a href="#">About</a>
    </li>
    <!-- more list items here... -->
  </ul>
</nav>
```

这里有一些 CSS 样式来增强我们菜单的外观:

```
.row {
  height: 80px;
}

ul a:before  { 
  font-family: FontAwesome;
  font-size: 22px;
}

ul li:nth-child(1) a:before {
  content: '\f015';
}

@media screen and (max-width: 800px) {
  ul a {
    font-size: 0;
  }

  ul a:before {
    font-size: 32px;
  }
}
```

注意:为了简单起见，我只包含了相应 CSS 文件中的一些规则。

见笔[一个基于 Flexbox 网格框架的灵活菜单。](http://codepen.io/SitePoint/pen/JdyNPw/)通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )。