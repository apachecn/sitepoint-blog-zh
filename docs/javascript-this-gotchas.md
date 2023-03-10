# JavaScript“这”抓到你了

> 原文：<https://www.sitepoint.com/javascript-this-gotchas/>

在我的[上一篇文章](https://www.sitepoint.com/what-is-this-in-javascript)中，我们研究了 JavaScript 的`this`语句，以及它如何根据函数调用的上下文而变化。今天，我们将考察几种`this`可能会让你措手不及的情况…

## 1.遗忘“新”

考虑以下代码:

```
 window.WhoAmI = "I'm the window object";

function Test() {
  this.WhoAmI = "I'm the Test object";
}

var t = Test();
alert(window.WhoAmI); // I'm the Test object
alert(t.WhoAmI); // t is undefined 
```

我们真正的意思是:

```
 var t = new Test(); 
```

省略`new`语句给了我们不希望的结果。其他语言在面对对构造函数的直接调用时会抛出一个错误，但是 JavaScript 只是像对待其他函数调用一样对待它。`this`被当作全局窗口对象，`Test()`没有返回值，所以`t`变成了`undefined`。

如果您正在为第三方开发人员编写 JavaScript 库，这种情况是可以解决的。参考 JavaScript 中的[修复对象实例。](https://www.sitepoint.com/javascript-object-instances/)

## 2.模块疯狂

这个会让你头疼。检查以下使用模块模式的代码:

```
 window.WhoAmI = "I'm the window object";

var Module = function() {

  this.WhoAmI = "I'm the Module object";

  function Test() {
    this.WhoAmI = "I'm still the Module object";
  }

  return {
    WhoAmI: WhoAmI,
    Test: Test
  };

}();

alert(Module.WhoAmI); // I'm the Module object
alert(window.WhoAmI); // I'm the Module object
Module.Test();
alert(Module.WhoAmI); // I'm still the Module object 
```

代码看起来很有逻辑——那么为什么`window.WhoAmI`说它是模块对象呢？

我们需要记住，我们有一个自我执行的功能。它的结果被返回到`Module`变量，但是，当它第一次运行时，模块并不存在。`this`因此是全局窗口对象。换句话说，`this.WhoAmI` = `window.WhoAmI` = `"I'm the Module object"`。

该函数返回一个带有值为`'WhoAmI'`的`WhoAmI`属性的 JavaScript 对象。但是那指的是什么呢？在这种情况下，JavaScript 解释器处理它的原型链，直到它神奇地找到`window.WhoAmI` ( `"I'm the Module object"`)。

最后，我们运行`Test()`方法。然而，`Module`现在已经被创建了，所以在`Test`函数中，`this`引用了`Module`对象，因此它可以正确地设置`WhoAmI`属性。

总之，避免在模块中使用`this`来引用模块本身！你不应该需要它。

## 3.方法误解

这里有另一个 JavaScript 模式，它会搅乱你的神经突触:

```
 var myObject = {};

myObject.method = function() {

  this.WhoAmI = "I'm myObject.method";

  function Test() {
    this.WhoAmI = "I'm myObject.method.Test()";
  }

  Test();

  return this.WhoAmI;
};

alert(myObject.method()); // I'm myObject.method 
```

在本例中，`Test()`是在`myObject.method()`中执行的私有函数。乍一看，你会认为`Test()`中的`this`指的是`myObject`。它没有:它引用全局窗口对象，因为它只是另一个函数。

如果你想在私有函数中引用`myObject`，你需要一个闭包，例如:

```
 var myObject = {};

myObject.method = function() {

  this.WhoAmI = "I'm myObject.method";
  var T = this;

  function Test() {
    T.WhoAmI = "I'm myObject.method.Test()";
  }

  Test();

  return this.WhoAmI;
};

alert(myObject.method()); // I'm myObject.method.Test() 
```

## 4.引用方法

幸运的是，这里有一小段代码可以完全按照您的预期运行:

```
 var myObject = {};

myObject.WhoAmI = "I'm myObject";

myObject.method = function() {
  this.WhoAmI = "I'm myObject.method";
};

// examine properties
alert(myObject.WhoAmI); // I'm myObject
myObject.method();
alert(myObject.WhoAmI); // I'm myObject.method 
```

让我们做一个小改动，将`myObject.method`赋给另一个变量:

```
 // examine properties
alert(myObject.WhoAmI); // I'm myObject
var test = myObject.method;
test();
alert(myObject.WhoAmI); // I'm myObject 
```

为什么`myObject.WhoAmI`没有改变？在这种情况下，对`test()`的调用就像一个常规的函数调用，所以`this`指的是窗口对象而不是`myObject`。

如果你认为这很糟糕，那就等着看我下一篇文章中的 JavaScript 事件处理程序吧！

**note:** Want more?

如果你想阅读更多 Craig 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章