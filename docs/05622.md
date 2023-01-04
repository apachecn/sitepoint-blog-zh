# 背景-重复(CSS 属性)

> 原文：<https://www.sitepoint.com/background-repeat-css-property/>

## 描述

`background-repeat`属性控制背景图像是否重复(平铺),如果重复，该属性定义图像沿哪个指定轴(x、y 或两者)重复。

默认情况下，`background-image`沿垂直轴和水平轴重复，并在两个方向重复。我们使用`background-repeat`属性来指定图像应该沿着哪个轴重复。

当背景图像重复时，首先根据`background-position`属性放置它，然后从该点开始在两个方向上重复。例如，放置在中心(元素的中心)的背景位置的`background-image`，其背景重复值为 repeat，将从中心开始沿 x 和 y 轴的两个方向重复，即上下左右。

元素的背景是该元素的宽度和高度所覆盖的区域(无论这些尺寸是显式设置的，还是内容规定的)；它还包括填充和边框所覆盖的区域。应用于元素的背景色(或背景图像)将出现在该元素的前景内容以及该元素的 padding 和 border 属性所覆盖的区域之下。当元素具有透明(或点或虚线)边界，并且在边界下面(或点之间)可以看到背景时，这个覆盖区域是明显的。请注意，Internet Explorer 及更高版本不支持透明边框。

如果要透过背景图像显示，所讨论的元素的某些区域必须是可见的。如果元素没有固有高度(由内容或尺寸定义)，背景将不可见。如果一个元素只包含未被清除的浮动子元素(请参见清除)，则不会显示任何背景，因为元素的高度将为零。

CSS2.1 规范中没有定义内联元素上背景图像的平铺和定位，但在未来的版本中可能会解决这个问题。

## 例子

这个样式规则使得分配给 ID 为`"example"`的元素的`background-image`沿着 *x* 轴重复:

```
#example{
  background-repeat: repeat-x;
}
```

## 价值

`repeat`

值`repeat`确保`background-image`在两个方向(即，左右和上下)以及沿两个轴重复，直到元素的背景被完全覆盖。

`repeat-x`

值`repeat-x`确保`background-image`只沿着 *x* 轴(即水平轴的左右两个方向)重复，直到元素的背景沿着该轴被完全覆盖。

`repeat-y`

值`repeat-y`确保`background-image`仅沿 *y* 轴(即上下两个方向的垂直轴)重复，直到元素的背景沿该轴被完全覆盖。

`no-repeat`

值`no-repeat`确保`background-image`不会在任何方向重复，并且只有图像的一个实例会被放置在由`background-position`指定的坐标上。

如果没有指定`background-position`，
图像被放置在元素的默认左上角位置(0，0)。

## 分享这篇文章