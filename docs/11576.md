# 在 JavaScript 中修复对象实例

> 原文：<https://www.sitepoint.com/javascript-object-instances/>

![JavaScript objects](img/bc0483f14dafe0856a7e71fba1db8417.png)你了解你的 JavaScript 吗？看一下下面的代码示例，并找出最终的警报语句中显示了什么值…

```
 // object constructor
function ObjectConstructor(a, b, c) {

	this.A = a;
	this.B = b;
	this.C = c;
	this.Total = a + b + c;

}

var obj = ObjectConstructor(1, 2, 3);

alert(obj.Total); 
```

回答“6”的人请举手

对不起，你错了。答案是……什么都没有——或者是一个错误，说明‘obj’没有定义。那么到底哪里出了问题？

简单的回答是，我们忘记了“new”操作符，所以永远不会创建对象实例。声明应该是:

```
 var obj = new ObjectConstructor(1, 2, 3); 
```

这是一个容易犯的错误。开发新手不太可能发现缺失的操作符，因为代码看起来几乎完全相同。即使是有经验的程序员也会发现调试起来很困难(特别是因为许多人认为 JavaScript 是一种[过程化编程语言](http://en.wikipedia.org/wiki/Procedural_language) …如果你选择这样写的话，它可能是这样的)。

主要问题是`var obj = ObjectConstructor(1, 2, 3);`是一个完全有效的 JavaScript 语句，解释器引擎不会抛出错误。在该上下文中，obj 的值被设置为从函数 ObjectConstructor 返回的值；由于没有返回值，obj 保持“未定义”(一个顶级 JavaScript 属性)。

如果您正在开发、测试和调试自己的代码，这不太可能成为主要问题。然而，当你向数以千计的第三方开发者提供一个库或 API 时，情况就不同了。在某个时候，某个地方的某个人会想念那个‘新’操作符，他们会责怪你的代码而不是他们的。

幸运的是，JavaScript 是一种灵活的语言。我们可以修改构造函数，这样即使省略了“new”操作符也能正确创建对象:

```
 // object constructor
function ObjectConstructor(a, b, c) {

	if (!(this instanceof arguments.callee)) {
		return new ObjectConstructor(a, b, c);
	}

	this.A = a;
	this.B = b;
	this.C = c;
	this.Total = a + b + c;

} 
```

构造函数顶部的附加“if”语句检查“this”是否是对象的实例，并在必要时返回一个实例。代码`var obj = ObjectConstructor(1, 2, 3);`现在将 obj 设置为一个对象实例，alert 语句将输出“6”。

你在你的代码中或者使用其他库的时候遇到过这个问题吗？

## 分享这篇文章