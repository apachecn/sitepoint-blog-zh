# 子选择器(CSS 选择器)

> 原文：<https://www.sitepoint.com/child-selector-css-selector/>

### 描述

该选择器匹配指定元素的所有直接子元素。子选择器中的组合子是一个大于号(>)。它可能被空白字符包围，但如果是这样，Windows 上的 Internet Explorer 5 将错误地将其视为后代选择器。所以最佳实践是避免这个组合符周围的空白。

考虑这个 HTML 片段:

```
<ul>
  <li>Item 1</li>
  <li>
    <ol>
      <li>Subitem 2A</li>
      <li>Subitem 2B</li>
    </ol>
  </li>
</ul>
```

让我们试着用下面的选择器来匹配上面片段中的元素:

```
ul>li {
  ⋮ declarations
}
```

上面的子选择器只匹配两个 li 元素，它们是 ul 元素的子元素。它将不匹配子项，因为它们的父项是 ol 元素。

### 例子

下面是一个子选择器工作的例子:

```
ul>li {
  ⋮ declarations
}
```

该选择器匹配所有作为`ul`元素的直接子元素的`li`元素——也就是说，所有以`ul`元素为父元素的`li`元素。

## 分享这篇文章