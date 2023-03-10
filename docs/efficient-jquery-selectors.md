# 提高 jQuery 选择器效率的 5 个技巧

> 原文：<https://www.sitepoint.com/efficient-jquery-selectors/>

顾名思义，jQuery 专注于查询。该库的核心允许您使用 CSS 选择器语法查找 DOM 元素，并对该集合运行方法。

jQuery 使用本地浏览器 API 方法来检索 DOM 集合。较新的浏览器支持解析 CSS 语法的 getElementsByClassName、querySelector 和 querySelectorAll。但是，旧版浏览器只提供 getElementById 和 getElementByTagName。在最坏的情况下， [jQuery 的 Sizzle 引擎](http://sizzlejs.com/)必须解析选择器字符串并寻找匹配的元素。

这里有五个技巧可以帮助你优化 jQuery 选择器…

## 1.如果可能，请使用 ID

HTML ID 属性在每个页面中都是唯一的，即使是较旧的浏览器也可以非常快速地找到单个元素:

```
 $("#myelement"); 
```

## 2.避免只按类选择

下面的类选择器将在现代浏览器中快速运行:

```
 $(".myclass"); 
```

不幸的是，在 IE6/7 和 Firefox 2 等较老的浏览器中，jQuery 必须检查页面上的每个元素，以确定是否应用了“myclass”。

如果我们用一个标记名来限定选择器，它会更有效，例如

```
 $("div.myclass"); 
```

jQuery 现在可以将搜索限制在 DIV 元素。

## 3.保持简单！

避免过于复杂的选择器。除非你有一个非常复杂的 HTML 文档，否则你很少需要两到三个以上的限定符。

考虑以下复杂的选择器:

```
 $("body #page:first-child article.main p#intro em"); 
```

p#intro 必须是唯一的，以便简化选择器:

```
 $("p#intro em"); 
```

## 4.从左到右增加特异性

稍微了解一下 jQuery 的选择器引擎是很有用的。它首先从最后一个选择器开始工作，因此，在较旧的浏览器中，诸如:

```
 $("p#intro em"); 
```

将每个 em 元素加载到数组中。然后，它处理每个节点的父节点，拒绝那些找不到 p#intro 的节点。如果页面上有数百个`em`标签，查询效率会特别低。

根据您的文档，可以通过首先检索最合适的选择器来优化查询。然后，它可以用作子选择器的起点，例如

```
 $("em", $("p#intro")); // or
$("p#intro").find("em"); 
```

## 5.避免选择器重复

很少有必要两次使用同一个选择器。以下代码选择每个`p`标签三次:

```
 $("p").css("color", "blue");
$("p").css("font-size", "1.2em");
$("p").text("Text changed!"); 
```

记住 jQuery 提供了链接；多个方法可以应用于同一个集合。因此，可以重写相同的代码，使其适用于单个选择器:

```
 $("p").css({ "color": "blue", "font-size": "1.2em"}).text("Text changed!"); 
```

如果需要多次使用同一组元素，应该在变量中缓存 jQuery 对象，例如

```
 var $p = $("p");
$p.css("color", "blue");
$p.text("Text changed!"); 
```

与标准 DOM 集合不同， [jQuery 集合不是实时的](https://www.sitepoint.com/javascript-vs-jquery-html-collections/)，当段落标签被添加到文档或从文档中删除时，对象不会更新。您可以通过创建一个 DOM 集合并在需要时将其传递给 jQuery 函数来解决这个限制，例如

```
 var p = document.getElementByTagName("p");
$(p).css("color", "blue");
// update the DOM
$(p).text("Text changed!"); 
```

你有任何进一步的 jQuery 选择器优化技巧吗？

## 分享这篇文章