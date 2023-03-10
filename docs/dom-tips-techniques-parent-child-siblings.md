# DOM 技巧和技术:父、子和兄弟

> 原文：<https://www.sitepoint.com/dom-tips-techniques-parent-child-siblings/>

复杂的、大量标记的 web 应用程序是当今的标准。像 jQuery 这样的库具有易用性、跨浏览器兼容性和各种特性，在动态操作 HTML 时会有很大帮助。因此，难怪许多开发人员选择使用这样的库，而不是本地 DOM APIss，因为本地 DOM API 在历史上一直是个大问题。尽管浏览器的差异仍然是一个问题，但是今天的 DOM 比 5、6 年前 jQuery 刚刚起步时的[要好得多。](http://ejohn.org/blog/the-dom-is-a-mess/)

在这篇文章中，我将讨论和演示一些不同的 DOM 特性，它们可以用来操作 HTML，特别关注父节点、子节点和兄弟节点的关系。

我将在结论中介绍浏览器支持，但是请记住，像 jQuery 这样的库通常仍然被认为是一个不错的选择，因为在使用这些种类的本机特性时会出现错误和不一致。

## 计数子节点

让我们使用下面的 HTML，在整篇文章中，我会以各种形式重复使用它:

```
<body>
  <ul id="myList">
    <li>Example one</li>
    <li>Example two</li>
    <li>Example three</li>
    <li>Example four</li>
    <li>Example five</li>
    <li>Example Six</li>
  </ul>
</body>
```

如果我想计算`<ul>`元素中有多少个元素，有两种方法可以做到。

```
var myList = document.getElementById('myList');

console.log(myList.children.length); // 6
console.log(myList.childElementCount); // 6
```

在 [CodePen](http://codepen.io) 上看到笔[计数子元素:children&childElementCount](http://codepen.io/SitePoint/pen/XmOvbK/)by site point([@ site point](http://codepen.io/SitePoint))。