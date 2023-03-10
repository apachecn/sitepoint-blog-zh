# :第一个孩子(CSS 选择器)

> 原文：<https://www.sitepoint.com/first-child-css-selector/>

## 描述

这个伪类只匹配父元素的第一个子元素。例如，li:first-child 匹配 ol 或 ul 元素中的第一个列表项。它与列表项的第一个子级不匹配。

例如，让我们以上面提到的 CSS 选择器为例:

```
li:first-child {
  ⋮ declarations
}
```

让我们将它应用于以下标记:

```
<ul>
  <li>This item matches the selector li:first-child.</li>
  <li>This item does not match that selector.</li>
  <li>Neither does this one.</li>
</ul>
```

只有第一个列表项元素匹配。

注意，这个伪类只适用于元素——它不适用于为文本生成的匿名框。

## 例子

该示例选择器匹配`ol`或`ul`元素中的
第一个列表项:

```
li:first-child {
  ⋮ declarations
}
```

## 分享这篇文章