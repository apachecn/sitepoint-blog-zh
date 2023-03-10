# HTML & CSS 表单指南(没有黑客！)

> 原文：<https://www.sitepoint.com/a-guide-to-html-css-forms-no-hacks/>

从历史上看，HTML 表单一直很棘手——首先，因为至少需要一点 JavaScript，其次，因为再多的 CSS 也无法让它们正常工作。

然而，在现代网络中这不一定是真的，所以让我们学习如何只用 HTML 和 CSS 来标记表单。

## 形成基本结构

![A form input with a prefilled value](img/e6ed7138c520f5f06dcb60bd94650a4a.png)

从`<form>`元素开始。

这里没什么特别的。只是简单介绍一下基本结构。

```
<form>
    ...
</form> 
```

如果您正在自然地提交表单数据(也就是说，没有 JavaScript)，您将需要包含`action`属性，其中的值是您要将表单数据发送到的 URL。`method`应该是`GET`或`POST`，这取决于你想要实现的目标(不要用`GET`发送敏感数据)。

此外，还有一个很少使用的 [`enctype`属性](https://www.w3schools.com/tags/att_form_enctype.asp)，它定义了被发送数据的编码类型。另外，`target`属性虽然不一定是表单独有的属性，但也可以用来在新选项卡中显示输出。

基于 JavaScript 的表单不一定需要这些属性。

```
<form method="POST" action="/subscribe" enctype="application/x-www-form-urlencoded" target="_blank">
    ...
</form> 
```

表单由输入组成，输入需要数据值。

```
<form>
    <input type="text"><!-- text input -->
    <input type="text" value="Prefilled value">
</form> 
```

见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
的笔 [表单 1](https://codepen.io/SitePoint/pen/jOqjEqx) 。