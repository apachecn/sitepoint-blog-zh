# AtoZ CSS 截屏:translateX CSS 属性

> 原文：<https://www.sitepoint.com/atoz-css-screencast-translatex/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

当我们接近字母表的结尾和 AtoZ CSS 第一季的结尾时，以字母 x 开头的属性、值或概念并不多。

然而，有一系列的`transform`值允许元素沿着 x 轴改变它们的视觉坐标，以在页面上创建复杂而有趣的视觉细节。

在这一集里，我们将了解:

*   CSS `transform`属性
*   用`translate`和`translateX`移动元件
*   与其他方法相比，使用`translate`的性能优势

### `transform`

`transform`属性允许元素从它们在文档中的自然位置移动，同时保持原来的空间——有点像用`position:relative`移动元素的结果。

元素可以在各种不同的轴上以各种不同的方式平移、旋转、缩放或倾斜。

`transform`属性可以取以下值:

*   `matrix()`
*   `matrix3d()`
*   `translate()`
*   `translateX()`
*   `translateY()`
*   `translateZ()`
*   `translate3d()`
*   `scale()`
*   `scaleX()`
*   `scaleY()`
*   `scaleZ()`
*   `scale3d()`
*   `skew()`
*   `skewX()`
*   `skewY()`
*   `rotate()`
*   `rotateX()`
*   `rotateY()`
*   `rotateZ()`
*   `rotate3d()`
*   `perspective()`

这里有一个很长的列表，老实说，我只在日常项目中使用其中的一小部分。了解其他选项很有用，但我最常用的是:

*   `translate()`
*   `rotate()`
*   `scale()`
*   和`skew()`

也可以通过创建一个空格分隔的列表将多个`transform`值链接在一起，如下所示:

```
.some-class {
  transform: scaleX(2) rotate(10deg);
}
```

这将使元素的水平刻度加倍，并将其顺时针旋转 10 度。

### `translate`

`translate`值在 2D 空间中移动一个元素。

我这里有一张图片，上面和下面有几段文字。

`translate`函数接受一个或两个值。如果传入两个值，第一个指定沿 x 轴的平移，第二个指定沿 y 轴的平移。

```
img {
  transform: translate(10px,60px);
}
```

这些值可以是 CSS 中可用的任何有效长度。可能是`px`、`em`、`rem`、`%`等。

如果只提供了一个值，则第二个值被假定为零，因此不会发生 y 轴平移。

使用`translateX`,我们可以更清楚地了解我们想要创建的变换，因为很明显这应该只发生在 x 方向。如果我只想要这种单轴运动，`translateX`将是我的首选，因为在编写代码时，最好是清楚自己的意图。

### 表演

当动画元素时，动画`transform`属性比动画`margin`或`top`和`left`位置有好处。

当用`transform`制作元素动画时，处理被转移到*图形*处理单元(GPU)而不是 CPU。最终结果是动画更流畅，对感知响应和电池寿命的影响更小。

如需深入了解这些优势的技术视频，请观看 Paul Irish 的视频[“为什么使用 Translate()移动元素优于 Pos:ABS Top/left”](http://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/)。

当我为 AtoZ CSS 制作动画时，我第一手发现了这种情况——首先，我制作了动画容器的`margin-left`属性的动画，但发现它非常慢。使用`translate`来制作容器动画，确实提高了视觉效果。

## 分享这篇文章