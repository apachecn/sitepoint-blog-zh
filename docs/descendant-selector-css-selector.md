# 后代选择器(CSS 选择器)

> 原文：<https://www.sitepoint.com/descendant-selector-css-selector/>

### 描述

后代选择器匹配作为指定元素后代的所有元素。这个选择器中的第一个简单选择器代表了**祖先元素**——一个结构上更高级的元素，比如父元素，或者父元素的父元素，等等。第二个简单的选择器表示我们试图匹配的后代元素。

我们在后代选择器中使用的组合符是一个空白字符:空格、水平制表符、回车符、换行符或换页符。因为所有的组合子都允许使用空白字符，所以在后代选择器的简单选择器之间可以包含多个空白字符。

考虑下面的 HTML 片段:

```
<ul>
  <li>Item 1</li>
  <li>
    <ol>
      <li>Sub-item 2A</li>
      <li>Sub-item 2B</li>
    </ol>
  </li>
</ul>
```

我们将尝试使用下面的选择器来匹配上面片段中的元素:

```
ul li {
  ⋮ declarations
}
```

这个后代选择器将匹配示例 HTML 中的所有四个`li`元素，因为每个元素都有一个`ul`元素作为其祖先。

在上面的例子中，我们还可以使用后代选择器来匹配`ol`中的`li`元素:

```
ul * li {
  ⋮ declarations
}
ul * * li {
  ⋮ declarations
}
ul * ol li {
  ⋮ declarations
}
ul li * li {
  ⋮ declarations
}
ul ol li {
  ⋮ declarations
}
ul li li {
  ⋮ declarations
}
ul li ol li {
  ⋮ declarations
}
```

然而，我们无法使用后代选择器来匹配无序列表中的列表项。为此，我们需要一个子选择器。

### 例子

看看这个
后代选择器的例子:

```
ul li {
  ⋮ declarations
}
```

这个选择器匹配作为一个`ul`元素的后代的所有`li`元素——也就是说，每个以`ul`元素作为其祖先的`li`元素。

## 分享这篇文章