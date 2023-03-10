# QUnit 1.16 的新功能

> 原文：<https://www.sitepoint.com/whats-new-qunit-1-16/>

测试是每个开发人员工作流程中必不可少的一部分，或者至少应该是。几年前的一项调查显示，大约 50%的 JavaScript 开发人员根本不写测试，这有点可怕。几个月前，我试图通过一个关于 JavaScript 单元测试框架 [QUnit](http://qunitjs.com) 的 3 部分系列来鼓励 JavaScript 测试实践。如果你错过了，这个系列是由 QUnit[<cite>入门</cite>](https://www.sitepoint.com/getting-started-qunit/) 、 [<cite>如何用 QUnit</cite>](https://www.sitepoint.com/test-asynchronous-code-qunit/) 和 [<cite>QUnit 高级概念:模块和配置</cite>](https://www.sitepoint.com/qunit-advanced-concepts-modules-configuration/) 文章组成的。

12 月，发布了该框架的 1.16 版本，做了一些重要的修改。在本文中，我将向您介绍它们，并描述它们如何影响您的测试。

## 为 2.0 版做准备

现在将您的测试更新到版本 1.16 将有助于您迁移到版本 2.0。QUnit 1.16 引入了几个新方法，它们将成为下一个里程碑中的默认方法，所以现在就计划是为主要版本做准备的一个明智的方法。此外，正如我们稍后将看到的，在 1.16 版本中，您用来测试异步代码的一些方法已经被弃用，一些属性被重命名，因此您必须了解这些变化。

## 测试异步代码的新方法

直到版本 1.15，为了测试一个异步函数，你通常使用一个不同的方法来定义名为`QUnit.asyncTest()`的测试，以及`QUnit.start()`和`QUnit.stop()`方法。例如，假设您有一个名为`max()`的函数，它从一组给定的数字中计算最大值，在 QUnit 1.15 中，您可以编写如下测试:

```
QUnit.asyncTest('max', function (assert) {
expect(2);
QUnit.stop(1);

window.setTimeout(function() {
assert.strictEqual(max(), -Infinity, 'No parameters');
QUnit.start();
}, 0);

window.setTimeout(function() {
assert.strictEqual(max(3, 1, 2), 3, 'All positive numbers');
QUnit.start();
}, 0);
});
```

从 1.16 版开始，`QUnit.asyncTest()`、`QUnit.start()`和`QUnit.stop()`已被弃用。要定义异步测试，您可以使用与同步测试相同的`QUnit.test()`方法。启动/停止机制已经被一种新的机制所取代，这种机制使用了一种叫做 [`async`](http://api.qunitjs.com/async/) 的新断言方法。后者在每次被调用时返回一个唯一的解析回调，这个回调必须在异步操作中执行，以代替`QUnit.start()`。从`QUnit.async()`方法返回的回调不能被执行两次，因为它会抛出一个错误，所以你不必担心这个。

根据新机制重写先前的测试会产生以下代码:

```
QUnit.test('max', function (assert) {
expect(2);

var done1 = assert.async();
window.setTimeout(function() {
assert.strictEqual(max(), -Infinity, 'No parameters');
done1();
}, 0);

var done2 = assert.async();
window.setTimeout(function() {
assert.strictEqual(max(3, 1, 2), 3, 'All positive numbers');
done2();
}, 0);
});
```

您可以在下面看到最后一个代码片段，它的代码也可以作为 JSFiddle 获得:

[https://jsfiddle.net/2dmvLgLu/embedded/](https://jsfiddle.net/2dmvLgLu/embedded/)

## 支持承诺

`QUnit.test()`现在可以自动处理承诺的异步解析。承诺是一种有趣的模式，在过去的几年中被大量使用，作为一种取代回调和避免所谓的回调地狱 T2 的方式。ECMAScript 6 原生引入了承诺，浏览器也开始实现这一特性。如果你需要一个关于 promise 的介绍，你可以阅读文章[<cite>JavaScript Promises——那里又回来</cite>](http://www.html5rocks.com/en/tutorials/es6/promises/) 。

回到在 QUnit 1.16 中所做的改变，如果你作为回调函数的结果返回一个`then` able Promise，QUnit 将等待测试解决或拒绝。这一新功能的一个例子[摘自官方文档](http://api.qunitjs.com/QUnit.test/)的相关页面，报告如下:

```
QUnit.test( "a Promise-returning test", function( assert ) {
assert.expect( 0 );

var thenable = new Promise(function( resolve, reject ) {
setTimeout(function() {
resolve( "result" );
}, 500 );
});
return thenable;
});
```

## 新方法:`QUnit.skip()`

除了`QUnit.async()`方法，我们还有`QUnit.skip()`。它可用于定义您希望暂时禁用且不得执行的测试。要跳过一个测试，你可以用`QUnit.skip()`代替对`QUnit.test()`的调用。假设你有一个测试:

```
QUnit.test('test', function(assert) {
// code goes here
});
```

您可以将对`QUnit.test()`的调用替换为`QUnit.skip()`，如下所示:

```
QUnit.skip('test', function(assert) {
// code goes here
});
```

这个简单的改变使您能够避免注释整个测试。跳过的测试仍然显示在 HTML 报告程序中，但被标记为“跳过”。

## 定义安装和拆卸功能的新属性

QUnit 允许通过将代码分成多个模块来组织我们的测试，这在我们为大型项目编写测试时特别有用，因为它增强了可维护性。为此，框架提供了一个名为`QUnit.module()`的方法来将测试分组到模块中。这个方法有第二个参数，在文档中称为`lifecycle`。它是一个对象，可以包含两个可选函数，分别在每个测试之前，`setup`属性和之后，`teardown`属性运行。例如，您可以定义一个模块，如下所示:

```
QUnit.module('My module, {
setup: function() {},
teardown: function() {}
});
```

在 1.16 版本中，这两个属性已被弃用，并被两个分别名为`beforeEach`和`afterEach`的等效属性所取代。因此，从此版本开始，您应该将模块定义为:

```
QUnit.module('My module, {
beforeEach: function() {},
afterEach: function() {}
});
```

这种改变是有意义的，因为函数实际上是在每次测试之前和之后运行的，而不是像原来的名字所暗示的那样在每个模块之前和之后运行。

## 其他变化

当使用`-require`选项运行时，QUnit 现在与 Rhino 兼容。框架搜索一个`exports`对象，并使用它来导出自己。

在文章 [QUnit 高级概念:模块和配置](https://www.sitepoint.com/qunit-advanced-concepts-modules-configuration/)中，我介绍了所有可用的选项。在 QUnit 的新版本中，`module`属性在`moduleFilter`中被重命名。

当单击单个测试的“重新运行”链接时，现在使用测试名称的散列来引用测试，称为`testId`，而不是之前的`testNumber`。此更改确保即使测试顺序发生变化，QUnit 也会重新运行所选的相同测试。

## 结论

在这篇简短的文章中，您了解了 QUnit 1.16 中的新特性和变化。如果你想发现更多，你可以[看看变更日志](https://github.com/jquery/qunit/blob/1.16.0/History.md)。将你的测试升级到这个版本应该不会很难，但是很明显这也取决于你的项目测试的数量。

你对这些变化有什么看法？你在用 QUnit 测试你的项目吗？你有什么想法？我们开始讨论吧。

## 分享这篇文章