# div (HTML 属性)

> 原文：<https://www.sitepoint.com/div-html-attribute/>

## 描述

`div`是一个通用的块级元素。它不传达关于其内容的任何含义(不像表示段落的`p`元素，或者分别表示 1 级或 2 级标题的  或  元素)；因此，很容易根据您的需求对其进行定制。`div`元素是目前使用 CSS 识别文档结构部分和布局网页最常用的方法。

一些开发人员发现了`p`和`div`元素之间的相似之处，认为它们是可以互换的，但事实并非如此。`p`元素提供了更多的语义信息(“这是一段文字，是一小群思想的集合；下一段概述了一些不同的想法”)，而
`div`元素可以用于将几乎任何元素组合在一起。事实上，它可以包含几乎任何其他元素，不像`p`只能包含行内元素。

## 例子

下面的 HTML 显示了两个与`id`属性结合使用的`div`来标识网页的不同部分:

```
*<div id="main_navigation">* ⋮
*</div>*
*<div id="body_content">*
  <h1>Page heading</h1>
  <p>Body content</p>
*</div>*
```

## 将此用于…

`div`是一个“万能”元素——它可以包含您选择的任何内联或块级
元素，因此它没有典型的内容。

## 和睦相处

| 微软公司出品的 web 浏览器 | 火狐浏览器 | 旅行队 | 歌剧 | 铬 |
| --- | --- | --- | --- | --- |
| <abbr title="Internet Explorer 5.5">5.5</abbr> | <abbr title="Internet Explorer 6.0">6.0</abbr> | <abbr title="Internet Explorer 7.0">7.0</abbr> | <abbr title="Internet Explorer 8.0">8.0</abbr> | <abbr title="Firefox 1.0">1.0</abbr> | <abbr title="Firefox 1.5">1.5</abbr> | <abbr title="Firefox 2.0">2.0</abbr> | <abbr title="Firefox 3.0">3.0</abbr> | <abbr title="Firefox 3.5">3.5</abbr> | <abbr title="Safari 1.3">1.3</abbr> | <abbr title="Safari 2.0">2.0</abbr> | <abbr title="Safari 3.1">3.1</abbr> | <abbr title="Safari 4.0">4.0</abbr> | <abbr title="Opera 9.2">9.2</abbr> | <abbr title="Opera 9.5">9.5</abbr> | <abbr title="Opera 10.0">10.0</abbr> | <abbr title="Chrome 2.0">2.0</abbr> |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 |

此元素没有兼容性问题。列出的所有浏览器都支持
`div`元素。

## 分享这篇文章