# id (HTML 属性)

> 原文：<https://www.sitepoint.com/id-html-attribute/>

## 描述

属性为文档中的一个元素提供了一个惟一的标识符。它可以被 a 元素用来创建一个到这个特定元素的超链接。

这个标识符也可以在 CSS 代码中作为钩子使用，用于样式化目的，或者由 JavaScript 代码(通过文档对象模型或 DOM)通过引用元素的唯一 id 来对元素进行更改或添加行为。

请注意，id 属性不能应用于以下元素:

*   `base`
*   `head`
*   `html`
*   `meta`
*   `script`
*   `style`
*   `title`

```
<span *id="thisspan"*>A uniquely identifiable element.</span>
```

## 价值

属性最重要的方面是它必须是绝对唯一的。与 class 属性不同，class 属性可以将相同的值应用于页面中的许多元素，应用于元素的`id`不能与同一页面上任何其他地方使用的`id`相匹配。

`id`属性值必须以罗马字母开头(A–Z 或 A–Z)；其后可以是字母(A–Z 或 A–Z)、数字(0–9)、连字符(-)、下划线(_)、冒号(:)和句点(。).id 值是区分大小写的，因此`<span *id="me"*>This is me</span>`和`<span *id="ME"*>This is me</a>`会被认为是同一个网页上独立的、唯一可识别的元素。

## 分享这篇文章