# 边框样式(CSS 属性)

> 原文：<https://www.sitepoint.com/border-style-css-property/>

## 句法

`border-style``:` { { `none` | `hidden` | `dotted` | `dashed` | `solid` | `double` | `groove` | `ridge` | `inset` | `outset` }  <sup>1 to 4 values</sup> | `inherit` } `;`

## 描述

速记属性`border-style`使用指定的值设置元素四边的边框样式。每个边框可以有自己的值——参考[速记属性](https://reference.sitepoint.com/css/shorthandproperties)中的助记符(麻烦),这是一个简单的记忆速记顺序的方法。

边框放置在元素背景的顶部。

## 例子

该样式规则将实线边框
分配到顶部，虚线边框分配到底部，虚线边框分配到 id 为
`"example"`的元素中段落的
左侧和右侧:

```
#example p {
  border-style: solid dotted dashed;
}
```

## 价值

*   `none` means that there is no border display, and the calculated `border-width` is zero.

*   `hidden` has the same meaning as `none`, except that when two cells share a border and the table cell has a folded border, it refers to the table border (`border-collapse:collapse;`). The value `hidden` ensures that no border is drawn, because `hidden` takes precedence over all other border styles. If `none` has been used for a border in a cell, the border will still be drawn, because the border of the adjacent cell will take precedence. See [Table Format](https://www.sitepoint.com/web-foundations/table-formatting/) for more information.

*   `dotted` Realize the border as a series of points.

*   `dashed` The border is realized as a series of dotted lines.

*   `solid` Realize the border as a solid line.

*   `double` Realize the border as two solid lines. The sum of the width of two borders and the distance between them is equal to the value set for `border-width`.

*   `groove` is a three-dimensional effect, which gives the impression that the border is carved on the canvas.

*   `ridge` is a 3D effect opposite to `groove` because the border seems to protrude from the canvas.

*   `inset` is a 3D effect, which gives a feeling that the box is embedded in the canvas. When it is used on a table to which the split border model has been applied, the `inset` value looks like the whole box is embedded in the canvas. When used with the collapsed border model, it is considered the same as the value `ridge`.

*   `outset` is a 3D effect. Contrary to the effect of `inset`, its border gives a feeling that the box protrudes from the canvas. When it's used for tables with the split border model applied, the border makes the whole box look as if it came out of the canvas. When it is used with the collapsed border model, its behavior is the same as `groove`.

之前，在 CSS1 中，用户代理被允许将所有的`dotted`、`dashed`、`double`、`groove`、`ridge`、`inset`和`outset`样式解释为`solid`。

## 分享这篇文章