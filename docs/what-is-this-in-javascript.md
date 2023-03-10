# JavaScript 中的‘this’是什么？

> 原文：<https://www.sitepoint.com/what-is-this-in-javascript/>

JavaScript 是一种很棒的编程语言。几年前，这可能是一个有争议的说法，但开发商已经重新发现了它的美丽和优雅。如果你不喜欢 JavaScript，可能是因为:

*   您遇到过浏览器 API 差异或问题——这并不是 JavaScript 的错。
*   你将它与基于类的语言如 C++、C#或 Java 相比较——而 JavaScript 并不像你预期的那样运行。

最令人困惑的概念之一是“this”关键字。在大多数语言中，“this”是对由类实例化的当前对象的引用。在 JavaScript 中，“this”通常指的是“拥有”该方法的对象，但这取决于如何调用函数。

## 全球范围

如果没有当前对象，“this”指的是全局对象。在网络浏览器中，这是“窗口”——顶层对象，代表文档、位置、历史和一些其他有用的属性和方法。

```
 window.WhoAmI = "I'm the window object";
alert(window.WhoAmI);
alert(this.WhoAmI); // I'm the window object
alert(window === this); // true 
```

## 调用函数

如果你调用一个函数，this 仍然是全局对象:

```
 window.WhoAmI = "I'm the window object";

function TestThis() {
	alert(this.WhoAmI); // I'm the window object
	alert(window === this); // true
}

TestThis(); 
```

## 调用对象方法

当调用对象构造函数或它的任何方法时，“this”指的是对象的实例——很像任何基于类的语言:

```
 window.WhoAmI = "I'm the window object";

function Test() {

	this.WhoAmI = "I'm the Test object";

	this.Check1 = function() {
		alert(this.WhoAmI); // I'm the Test object
	};

}

Test.prototype.Check2 = function() {
	alert(this.WhoAmI); // I'm the Test object
};

var t = new Test();
t.Check1();
t.Check2(); 
```

## 使用调用或应用

本质上，调用和应用运行 JavaScript 函数就像它们是另一个对象的方法一样。一个简单的例子进一步说明了这一点:

```
 function SetType(type) {
	this.WhoAmI = "I'm the "+type+" object";
}

var newObject = {};
SetType.call(newObject, "newObject");
alert(newObject.WhoAmI); // I'm the newObject object

var new2 = {};
SetType.apply(new2, ["new2"]);
alert(new2.WhoAmI); // I'm the new2 object 
```

唯一的区别是“call”需要离散数量的参数，而“apply”可以传递一个参数数组。

简而言之就是“这个”！然而，有几个陷阱可能会让你措手不及。我们将在我的下一篇文章中讨论这些…

## 分享这篇文章