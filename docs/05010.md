# 5 个典型的 JavaScript 面试练习

> 原文：<https://www.sitepoint.com/5-typical-javascript-interview-exercises/>

IT 界对 JavaScript 开发者的需求很大。如果这是最能表达你知识的角色，你有很多机会改变你工作的公司，增加你的薪水。但是在你被一家公司雇用之前，你必须展示你的技能才能通过面试。在本文中，我将向您展示五个典型的前端工作问题，以测试候选人的 JavaScript 技能及其相关解决方案。会很好玩的！

## 问题 1:范围

考虑以下代码:

```
(function() {
   var a = b = 5;
})();

console.log(b);
```

控制台上会打印什么？

### 回答

上面的代码打印了`5`。

这个问题的诀窍在于，在生命中有两个赋值，但是变量`a`是用关键字`var`声明的。这意味着`a`是函数的一个局部变量。相反，`b`被分配到全局范围。

这个问题的另一个诀窍是，它没有在函数内部使用*严格模式* ( `'use strict';`)。如果[严格模式](http://cjihrig.com/blog/javascripts-strict-mode-and-why-you-should-use-it/)被启用，代码将产生错误<q>未捕获引用错误:b 未定义</q>。请记住，严格模式要求您显式引用全局范围，如果这是预期的行为。所以，你应该写:

```
(function() {
   'use strict';
   var a = window.b = 5;
})();

console.log(b);
```

## 问题 2:创建“本地”方法

在`String`对象上定义一个`repeatify`函数。该函数接受一个整数，该整数指定字符串必须重复多少次。该函数返回重复指定次数的字符串。例如:

```
console.log('hello'.repeatify(3));
```

应打印`hellohellohello`。

### 回答

一个可能的实现如下所示:

```
String.prototype.repeatify = String.prototype.repeatify || function(times) {
   var str = '';

   for (var i = 0; i < times; i++) {
      str += this;
   }

   return str;
};
```

这个问题测试了开发人员关于 JavaScript 和`prototype`属性中的继承的知识。它还验证了开发人员能够扩展本机数据类型功能(尽管不应该这样做)。

这里的另一个要点是证明您知道如何不覆盖可能已经定义的函数。这是通过在定义自己的函数之前测试该函数不存在来实现的:

```
String.prototype.repeatify = String.prototype.repeatify || function(times) {/* code here */};
```

当您被要求填充一个 JavaScript 函数时，这种技术特别有用。

## 问题 3:吊装

执行这段代码的结果是什么，为什么。

```
function test() {
   console.log(a);
   console.log(foo());

   var a = 1;
   function foo() {
      return 2;
   }
}

test();
```

### 回答

这段代码的结果是`undefined`和`2`。

原因是变量和函数都被[提升](https://www.sitepoint.com/back-to-basics-javascript-hoisting/)(移动到函数的顶部)，但是变量不保留任何赋值。因此，在打印变量`a`时，它存在于函数中(它被声明了),但它仍然是`undefined`。换句话说，上面的代码相当于下面的代码:

```
function test() {
   var a;
   function foo() {
      return 2;
   }

   console.log(a);
   console.log(foo());

   a = 1;
}

test();
```

## 问题 4:`this`如何在 JavaScript 中工作

以下代码的结果是什么？解释一下你的答案。

```
var fullname = 'John Doe';
var obj = {
   fullname: 'Colin Ihrig',
   prop: {
      fullname: 'Aurelio De Rosa',
      getFullname: function() {
         return this.fullname;
      }
   }
};

console.log(obj.prop.getFullname());

var test = obj.prop.getFullname;

console.log(test());
```

### 回答

代码打印出`Aurelio De Rosa`和`John Doe`。原因是 JavaScript 中函数的上下文，即`this`关键字所指的内容，取决于函数是如何被调用的，而不是如何被定义的。

在第一个`console.log()`调用中，`getFullname()`作为`obj.prop`对象的函数被调用。因此，上下文引用后者，函数返回该对象的`fullname`属性。相反，当`getFullname()`赋给`test`变量时，上下文指的是全局对象(`window`)。这是因为`test`被隐式设置为全局对象的属性。因此，该函数返回一个名为`window`的`fullname`属性的值，在本例中，它就是代码片段第一行中的代码集。

## 问题 5: `call()`和`apply()`

修复前一个问题的问题，以便最后一个`console.log()`打印`Aurelio De Rosa`。

### 回答

这个问题可以通过使用`call()`或`apply()`函数强制函数的上下文来解决。如果你不知道它们和它们的区别，我建议你去读一读[这篇文章 function.call 和 function.apply 有什么区别？](https://www.sitepoint.com/whats-the-difference-between-function-call-and-function-apply/)。在下面的代码中，我将使用`call()`，但在这种情况下`apply()`会产生相同的结果:

```
console.log(test.call(obj.prop));
```

## 结论

在本文中，我们讨论了测试 JavaScript 开发人员的五个典型面试问题。实际的问题可能会因面试而异，但是概念和话题通常是非常相似的。我希望你在测试你的知识时玩得开心。如果你不知道所有答案中的一些，不要担心:没有什么是学习和经验不能解决的。

如果你在面试中被问到一些其他有趣的问题，请不要犹豫，与我们分享。这会对很多开发者有帮助。

## 分享这篇文章