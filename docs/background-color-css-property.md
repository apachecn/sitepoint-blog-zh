# 背景色(CSS 属性)

> 原文：<https://www.sitepoint.com/background-color-css-property/>

## 描述

此属性设置元素的`background-color`；同时指定一种前景色(`color`)是一个很好的做法，这样可以确保不会与其他地方定义的颜色或背景发生冲突。

元素的背景是该元素的`width`和`height`所覆盖的区域(无论那些维度是显式设置的，还是内容规定的)；它还包括填充和边框所覆盖的区域。应用于某个元素的`background-color`(或`background-image`)将出现在该元素的前景内容下方，以及该元素的`padding`和`border`属性所覆盖的区域。当一个元素有`transparent`(或者`dotted`或者`dashed`)边框时，这个覆盖区域是明显的，并且在边框下面(或者在点之间)可以看到背景。请注意，Internet Explorer 及更高版本不支持`transparent`边框。

如果`background-color`要显示出来，元素的某些区域必须是可见的。如果元素没有固有高度(由内容或尺寸定义)，则看不到背景。如果一个元素只包含没有被清除的浮动子元素，那么背景也不会显示，因为元素的高度为零。

元素的`background`并不像通常认为的那样被其子元素继承，只是因为默认值为`transparent`，所以`background`可以透过子元素的背景看到。

## 例子

这个样式规则为 ID 为`"example"`的元素分配一个白色背景(`#fff`):

```
#example{
  background-color: #fff;
}
```

## 价值

`color`接受任何有效的 CSS 颜色值或颜色关键字。

关键字`transparent`将`background-color`设置为透明。该值确保当前元素下面的任何元素的内容都可以透过透明背景看到。`background-color`的缺省值是`transparent`，所以没有必要指定这个值，除非您要覆盖先前的定义。

颜色值在 CSS 中可以用几种不同的方式表示(例如 RGBA 颜色值)，这个主题在 [CSS 概念章节](https://reference.sitepoint.com/css/colorvalues)中有所涉及。

## 分享这篇文章