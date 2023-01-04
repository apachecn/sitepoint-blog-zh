# AtoZ CSS 截屏:CSS 颜色语法

> 原文：<https://www.sitepoint.com/atoz-css-screencast-color/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

如果没有一点色彩，网络将会是一个相当乏味的地方。

有许多不同的属性接受颜色值，CSS 中有四种不同的颜色语法，这就是我们在这个视频中要关注的。这些格式是颜色关键字，十六进制，RGB 和 HSL。

```
.box-white {
  color: white;
  color: #fff;
  color: #ffffff;
  color: rgb(255,255,255);
  color: rgba(255,255,255,1);
  color: hsl(0,0%,100%,1);
  color: hsla(0,0%,100%,1);
}
.box-black {
  color: black;
  color: #000;
  color: #000000;
  color: rgb(0,0,0);
  color: rgba(0,0,0,1);
  color: hsl(0,0%,0%,1);
  color: hsla(0,0%,0%,1);
}
```

让我们依次看一下它们。

### 关键词

CSS3 中有 147 种命名颜色。

实际上只有 140 种不同的颜色，因为其中 7 种是重复的，只是灰色的拼法不同。

这是颜色，这是关键词列表。

我个人觉得这个列表很没用——有很多可以选择，但是很难记住它们的样子。

什么是`palevioletred`？有多苍白？有多紫？有多红？

什么是`gainsboro`？

`burlywood`或者`goldenrod`怎么样？

这些并不那么有用，但幸运的是有更多的选择。

### RGB

在 CSS 中，颜色是在标准的红绿蓝颜色空间或 sRGB 中定义的，其中颜色是通过三个通道定义的:红色、绿色和蓝色。

在 CSS 中定义颜色的一种方法是使用 RGB 语法。这里，这三个部分的值的范围是从 0 到 255。在这种情况下，为每个组件指定 255，得到白色。

```
.box {
  color: rgb(255, 255, 255); /* white */
  color: rgba(255, 255, 255, 0.5); /* semi-transparent white */
}
```

我们也可以用 RGBA 制作半透明的颜色。这里，第四个值，称为 Alpha，决定了颜色的不透明度。该值是一个范围从 0 到 1 的小数，其中 0 表示完全透明，1 表示完全不透明。

`transparent`关键字表示为`rgba(0,0,0,0)`。

```
.box {
  color: transparent;
  color: rgba(0, 0, 0, 0); /* transparent */
}
```

### 十六进制

大概我最常用的颜色格式是 hex。Hex 是十六进制的缩写，十六进制是以 16 为基数的数字系统。大多数人更熟悉十进制，比如十进制。

一种十六进制颜色被分解成三对，分别表示颜色的红色、绿色和蓝色成分。这些对的值以 16 为基数，范围从 00 到 FF。你可以把 00 想成无色，FF 想成*全*色。FF 实际上是以 16 为基数的十进制数 255。

在这种情况下，我们有全红色，全绿色和全蓝色，它们结合在一起形成白色。

在第二个例子中，我们没有红色，全绿色，也没有蓝色，这给了我们绿色。

当三对中的每一对的两个值都相同时，十六进制值可以缩写成三位数的简写格式。

```
.box {
  color: #00ff00; /* green */
  color: #0f0; /* green in shorthand */
}
```

### high-speedlaunch 高速快艇

HSL 代表色调、饱和度和明度。

色调以 0 到 360 度为单位指定，对应于色轮周围的位置。0 度代表红色，然后颜色通过橙色、黄色、绿色、蓝色、紫色、粉红色混合，并在 360 度再次回到红色。

饱和度决定了颜色的鲜艳程度，从 0%单色到 100%鲜艳。

亮度也设置为百分比，并确定亮度的总量。

HSL 有相应的 HSLA 版本，允许 alpha 透明。

```
.box {
  color: hsl(0, 0%, 100%); /* white */
  color: hsla(0, 0%, 100%, 0.5); /* semi-transparent white */
}
```

### 使用

那么，我们如何使用这些颜色呢？

color 属性只影响文本——所以它允许我们设置元素的文本颜色。

但是还有许多其他属性接受颜色。例如，`background-color`或颜色在渐变中停止。我们也可以设置`border-color`或者`box-shadow`或者`text-shadow`的颜色分量。我们甚至可以改变 SVG 路径或多边形的`fill`颜色。

```
.box {
  background-color: #000;
  background-image:linear-gradient(to right, #000, #fff);
  border-color: #000;
  box-shadow: 0 0 10px #000;
}
.text {
  text-shadow: 0 0 10px #000;
}
.svg path {
  fill: #000;
}
```

### 当前颜色

还有最后一个颜色关键字，它有一个独特的(如果不是完全有用的)用途。`currentColor`是一个特殊的关键字，它将颜色属性的值链接到其他接受颜色值的属性，如`border-color`、`box-shadow`和`text-shadow`。让我们来看一个快速演示。

我们可以创建一个带有边框和阴影的盒子，如下所示，并以关键字、十六进制、rgb 或 hsl 的形式传递颜色值。如果我们使用`currentColor`而不是颜色值，那么边框和阴影的颜色是黑色——这是`color`属性的默认值。

如果我们现在设置颜色为 hotpink，它将代替 currentColor。这允许我们从边框和框阴影属性中删除颜色值。这也许能让你少敲几下键盘。

```
.box {
  color: hotpink;
  padding: 50px;
  border: 10px solid; /* border-color is hotpink */
  box-shadow: 0 0 100px; /* shadow color is hotpink */
}
```

请关注我们即将发布的快速技巧文章！

## 分享这篇文章