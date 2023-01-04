# 用 JavaScript 或 jQuery 访问父元素

> 原文：<https://www.sitepoint.com/access-parent-element-javascript-jquery/>

通常，您会希望 JavaScript 函数访问 DOM 中的父元素。

要在 JavaScript 中实现这一点，请尝试`element.parentNode`。

要在 jQuery 中做同样的事情，请尝试`element.parent()`。

这里有一个 JavaScript 例子。

HTML:

```
<ul id="tabs">
<li class="firsttab"><a href="#">one</a></li>
<li class="secondtab"><a href="#">two</a></li>
</ul>
```

JavaScript:

```
function init() {
  var tablinks = document.getElementById('tabs').getElementsByTagName('a');
  for (var i = 0, j = tablinks.length; i < j; i++) {
    tablinks[i].onclick = doit;
  }
}
function doit() {
  alert(this.parentNode.className);
}
window.onload = init;
```

## 分享这篇文章