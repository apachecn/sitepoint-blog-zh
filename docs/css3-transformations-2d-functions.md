# CSS3 2D 变换函数

> 原文：<https://www.sitepoint.com/css3-transformations-2d-functions/>

在我的[上一篇文章](/css3-transformations-101/)中，我们发现使用`transform`属性应用转换(Chrome、Safari 和 Opera 15 使用-webkit 前缀):

```
-webkit-transform: function(values);
transform: function(values);
```

在这篇文章中，我们将讨论所有的 2D 函数。

## 翻译、translateX 和 translateY

函数的作用是相对于当前位置移动一个元素。它接受 x 和 y 值，可以是任何典型的 CSS 长度(px、em、%，等等):

```
transform: translate(-200%, 100px);
```

[**查看翻译转换演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/translate.html)

在本例中，元素向左移动 200%，向下移动 100 像素。该百分比基于盒子的原始大小，因此 200%表示其宽度的两倍。

如果您只想向一个方向移动，您可以相应地使用`translateX`或`translateY`。

现在你可能认为这个属性毫无意义——毕竟，我们可以使用边距或绝对位置值来移动 CSS2.1 中的元素。然而，它可以派上用场。例如，假设您有一个使用`margin: 0 auto;`居中的元素。将元素向左移动 20px 会很棘手，除非你知道容器的确切尺寸——但是`translate`让它变得很容易。

## scale、scaleX 和 scaleY

`scale`影响元素及其所有子元素的尺寸。可以向其传递表示两个方向上的缩放的单个参数，例如，使元素放大两倍:

```
transform: scale(2);
```

或者，我们可以传递两个参数来水平和垂直缩放元素，例如，使元素的宽度为一半，高度为两倍:

```
transform: scale(0.5, 2);
```

或者，您可以使用`scaleX`或`scaleY`来定位一个维度。

不太为人所知的是，任何一个维度都可以接受负值，这会创建元素的镜像。例如，高出 150%的水平反射:

```
transform: scale(-1, 1.5);
```

[**查看比例尺变换演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/scale.html)

## 辐状的

如您所料，`rotate`使用以度(deg)或弧度(rad)指定的角度旋转元素，例如

```
transform: rotate(30deg);
```

[**查看旋转变换演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/rotate.html)

正值顺时针旋转，负值逆时针旋转。项目围绕其中心旋转，尽管您可以通过关键字(左、右、上、下、中心)、百分比或长度单位使用`transform-origin`属性(和`-webkit-transform-origin`)来改变行为，例如

```
/* rotate around top-left of element */
transform-origin: left top;
```

## skewX 和 skewY

`skewX`和`skewY`可用于通过传递以度(deg)或弧度(rad)为单位的角度来相应地倾斜水平和垂直轴。*(注意接受两个参数的单个`skew`函数已从规范中删除，不应使用)。*

```
transform: skewX(-10deg);
```

[**查看 skewX 转换演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/skew.html)

## 多重转换

多个转换函数可以传递给`transform`属性，如果它们被空白字符分隔开，例如

```
transform: rotate(-20deg) skewX(-10deg) scale(0.8);
```

[**查看多次变换演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/multiple.html)

如果你是一个铁杆数学家，你也可以使用矩阵函数进行变换。它接受六个参数；四个用于变换矩阵，随后两个用于尺寸缩放，例如

```
transform: matrix(1.2,0.2,0.15,1,-50,10);
```

将矩阵应用于每个点，以确定新的形状和位置。除非您使用 JavaScript 操作这些值，否则我建议使用多个值；它们更容易阅读。

## 禁用转换

最后，如果您想关闭应用于一个元素的所有变换，只需使用一个没有参数的`none`函数:

```
transform: none;
```

处理二维变换很容易。在下一课中，我们将学习 3D…

## 分享这篇文章