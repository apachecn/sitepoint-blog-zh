# HTML5 表单属性

> 原文：<https://www.sitepoint.com/the-html5-form-attribute/>

HTML4 和 XHTML 坚持所有表单元素——包括提交按钮——都包含在一个单独的`<form>…</form>`块中。虽然这很少是一个问题，但它可能会导致设计挑战，我当然记得与早期版本的 ASP.NET 进行斗争，它在每一页上强制执行一个表单。一般来说，如果您需要表单外部的输入字段，您需要 JavaScript 在表单提交时导入它的值。

HTML5 的一个鲜为人知的特性是`form`属性。它允许您在任何孤立字段上通过 ID 引用特定的`form`,例如

```
<form id="myform1" method="post">
 <label for="name">name:</label>
 <input type="text" name="name" />

 <label for="email">email:</label>
 <input type="email" name="email" /></form>

<input type="number" form="myform1" name="age" />

<button form="myform1" type="submit">Submit</button>
```

有趣的是，`form`属性允许您将一个字段放在一个表单中，然后在另一个表单中提交。或者，您可以在 JavaScript 中更改`form`属性，而不是导入值，例如

```
<script type="text/javascript">// <![CDATA[
// grab fieldx from another form
document.getElementById("myform1").addEventListener("submit", function(e) {

	document.getElementById("fieldx").setAttribute("form", e.target.id);
	return true;

}, false);
// ]]></script>
```

## 浏览器支持

所有浏览器都支持`form`属性——除了任何版本的 Internet Explorer。仅仅因为这个原因，它可能不值得使用，除非您的孤立字段相对不重要，并且您乐于丢失数据。

*但是这种感觉对吗？*也许是因为我多年来一直在编写自包含的表单，但是我不建议使用`form`属性，除非没有其他选择。充其量是有点不雅。最坏的情况是，当您在几个月后检查代码时，它会让您和您的同事感到困惑。但令人欣慰的是，你知道它就在你需要的地方！

## 分享这篇文章