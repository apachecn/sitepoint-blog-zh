# AtoZ CSS 快速提示:如何使用 CSS 自动值

> 原文：<https://www.sitepoint.com/atoz-css-auto/>

购买 SitePoint Premium 会员资格可以让你访问完整的 AtoZ: CSS 系列。

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),从字母表中的一个字母开始。在本文中，我为您添加了一个关于`auto`属性的新的快速提示/课程。

## a 代表`auto`

CSS 值对于自动调整内容以适应网页非常有用。该值有多种用途——您可以使用它来包含溢出内容，作为 clearfix 的替代方案，使图像保持比例，甚至使内容在页面中居中。我将通过这三个简单的提示教你如何做到这一点。

### 提示 1

当内容可能会流出其包含元素时，`auto`值非常有用。设置`overflow: auto`将在需要时添加一个滚动条来保持包含元素中的内容。

```
.content-box {
  width: 300px;
  height: 300px;
  border: 1px solid #cc3f85;
  overflow: auto;
}
```

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔 [CSS A 到 Z: A —溢出提示 1](http://codepen.io/SitePoint/pen/OXqqLg/) 。

通过移除`overflow`属性来测试上面的 CodePen 代码片段，并观察不适合包含框的内容会发生什么。

### 提示 2

`overflow: auto`也可作为 clearfix 的替代方案。这创建了一个[新的块格式化上下文](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Block_formatting_context)，它包含了浮动并防止容器崩溃。在某些情况下，这可能是使用`overflow: hidden`的更好替代方案。

```
.container {
  width: 600px;
  border: 4px solid #9be22d;
  overflow: auto;
}

.blue-box {
  float: left;
  width: 200px;
  height: 200px;
  background-color: #66d9ef;
}

.pink-box {
  float: right;
  width: 200px;
  height: 200px;
  background-color: #cc3f85;
}
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔 [CSS A 到 Z: A —溢出提示 2](http://codepen.io/SitePoint/pen/NAJJPy/) 。

通过移除`overflow`属性来测试上面的 CodePen 片段，并观察包含元素是如何折叠的。

### 提示 3

`auto`值也可用于保持图像比例。

如果需要调整包含元素中原始图像的大小，只需显式设置宽度或高度(相对于包含元素)，并将另一个元素设置为 auto。这将保持图像的比例，避免任何失真。

```
.image-holder {
  width: 50%;
  border: 1px solid;
}

img {
  width: 40%;
  height: auto;
}
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔 [CSS A 到 Z: A —溢出提示 3](http://codepen.io/SitePoint/pen/Wxmmvz/) 。

通过调整窗口大小来测试上面的[代码片段](http://codepen.io/SitePoint/pen/Wxmmvz)，并观察图像如何保持比例。