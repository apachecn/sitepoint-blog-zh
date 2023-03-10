# 背景图像(CSS 属性)

> 原文：<https://www.sitepoint.com/background-image-css-property/>

## 描述

此属性通过指定的 URI 设置元素的背景图像。图像放在`background-color`的上面，如果图像不透明，下面的`background-color`就看不见了。当你设置背景图片时，如果可能的话，也设置一个`background-color`，以防图片不可用。

元素的背景是该元素的宽度和高度所覆盖的区域(无论这些尺寸是显式设置的，还是内容规定的)；它还包括填充和边框所覆盖的区域。应用于某个元素的`background-color`(或`background-image`)将出现在该元素的前景内容以及该元素的`padding`和`border`属性所覆盖的区域之下。当一个元素有`transparent`(或者`dotted`或者`dashed`)边界，并且背景在边界之下(或者在点之间)时，这个覆盖区域是明显的。请注意，Internet Explorer 及更高版本不支持`transparent`边框。

有问题的元素的某些区域必须是可见的，以便`background-image`可以显示出来。如果元素没有固有高度(由内容或尺寸定义)，背景将没有任何显示空间。如果一个元素只包含未被清除的浮动子元素(参见清除),背景也不会显示，因为元素的高度为零。

默认情况下，背景图像位于元素的左上角(背景位置);它沿着`x`和`y`轴(`background-repeat`)重复，并将随着文档滚动。这些是默认设置，如果您没有显式设置任何其他设置，可以使用其他背景属性进行调整。请参考下面的其他相关内容，了解您可以用来定位和控制图像的方法。

## 例子

这个样式规则为 ID 为`"example"`的元素分配一个背景
图像:

```
#example {
  background-image:
  ➥    url(images/bg.gif);
}
```

## 价值

[URI 值](https://reference.sitepoint.com/css/uris)指定可以找到图像的位置。

值 none 确保不显示背景图像；这是默认设置，所以不需要显式定义它，除非您想覆盖以前的`background-image`声明。

值 inherit 将导致元素继承其父元素的`background-image`。通常不采用这种方法，因为元素继承的背景图像很可能会与父元素的图像重叠。在大多数情况下，父元素的背景图像可以透过元素的透明背景看到，除非设置了另一个`background-image`或`background-color`。

## 分享这篇文章