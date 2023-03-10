# border-bottom (CSS 属性)

> 原文：<https://www.sitepoint.com/border-bottom-css-property/>

## 句法

border-bottom: { [ `border-width` ] [ `border-style` ] [ `border-color` ] | inherit } ;

## 描述

`border-bottom`速记属性将`border-bottom-width`和/或`border-bottom-style`和/或`border-bottom-color`同时设置到一个元素的底部。

边框放置在元素背景的顶部。

## 例子

该样式规则为 ID 为`"example"`的元素内的段落底部指定一个 2px 蓝色边框:

```
#example p {
  border-bottom: 2px solid blue;
}
```

## 价值

关于允许值和初始值的具体信息，请参考以下单个属性:[边框宽度](https://reference.sitepoint.com/css/border-width)、[边框样式](https://reference.sitepoint.com/css/border-style)、[边框颜色](https://reference.sitepoint.com/css/border-color)。

负长度值是非法的。

与大多数简写属性一样，您不需要指定列出的所有属性，但是任何省略的属性都将恢复为默认值。在`border-style`的情况下，如果您省略一个值，则不会显示任何边框，因为默认值是`none`。只有当`border-style`属性被设置为除了`none`或`hidden`之外的值，或者在最初的简写声明之后被显式重述时，边框才是可见的。否则，将不会显示边框，并且`border-width`将被重置为零。因此，在使用速记符号时，为边框的样式指定一个值是一个好习惯。

## 分享这篇文章