# 介绍新的 HTML5 模板标签

> 原文：<https://www.sitepoint.com/html5-template-tag/>

现代 web 应用程序使用 DOM 操作来动态更改页面区域或插入值。一个典型的例子是图表；初始页面可以返回 HTML 列标题，然后初始化一个返回几个数据记录的 Ajax 请求。然后将数据添加到表中— *但是如何添加呢？*开发者有两种选择:

1.  JavaScript 用于构建附加到表中的 HTML 行字符串或 DOM 片段。这看起来很容易，直到你需要做出改变，必须在 JavaScript 中寻找相关的 HTML 代码。为了简化工作，开发人员经常从 Ajax 调用中返回完整的 HTML 字符串片段，但这使得有效负载更加冗长，并为跨站脚本攻击提供了可能性。
2.  或者，在 HTML 页面中创建一个空的第一行，作为所有其他行的模板。这应该更容易维护，但是您需要将它从 DOM 中移除，或者用 display:none 对其进行样式化，以确保它不会出现。

这两种解决方案都不是特别好。

幸运的是， [W3C 引入了一个新的`template`标签](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/templates/index.html)，它提供了一种将 HTML 标记片段定义为原型的机制。*(也许这也会安抚那些因 hgroup 元素的[消亡而不安的人吧！)](https://www.sitepoint.com/html5-hgroup-element-dropped/)*

本质上，模板可以用来将 HTML 代码片段插入到页面中，例如

```
<template id="mytablerow">
<tr>
<td class="record"></td>
<td></td>
<td></td>
</tr>
</template> 
```

几乎可以在任何地方定义`template`代码——`head`、`body`甚至是`frameset`。然而:

1.  不会显示模板
2.  模板不被视为文档的一部分，即使用 document . getelementbyid(" my tablerow ")不会返回子节点
3.  模板在使用前是不活动的，例如，不下载附带的图像，不播放媒体，不运行脚本等。

## 使用模板

要使用模板，必须将其克隆并插入到 DOM 中。例如，假设以下 HTML:

```
<table id="mytable">
<thead>
<tr>
<td>ID</td>
<td>name</td>
<td>twitter</td>
</tr>
</thead>
<tbody>

<!-- rows to be appended here -->

</tbody>
</table>

<!-- row template -->
<template id="mytablerow">
<tr>
<td class="record"></td>
<td></td>
<td></td>
</tr>
</template> 
```

我们可以在 JavaScript 中克隆一个新行:

```
// fetch tbody and row template
var	t = document.querySelector("#mytable tbody"),
	row = document.getElementById("mytablerow");

// modify row data
var td = row.getElementsByTagName("td");
td[0].textContent = "1";
td[1].textContent = "SitePoint";
td[2].textContent = "sitepointdotcom";

// clone row and insert into table
t.appendChild(row.content.cloneNode(true)); 
```

重要问题:*我们可以使用模板标签吗？*

可能还不是时候。它在最新版本的 Chrome 和 Firefox nightlies 中得到支持。Opera 在切换到[闪烁](https://www.sitepoint.com/blink-rendering-engine-google-chrome/)时会支持标签。IE 和 Safari 团队还没有消息，但是如果你想支持所有的浏览器，JSfiddle 上已经展示了一个[模板垫片。](https://jsfiddle.net/brianblakely/h3EmY/)

我个人认为`template`标签是个好主意。它很简单，并且为 HTML5 开发人员标准化了模板技术。

## 分享这篇文章