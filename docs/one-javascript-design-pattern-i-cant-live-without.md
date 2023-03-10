# 一个我不能没有的 JavaScript 设计模式

> 原文：<https://www.sitepoint.com/one-javascript-design-pattern-i-cant-live-without/>

我想看看我经常使用的 JavaScript 设计模式可能会很有趣。在一段时间内，我逐渐选定了它，吸收和适应来自各种来源的影响，直到达到一个提供我所需要的灵活性的模式。

让我向您展示一个概述，然后看看它是如何组合在一起的:

```
function MyScript(){} (function() { var THIS = this; function defined(x) { return typeof x != 'undefined'; } this.ready = false; this.init = function() { this.ready = true; }; this.doSomething = function() { }; var options = { x : 123, y : 'abc' }; this.define = function(key, value) { if(defined(options[key])) { options[key] = value; } }; }).apply(MyScript);
```

从示例代码中可以看出，整体结构是一个函数文字:

```
(function() { ... })();[/xml]
A function literal is essentially a self-executing scope, equivalent to defining a named function and then calling it immediately:
[xml]function doSomething() { ... } doSomething();
```

我最初使用函数文字是为了封装——任何格式的任何脚本都可以包装在这个外壳中，它有效地将它“密封”在一个私有范围内，防止它与同一范围内的其他脚本或全局范围内的数据发生冲突。最末端的括号对执行作用域，就像调用其他函数一样调用它。

但是，如果不只是全局调用，而是使用 [`Function.apply`](http://www.devguru.com/technologies/ecmascript/quickref/apply.html) 来执行作用域，那么它可以在一个特定的**中执行，命名为作用域**，然后可以在外部引用。

因此，通过将这两者结合在一起——创建一个命名函数，然后在命名函数的范围内执行一个函数文字——我们最终得到一个可以构成任何脚本基础的一次性对象，同时模拟面向对象类中的继承类型。

## 内在的美

查看第一个代码示例，您可以看到封闭范围结构提供了多大的灵活性。当然，在任何函数中没有什么是你做不到的，但是通过这样包装，我们有了一个可以与任何命名作用域相关联的构造。

我们可以创建多个这样的构造，并将它们都关联到同一个作用域，然后它们将彼此共享它们的公共数据。

但是在共享公共数据的同时，每个人也可以定义自己的私有数据。例如，在脚本的最顶端:

```
var THIS = this;
```

我们已经创建了一个名为`THIS`的私有变量，它指向函数作用域，并且可以在私有函数中使用来引用它——与使用`"self = this"`创建内部作用域的引用完全相同。

以同样方式声明的其他私有变量，如果它们定义了常量数据，可以共享大写约定(但是应该避免使用`const`而不是`var`来声明，因为它没有得到很好的支持)。

私有函数可用于提供内部实用程序:

```
function defined(x) { return typeof x != 'undefined'; }
```

然后，我们可以创建公共方法和属性，供其他实例和外部访问:

```
this.ready = false; this.init = function() { this.ready = true; }; this.doSomething = function() { };
```

我们还可以创建特权值——这些值是私有的，但可以公开定义，在这里是通过 public `define`方法；可以根据数据的需要进一步验证其论点:

```
var options = { x : 123, y : 'abc' }; this.define = function(key, value) { if(defined(options[key])) { options[key] = value; } };
```

## 包好了！

所有这些特性使得这个结构对我如此有用。这一切都被包装在一个整洁的、自我执行的单例中——一个易于引用和集成、使用简单的一次性对象！

你觉得怎么样？这是你熟悉的模式吗，还是你有其他喜欢用的模式？

*缩略图鸣谢:[超级金博](http://www.flickr.com/photos/superkimbo/3121815917/)*

## 分享这篇文章