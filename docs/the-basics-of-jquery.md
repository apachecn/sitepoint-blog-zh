# jQuery 的基础知识

> 原文：<https://www.sitepoint.com/the-basics-of-jquery/>

说到客户端库， [jQuery](http://jquery.com/ "jQuery: The Write Less, Do More, JavaScript Library") 显然独占鳌头。最近的研究表明 [jQuery 在一半的网站上使用](https://www.sitepoint.com/jquery-used-on-50-percent-of-websites/ "jQuery Is Used on Half of All Websites - SitePoint")。jQuery 的流行有一个很好的解释——它使用起来非常简单。本文介绍了 jQuery 背后的关键概念，将帮助绝对初学者入门并开始运行。

## 背景

jQuery 是由 John Resig 于 2006 年 1 月发布的。jQuery 通过在本地浏览器 API 之上提供一个抽象层，简化了跨浏览器 JavaScript 编程。特别是，通过允许使用 CSS 选择器样式语法进行 DOM 查询，简化了 DOM 导航。这在支持[选择器 API](http://cjihrig.com/blog/javascripts-selectors-api/ "JavaScript’s Selectors API") 的现代浏览器中并不重要，但是 IE6 等老浏览器可以从 jQuery 中受益匪浅。jQuery 还提供了许多其他流行特性的抽象，比如 AJAX 和动画。

## 获取 jQuery

jQuery 只是一个普通的 JavaScript 文件，所以您可以使用标准的`<script>`标签来包含它。jQuery 可以包含在项目页面中，也可以包含在各种内容交付网络(T2)中。下面的例子展示了 jQuery 如何包含在任何 HTML 文档中。

```
<script src="https://code.jquery.com/jquery-1.8.3.min.js"></script>
```

如果你想让你的网站完全独立，我建议你下载代码并把它放在你自己的服务器上。jQuery 可以在项目主页上免费下载。该代码在生产(缩小)和开发版本中都可以方便地获得。

## `$()`功能

对于新的 jQuery 开发人员来说，最突出的一点是代码中美元符号的扩散。默认情况下，jQuery 将其所有方法和属性命名为`$`变量。最流行的方法是`[jQuery()](http://api.jquery.com/jQuery/ "jQuery() - jQuery API")`，通常别名为`$()`。这个方法可以以多种方式使用，但通常它接受一个 CSS 样式选择器字符串，用于查询 DOM。任何匹配的节点都作为 jQuery 对象的集合返回。

以下示例选择类别`foo`的所有超链接。然后使用`[each()](http://api.jquery.com/jQuery.each/ "jQuery.each() - jQuery API")`函数遍历这些链接，一次遍历一个。`each()`，以一个回调函数作为参数。随着每个匹配元素被迭代，回调函数被调用，其中`this`引用当前元素。然后使用`[attr()](http://api.jquery.com/attr/ ".attr() - jQuery API")`函数从每个链接中检索`href`属性。

```
$("a.foo").each(function() {
  var link = $(this);
  var href = link.attr("href");
  // Use link here
});
```

## `ready()`功能

jQuery 也非常擅长抽象出事件。并且，可能没有比页面加载事件实现更不一致的事件了。例如，一些浏览器只支持窗口的`load`事件，该事件只有在所有东西都加载后才会触发，包括图像。更好的浏览器还支持`DOMContentLoaded`事件，一旦 DOM 被解析就触发该事件，而无需等待图像、样式表和其他资源。

使用 jQuery，您不必试图区分各种页面加载事件。相反，jQuery 提供了`[ready()](http://api.jquery.com/ready/ ".ready() - jQuery API")`函数。`ready()`接受单个参数，这个函数在 DOM 完全加载后执行。下面的例子展示了如何使用`ready()`来响应页面加载事件。

```
$(document).ready(function(){
  // Explore jQuery here
});
```

## 结论

本文探讨了 jQuery 的基础知识。当然，这只是冰山一角。jQuery 的大受欢迎已经催生了一个由书籍、开发者、插件和工具组成的完整生态系统。然而，我必须对任何潜在的 jQuery 开发人员提出警告。因为 jQuery 让复杂的任务变得如此简单，所以许多开发人员从来不会费心去了解事情实际上是如何工作的。这会导致一种错误的理解感，这对程序员来说从来都不是一件好事。我鼓励你通读 jQuery 源代码-[你永远不知道你会学到什么](http://paulirish.com/2010/10-things-i-learned-from-the-jquery-source/ "10 Things I Learned from the jQuery Source - Paul Irish")。

## 分享这篇文章