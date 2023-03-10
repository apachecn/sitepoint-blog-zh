# 列表样式图像(CSS 属性)

> 原文：<https://www.sitepoint.com/list-style-image-css-property/>

## 描述

该属性指定用作列表中项目(其 display 属性值为`list-item`的元素)的列表标记的图像。如果指定的图像可用，它将替换由`list-style-type`属性指定的任何标记。

列表标记图像的确切位置不能超过`[list-style-position](https://reference.sitepoint.com/css/list-style-position "list-style-position")`属性所允许的范围。

## 例子

该样式规则为 ID 为`"links"`的列表中的所有项目分配一个图像作为列表标记:

```
#links li {
  list-style-image:
  ➥    urlimg/link.png");
}
```

## 价值

如果属性值指定为 none，则不使用列表标记图像。相反，`list-style-type`属性将控制呈现什么类型的列表标记(如果有的话)。

如果使用 [url()函数符号](https://reference.sitepoint.com/css/uris "URIs")将该值指定为 URI，则该 URI 处的图像将被用作列表标记(如果可用的话)。

## 分享这篇文章