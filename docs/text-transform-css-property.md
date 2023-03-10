# 文本转换(CSS 属性)

> 原文：<https://www.sitepoint.com/text-transform-css-property/>

## 句法

`text-transform``:` { `capitalize` | `lowercase` | `none` | `uppercase` | `inherit` } `;`

## 描述

此属性控制元素的文本内容是否大写以及如何大写。

## 例子

这些样式规则使得
`h1`标题仅使用大写字母，而`h2`标题中每个单词的第一个
字母将是
大写:

```
h1 {
  text-transform: uppercase;
}
h2 {
  text-transform: capitalize;
}
```

## 价值

*   T2`capitalize`

Transforms the first character in each word to uppercase; all other characters remain unaffected—they’re not transformed to lowercase, but will appear as written in the document

*   T2`lowercase`

Transforms all characters to lowercase

*   T2`none`

Produces no capitalization effect at all

*   T2`uppercase`

Transforms all characters to uppercase

## 分享这篇文章