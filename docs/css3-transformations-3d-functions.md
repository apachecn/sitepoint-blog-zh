# CSS3 3D 变换函数

> 原文：<https://www.sitepoint.com/css3-transformations-3d-functions/>

在我的[上一篇文章](/css3-transformations-3d/)中，我们创建了一个三维场景，其中包含一个带有三个盒子的外部#stage 元素。#stage 进行了旋转并应用了透视，因此我们可以欣赏 3D 效果:

[**查看第三次 3D 改造演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/3dtransform3.html)

![CSS3 3D](img/20e941c685bf2695f8b3eb11832c3044.png)

在这篇文章中，我们看看基本的三维功能，你可以应用到元素。

## translate、translatey、translation and translate 3d

3D 平移功能相对于元素的当前位置移动元素。您可以使用任何 CSS 长度单位(px、em、%，等等)来水平(`translateX`)、垂直(`translateY`)和移入或移出屏幕(`translateZ`)

```
#mybox1
{
	transform: translateX(-20px) translateY(4em) translateZ(10px);
}
```

幸运的是，有一个`translate3d`快捷函数，可以按顺序传递 x、y 和 z 偏移量:

```
#mybox1
{
	transform: translate3d(-20px, 4em, 10px);
}
```

## scaleX、scaleY、scaleZ 和 scale3d

就像我们在 [2D 变换](https://sitepoint.comcss3-transformations-2d-functions)中看到的，尺度函数影响元素及其所有子元素的维度。`scaleX`、`scaleY`和`scaleZ`可以传递单个缩放参数，例如

```
#mybox2
{
	transform: scaleX(1.3) scaleY(-1) scaleZ(1);
}
```

这将使元素宽度增加 130%,并在 y 轴上镜像自身。正如上一课所讨论的，`scaleZ`不会拉伸元素使其变厚，因为它的深度为零。令人困惑的是，`scaleZ`充当了所有后续`translateZ`函数的乘数。本质上，这种转变:

```
transform: translateZ(100px) scaleZ(2);
```

等同于:

```
transform: translateZ(200px);
```

但是，要警惕加工顺序。最后的变换函数首先被处理，因此:

```
transform: scaleZ(2) translateZ(100px);
```

与`translateZ(100px)`相同；从未应用过`scaleZ`。

还有一个`scale3d`快捷功能来应用所有三个 x、y 和 z 比例因子:

```
#mybox2
{
	transform: scale3d(1.3, -1, 1)
}
```

## rotateX、rotateY、rotateZ 和 rotate3d

您可以使用适当的`rotateX`、`rotateY`或`rotateZ`功能沿任意轴旋转，角度以度(deg)或弧度(rad)指定，例如

```
#mybox3
{
	transform: rotateX(45deg) rotateY(10deg) rotateZ(-20deg);
}
```

请注意，`rotate3d`快捷功能并不像您预期的那样工作。不是接受三个旋转参数，而是定义一个旋转向量；围绕从 0，0，0 到空间中另一点定义的直线的角度，例如

```
transform: rotate3d(0, 1, 0, 90deg);
```

这将围绕连接 0，0，0 到 0，1，0 的线(或 y 轴)将元素旋转 90 度。因此，它等同于:

```
transform: rotateY(90deg);
```

默认情况下，元素围绕其中心旋转，尽管您可以使用关键字(左、右、上、下、中心)、百分比或长度单位的`transform-origin`属性来改变行为，例如

```
transform-origin: right 80% -50px;
```

## 多重 3D 变换

正如我们已经看到的，多个转换函数可以传递给`transform`属性，如果它们被空白字符分隔开，例如

```
transform: translateZ(-600px) rotateX(45deg) rotateY(10deg) rotateZ(-20deg);
```

这些以相反的顺序应用。以上例子都在演示中得到了应用:

[**查看 3D 转换演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-transformations/3dtransform4.html)

![CSS3 3D](img/f702decb088933a2088ede00501b7206.png)

如果你觉得特别自虐，试试`matrix3d`函数，它接受不少于 16 个参数来定义一个 4×4 矩阵。我甚至不想解释它，而且几乎没有什么文档— [这是我找到的最好的资源](http://9elements.com/html5demos/matrix3d/)。

## 禁用转换

最后，请记住，您可以使用`none`函数关闭应用于元素的所有变换:

```
transform: none;
```

如果您感到头疼，您会很高兴地听到，在本系列的最后一部分中，我们还需要介绍一个 3D 变换属性。

## 分享这篇文章