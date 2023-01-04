# 服务器端 JavaScript 会流行起来吗？

> 原文：<https://www.sitepoint.com/server-side-javascript/>

JavaScript 可能是这个星球上使用最广泛的编程语言——几乎每个网站都有几行代码。然而，这种语言也是最容易被误解的语言之一，经常让有经验的开发人员感到困惑:它不是 Java，它不是“脚本”，它不像 T2 那样支持面向对象的编程，让代码在一个浏览器中工作也有问题——更不用说所有的浏览器了。

web 开发人员经历的许多痛苦很少是由 JavaScript 本身引起的；DOM 操作、浏览器怪癖以及直到最近缺乏好的开发工具和调试器是抱怨的最大原因。然而，Ajax 和 Web2.0 的兴起让许多开发人员“重新发现”了这种语言:它可能并不完美，但它功能强大，提供了引人注目的特性，如原型、一流的函数、闭包和对象文字。

虽然 JavaScript 引擎在除了最基本的 web 浏览器之外的所有浏览器中都可用，但它在 web 服务器上的使用从未变得广泛。JavaScript 的服务器实现有[几种，但是与](http://en.wikipedia.org/wiki/Server-side_JavaScript) [PHP](http://www.php.net/) 、[ASP.NET](http://www.asp.net/)甚至 [Ruby](http://rubyonrails.org/) 相比，没有一种可以宣称已经成为主流。

然而，服务器端 JavaScript 确实提供了一些诱人的可能性:

*   这是一种少学少用的语言。Web 开发通常涉及一系列技术，而服务器端 JavaScript 会使这个过程变得更容易(有多少次你开始在 JavaScript 中输入服务器端代码，或者相反)?)
*   可以在客户端和服务器端使用相同的代码，例如，可以使用相同的方法验证表单字段。
*   JavaScript 库如 [jQuery](http://jquery.com/) 可以在服务器上工作。诸如服务器端 DOM 操作之类的特性也应该是可能的。
*   Web 服务和 Ajax 开发会更容易，例如 JSON 可以在任何一端本地处理。
*   知识可以在服务器和客户端 web 开发专家之间共享。

JavaScript 是适合服务器环境的语言吗？像 [Jaxer](http://www.aptana.com/jaxer) 这样的项目能成功吗？你现在使用它还是计划在未来的项目中使用它？更多的 ISP 应该在他们的托管计划中提供服务器端 JavaScript 吗？

## 分享这篇文章