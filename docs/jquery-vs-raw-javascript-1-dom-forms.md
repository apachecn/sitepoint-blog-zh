# jQuery 方法的本地 JavaScript 等价物:DOM 和表单

> 原文：<https://www.sitepoint.com/jquery-vs-raw-javascript-1-dom-forms/>

争论还在我最近的 [*上肆虐，你真的需要 jQuery 吗？*](/do-you-really-need-jquery/) 文章但是，总而言之，使用 jQuery 是个好主意有两个原因:

1.  您需要支持 IE6/7/8(记住您不能迁移到 jQuery 2.0)，或者
2.  如果没有 jQuery，编写类似 jQuery 的库要比开发应用程序花费更多的时间。

对于其他任何事情，都要务实。jQuery 是一个 270Kb 的通用库。您不太可能需要它提供的所有功能，即使您省略了某些模块，它仍然保留了相当数量的代码。你可以从 CDN 加载 30Kb 的精简版本，但是浏览器必须在做任何事情之前停止处理并解析每一页的代码。

这是展示常见 jQuery 方法的原生 JavaScript 等价物的系列文章中的第一篇。虽然您可能希望将其中一些封装在更短的类似别名的函数中，但是您肯定不需要创建自己的类似 jQuery 的库。

## DOM 选择器

jQuery 允许使用 CSS 选择器语法选择 DOM 节点，例如

```
// find all paragraphs with the class "summary" in the article with ID "first"
var n = $("article#first p.summary"); 
```

本机等效项:

```
var n = document.querySelectorAll("article#first p.summary");
```

`document.querySelectorAll`在所有现代浏览器和 IE8 中都实现了(虽然那只支持 CSS2.1 选择器)。jQuery 对更高级的选择器有额外的支持，但是在大多数情况下，它将在`$()`包装器中运行`document.querySelectorAll`。

原生 JavaScript 还提供了四种选择，如果您可以使用的话，它们几乎肯定会比 querySelectorAll 快:

1.  `document.querySelector(selector)` —仅获取第一个匹配的节点
2.  `document.getElementById(idname)` —按 ID 名称提取单个节点
3.  `document.getElementsByTagName(tagname)` —获取与元素匹配的节点(如 h1、p、strong 等)。
4.  `document.getElementsByClassName(class)` —获取具有特定类名的节点

getElementsByTagName 和 getElementsByClassName 方法也可以应用于单个节点，以将结果限制为仅后代

```
var n = document.getElementById("first");
var p = n.getElementsByTagName("p");
```

让我们做一些测试。我编写了一小部分脚本来从我的 [*中检索所有的注释节点，你真的需要 jQuery 吗？*](/do-you-really-need-jquery/) 万次条。结果是:

| 密码 | 时间 |
| 

```
// jQuery 2.0
var c = $("#comments .comment");
```

 | 4649 毫秒 |
| 

```
// jQuery 2.0
var c = $(".comment");
```

 | 3437 毫秒 |
| 

```
// native querySelectorAll
var c = document.querySelectorAll("#comments .comment");
```

 | 1，362 毫秒 |
| 

```
// native querySelectorAll
var c = document.querySelectorAll(".comment");
```

 | 1，168 毫秒 |
| 

```
// native getElementById / getElementsByClassName
var n = document.getElementById("comments");
var c = n.getElementsByClassName("comment");
```

 | 107 毫秒 |
| 

```
// native getElementsByClassName
var c = document.getElementsByClassName("comment");
```

 | 75 毫秒 |

我不能说严格的实验室条件，它不能反映真实世界的使用情况，但在这种情况下，原生 JavaScript 的速度高达 60 倍。它还说明了通过 ID、标签或类获取节点通常比`querySelectorAll`更好。

## DOM 操作

jQuery 提供了几种向 DOM 添加更多内容的方法，例如

```
$("#container").append("<p>more content</p>");
```

在表面之下，jQuery 使用本机 innerHTML 方法，例如

```
document.getElementById("container").innerHTML += "<p>more content</p>";
```

您也可以使用 DOM 构建技术。这些比 innerHTML 更安全，但很少比 innerHTML 更快:

```
var p = document.createElement("p");
p.appendChild(document.createTextNode("more content");
document.getElementById("container").appendChild(p);
```

我们还可以删除 jQuery 中的所有子节点:

```
$("#container").empty();
```

使用 innerHTML 的本机等效项:

```
document.getElementById("container").innerHTML = null;
```

或者一个小功能:

```
var c = document.getElementById("container");
while (c.lastChild) c.removeChild(c.lastChild);
```

最后，我们可以从 jQuery 的 DOM 中删除整个元素:

```
$("#container").remove();
```

或者原生 JavaScript:

```
var c = document.getElementById("container");
c.parentNode.removeChild(c);
```

## 可缩放矢量图形

核心的 jQuery 库已经被开发来处理当前文档。SVG 也有一个 DOM，但是 jQuery 不提供对这些对象的直接操作，因为通常需要使用像`createElementNS`和`getAttributeNS`这样的方法。它可以工作，并且有几个[插件可用](http://keith-wood.name/svg.html)，但是滚动你自己的代码或者使用专门的 SVG 库比如[拉斐尔](http://raphaeljs.com/)或者 [svg.js](http://svgjs.com/) 会更有效率。

## HTML5 表单

即使是最基本的 web 应用程序也会有一两种形式。您应该总是在服务器端验证用户数据，但是理想情况下，您应该用客户端验证来补充它，以便在提交表单之前捕获错误。

客户端验证非常简单:

1.  提交表单时运行一个函数。
2.  如果遇到任何问题，您将停止提交并显示一个错误。

可以使用 jQuery。可以使用原生 JavaScript。你应该选择哪个？*都不是*。

HTML5 内置了对各种常见输入类型的支持，如电子邮件、电话、URL、数字、时间、日期、颜色和基于正则表达式的自定义字段。例如，如果您想强制用户输入电子邮件地址，请使用:

```
<input type="email" name="email" required="required" />
```

不需要额外的 JavaScript 或 jQuery 代码，除非您需要更复杂的功能，比如比较两个或多个字段或者显示定制的错误消息。

较旧的浏览器(包括 IE9 和更低版本)不理解新的类型，将恢复到标准的文本输入字段。这些用户将退回到服务器端验证；这不是一个很好的体验，但是你可以[应用一个垫片](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-Browser-Polyfills#web-forms)或者希望那些人看到光明并升级。

在我的下一篇文章中，我们将研究原生 CSS 类操作和动画。

## 分享这篇文章