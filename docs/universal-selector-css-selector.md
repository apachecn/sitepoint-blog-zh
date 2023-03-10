# 通用选择器(CSS 选择器)

> 原文：<https://www.sitepoint.com/universal-selector-css-selector/>

### 描述

通用选择器匹配任何元素类型。如果它不是简单选择器的唯一组件，它可以是隐含的(因此可以省略)。此处显示的两个选择器示例是等效的:

```
*.warning {
  ⋮ declarations
}
.warning {
  ⋮ declarations
}
```

重要的是不要将通用选择器与通配符混淆——通用选择器不匹配“零个或多个元素”考虑下面的 HTML 片段:

```
<body>
  <div>
    <h1>The <em>Universal</em> Selector</h1>
    <p>We must <em>emphasize</em> the following:</p>
    <ul>
      <li>It's <em>not</em> a wildcard.</li>
      <li>It matches elements regardless of <em>type</em>.</li>
    </ul>
    This is an <em>immediate</em> child of the division.
  </div>
</body>
```

选择器`div * em`将匹配以下`em`元素:

*   `h1`元素中的“通用”(`*`匹配`<h1>`)
*   `p`元素中的“强调”(`*`与`<p>`匹配)
*   第一个`li`元素(`*`匹配`<ul>`或`<li>`)中的“非”
*   第二个`li`元素(`*`匹配`<ul>`或`<li>`)中的“类型”

然而，它不会匹配`<em>immediate</em>`元素，因为它是`div`元素的直接子元素——在`<div>`和`<em>`之间没有任何东西可以匹配`*`。

### 例子

此规则集将应用于文档中的每个元素:

```
* {
  margin: 0;
  padding: 0;
}
```

## 分享这篇文章