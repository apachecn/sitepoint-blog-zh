# JavaScript 模板引擎概述

> 原文：<https://www.sitepoint.com/overview-javascript-templating-engines/>

*这篇文章由[克里斯·佩里](https://www.sitepoint.com/author/cperry/)和[里泰什·库马尔](https://www.sitepoint.com/author/rkumar/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在这篇文章中，我们将对 JavaScript 中的模板化做一个概述。我们将首先讨论什么是 JavaScript 模板，什么时候我们应该使用它们，以及我们如何实现它们，然后再详细讨论一些流行的模板引擎。我们将重点关注[小胡子](https://github.com/janl/mustache.js/)、[车把](http://handlebarsjs.com/)和 [jQuery 模板](http://codepb.github.io/jquery-template/)。

## 什么是 JavaScript 模板？

JavaScript 模板是一种将 HTML 结构与其中包含的内容分离的方法。模板系统通常会引入一些新的语法，但通常使用起来非常简单，尤其是如果我们以前在其他地方使用过模板系统的话(例如 PHP 中的 [Twig](http://twig.sensiolabs.org/) )。需要注意的一个有趣的点是，令牌替换通常由双花括号(`{{ ... }}`)来表示，小胡子和车把的名称就是由此而来的(提示:将它们转向一侧以查看相似性)。

## 我们什么时候应该使用 JavaScript 模板？

一旦我们发现自己在 JavaScript 字符串中包含 HTML，我们就应该开始考虑 JavaScript 模板能给我们带来什么好处。当构建一个可维护的代码库时，关注点分离是最重要的，所以任何可以帮助我们实现这一点的东西都应该被探索。在前端 web 开发中，这集中体现在将 HTML 从 JavaScript 中分离出来(这是双向的，因为我们不应该将 JavaScript 内联到 HTML 中)。

受益于 JavaScript 模板的一些常见场景是实时 web 应用程序(例如用于事件评论的直播应用程序)或国际化(i18n)，这通常需要使用相同的格式显示不同的内容。

## 我们如何实现 JavaScript 模板？

我们将通过具体的库示例对此进行更详细的介绍，但本质上它就像包含我们选择的库、获取我们的模板并将其与一些数据一起呈现一样简单。

大多数库支持内联和外部模板。当我们只有很少的模板或者我们知道我们将在每次页面加载时使用包含的模板时，内联模板非常有用，但是通常我们的模板应该是外部的。外部模板有很多好处，主要是模板永远不会被下载到客户端，除非页面需要它们。

## mustache.js

Mustache 是一个多语言、无逻辑的模板系统。mustache.js 实现只是众多实现之一。所以一旦我们习惯了(非常简单的)语法，我们就可以在各种编程语言中使用它。

### 要点

*   9kb 文件大小(小)
*   简单的
*   没有依赖关系
*   没有逻辑
*   没有预编译模板
*   编程语言不可知

### 例子

```
<script id="template" type="x-tmpl-mustache"> <p>Use the <strong>{{power}}</strong>, {{name}}!</p> </script> 
```

```
 //Grab the inline template
var template = document.getElementById('template').innerHTML;

//Parse it (optional, only necessary if template is to be used again)
Mustache.parse(template);

//Render the data into the template
var rendered = Mustache.render(template, {name: "Luke", power: "force"});

//Overwrite the contents of #target with the rendered HTML
document.getElementById('target').innerHTML = rendered; 
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Mustache.js 示例](http://codepen.io/SitePoint/pen/YyRxdP/)。