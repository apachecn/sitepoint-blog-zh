# 介绍 pure . CSS——一个轻量级响应框架

> 原文：<https://www.sitepoint.com/introducing-pure-css-lightweight-responsive-framework/>

框架使得设计网站变得容易。您可能已经在项目中使用了 Bootstrap 之类的流行框架。今天给大家介绍一下 [Pure.css](http://purecss.io/) (简称“Pure”)，一个由 css 模块组成的小框架。如果进行缩小和压缩，它的所有模块加在一起的重量不到 4.0KB。如果您决定只使用一两个模块，您甚至可以节省更多的字节。

Pure 的极简风格确保您可以毫不费力地定制元素的外观以满足您的需求。您可以使用以下代码行将 Pure 包含在您的项目中:

```
<link rel="stylesheet"
      href="http://yui.yahooapis.com/pure/0.6.0/pure-min.css">
```

## 介绍网格系统

Pure 中的网格系统与 Bootstrap 中的有很大的不同，一旦你掌握了基础知识，它将变得强大而易于使用。在继续之前，您需要记住几件事:

*   Pure 有一个名为`pure-g`的包装网格类和名为`pure-u-*`的单元类。确保所有内容都在网格单元内，以便正确呈现。
*   宽度是基于分数计算的。分数本身由类名决定。例如，`pure-u-2-5`的宽度为 40%。
*   一个`pure-g`元素中的所有子元素必须有类名`pure-u`或`pure-u-*`。

通过使用类似`pure-u-md-2-3`的附加类名，你可以控制特定断点处不同元素的宽度。看看下面的 HTML:

```
<div class="pure-g">
  <div class="pure-u-1 pure-u-md-1-5"> One </div>
  <div class="pure-u-1 pure-u-md-2-5"> Two </div>
  <div class="pure-u-1 pure-u-md-2-5"> Three </div>
</div>
```

上面的代码做了以下事情:当屏幕尺寸小于 568px 时，所有的`divs`都将是 100%宽。当屏幕尺寸高于中等屏幕类别(768px)时，第一个`div`被设置为 20%的宽度，而其他的则各为 40%的宽度。

网格基于第五和第二十四分数系统。例如，在类名`pure-u-x-24`中，x 可以是 1 到 24 之间的任何值，包括 1 和 24。此外，`pure-u-1-12`和`pure-u-2-24`是相同的。关于网格系统更详细的讨论，请参考[官方文档](http://purecss.io/grids/)。

## 纯导航菜单

默认情况下，Pure 提供垂直菜单。最小化的样式和低特异性选择器的使用使得定制菜单更加容易。下面是创建垂直菜单的代码:

```
<div class="pure-menu">
  <span class="pure-menu-heading">Brand Name</span>

  <ul class="pure-menu-list">
    <li class="pure-menu-item">
      <a href="#" class="pure-menu-link">Home</a>
    </li>
    <li class="pure-menu-item">
      <a href="#" class="pure-menu-link">Products</a>
    </li>
    <li class="pure-menu-item">
      <a href="#" class="pure-menu-link">Contant</a>
    </li>
    <li class="pure-menu-item">
      <a href="#" class="pure-menu-link">Blog</a>
    </li>
  </ul>
</div>
```

要将上面的菜单改为水平，添加类名`pure-menu-horizontal`。此外，您可以分别使用类名`pure-menu-selected`和`pure-menu-disabled`将菜单项标记为选中或禁用。

## 创建下拉导航

创建下拉导航需要在标记上做一些小的改动。您需要将类名`pure-menu-has-children`添加到适当的菜单项中。要在悬停时显示子菜单，包括类名`pure-menu-allow-hover`。下面的代码片段应该可以澄清一些事情:

```
<div class="pure-menu pure-menu-horizontal">
  <ul class="pure-menu-list">
    <li class="pure-menu-item pure-menu-selected">
      <a href="#" class="pure-menu-link">About</a>
    </li>
    <li class="pure-menu-item pure-menu-has-children pure-menu-allow-hover">
        <a href="#" class="pure-menu-link">Services</a>
        <ul class="pure-menu-children">
          <li class="pure-menu-item">
            <a href="#" class="pure-menu-link">Designing</a>
          </li>
          <li class="pure-menu-item">
            <a href="#" class="pure-menu-link">Marketing</a>
          </li>
          <li class="pure-menu-item">
            <a href="#" class="pure-menu-link">SEO</a>
          </li>
        </ul>
    </li>
  </ul>
</div>
```

您还可以包含用普通 JavaScript 编写的示例脚本,使菜单更容易访问。它提供 ARIA 支持、子菜单箭头键导航和基于外部事件的菜单解除。

下面是 Pure 的导航菜单和下拉菜单的演示。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )使用 Pure.css 看到带下拉菜单的笔[导航菜单。](http://codepen.io/SitePoint/pen/YXQeNw/)