# 关于 JavaScript 的 5 个常见误区

> 原文：<https://www.sitepoint.com/5-common-javascript-myths/>

尽管是世界上使用最广泛的编程语言，JavaScript 却是最容易被误解和低估的。这种情况在过去几年中有所改善，但是这些神话仍然充斥着 IT 世界。

## 1.JavaScript == Java

英国开发者杰里米·基思设计了最佳解释:

> Java 对于 JavaScript 就像 ham 对于仓鼠一样

这些名字非常相似，但是 Java 和 JavaScript 没有关系。表面上有许多相似之处，但是对于采用类似 C 的语法的任何语言来说都是一样的。

这种语言最初被命名为 Mocha，后来成为 LiveScript，最后成为 JavaScript，当时 Java 被誉为生命、宇宙和万物的救世主。这个名字反映了网景公司的营销努力，而不是任何潜在的关系。

## 2.JavaScript 是一种“玩具”语言

名称中的*“script”*部分给人的印象是 JavaScript 是某种精简的、类似宏的或功能较弱的 Java 版本。不是的。如果说有什么不同的话，流行的 JavaScript 特性，比如闭包和 lambda 函数，现在正在进入其他语言(PHP、C#和 Java 本身)。

JavaScript 是最常见的基于浏览器的解释语言，但这并不意味着它比基于操作系统的编译语言更强大或更有竞争力。

## 3.JavaScript 只能在浏览器中找到

大多数人认为 JavaScript 是一种嵌入式浏览器语言，但是它(或基于 ECMAScript 标准的语言)可以在许多其他地方找到，例如

*   Mozilla 的 Firefox 浏览器和雷鸟电子邮件客户端在一些应用程序进程中使用 JavaScript
*   Flash ActionScript 基于 JavaScript
*   PDF 文件可以嵌入 JavaScript
*   许多微软和苹果的桌面小工具都使用 JavaScript
*   OpenOffice.org 提供 JavaScript 支持
*   Palm mobiles 中使用的 webOS 使用 JavaScript 作为其应用程序框架
*   JavaScript 可以在 GNOME 桌面上用作应用程序编程语言，也可以在 Windows 中用作脚本语言。

JavaScript 没有强大的服务器端存在，但是有几十个实现。我们能够在客户机和服务器上开发 JavaScript 的那一天已经迫不及待了！

## 4.JavaScript 不一致且错误百出

那些日子早已过去。浏览器供应商遵循一个公认的标准(ECMAScript ), JavaScript 特有的问题很少出现。

然而，浏览器的特性并不总是一致的:DOM 操作的特殊性、事件处理的特质和浏览器的怪癖都会引起开发上的麻烦。这不是语言本身的问题，而是可以用 JavaScript 访问的浏览器对象的问题。

## 5.JavaScript 不是面向对象的

出现混乱是因为 JavaScript 没有提供明显的面向类的继承。缺少“class”关键字导致许多开发人员认为 JavaScript 是一种函数式语言。

JavaScript 提供了原型继承。真正的美妙之处在于，您可以编写函数式语言代码，或者使用带有私有属性和方法的经典继承模式。

此外，JavaScript 中的所有东西都是对象，包括本地类型和函数。有多少其他语言提供了这样的结构:

```
 var x = new Number(123);
var opp = "oops".substr(0,3);
var f = function() { return function() { alert("hello!"); }; }; 
```

如何将函数作为参数传递(它只是另一个对象)…

```
 var Add = function(a, b) { return a + b; };

function Calculate(func, a, b) {
	return func(a, b);
}

var s = Calculate(Add, 1, 2); // s = 3 
```

或者扩展原生类型…

```
 // reverse any string
String.prototype.Reverse = function() {
	return this.split("").reverse().join("");
};

var h1 = "Hello!";
var h2 = h1.Reverse(); // !olleH 
```

JavaScript 一开始可能有点令人困惑，但是当你回到其他语言时，你会怀念它的优雅和强大。

还是不相信我？你报名参加了凯文·杨克的 JavaScript Live T2 课程 T3 了吗？我们论坛上见。

## 分享这篇文章