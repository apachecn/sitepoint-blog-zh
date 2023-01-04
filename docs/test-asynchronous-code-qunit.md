# 如何用 QUnit 测试异步代码

> 原文：<https://www.sitepoint.com/test-asynchronous-code-qunit/>

几周前，我发表了一篇名为[开始使用 QUnit](https://www.sitepoint.com/getting-started-qunit/) 的文章，其中我讨论了单元测试的主要概念，以及我们如何使用 QUnit 测试 JavaScript 代码。在那篇文章中，我重点关注了框架提供的断言以及如何测试同步运行的代码。然而，如果我们想讨论真实世界的情况，我们不能避免谈论异步函数。

就像同步函数一样，异步的需要爱，更需要测试。在本文中，我将教你如何用 QUnit 测试异步代码。如果您不记得可用的断言方法，或者您完全错过了我的文章，我建议您阅读[quinit](https://www.sitepoint.com/getting-started-qunit/)入门。它所涵盖的材料将是这篇文章的先决条件。

## 用 QUnit 创建异步测试

用 JavaScript 编写的每个重要项目都包含异步函数。它们用于在一定时间后执行给定的操作，从服务器检索数据，或向服务器发送数据。QUnit 提供了一个方法，叫做`QUnit.asyncTest()`，目的是测试异步代码。该方法的特征是:

```
QUnit.asyncTest(name, testFunction)
```

参数的含义与`QUnit.test()`相同，但是为了方便起见，我在这里报告它们:

*   `name`:帮助我们识别创建的测试的字符串。
*   `testFunction`:包含框架将要执行的断言的函数。框架向该函数传递一个参数，该参数公开了 QUnit 的所有断言方法。

这个方法接受与`QUnit.test()`相同的参数的事实可能会引起误解。你可能认为原理是一样的，测试一个异步函数所要做的就是用`QUnit.asyncTest()`替换对`QUnit.test()`的调用，这样就完成了。没那么快！

为了完成它的工作，`QUnit.asyncTest()`需要和另外两个方法一起使用:`QUnit.start()`和`QUnit.stop()`。让我们更多地了解他们。

### `QUnit.start()`和`QUnit.stop()`

当 QUnit 执行使用`QUnit.asyncTest()`创建的测试时，它会自动停止 testrunner。然后，它会一直等到包含断言的函数调用`QUnit.start()`。`QUnit.start()`的目的是在运行测试停止后开始或恢复运行测试。这个方法接受一个整数作为它唯一的可选参数<q cite="http://api.qunitjs.com/QUnit.start/">，将多个`QUnit.start()`调用合并成一个</q>。

可以使用方法`QUnit.stop()`停止测试。它<q cite="http://api.qunitjs.com/QUnit.stop/">增加了 testrunner 在继续</q>之前必须等待的`QUnit.start()`调用的数量。这个方法接受一个整数作为它唯一的可选参数，指定框架必须等待的对`QUnit.start()`的额外调用的次数。其默认值为 1。

有点难以理解，不是吗？包含对应方法的方法定义听起来完全是一团糟。不幸的是，这正是他们所做的。我所知道的澄清这些概念的最好方法是给你一个具体的使用例子。

## 把所有的放在一起

在这一节中，我们将把到目前为止讨论过的方法付诸实施。希望一旦你读了它，你会对这个机制有一个深入的了解。

让我们从一个简单的例子开始，这个例子使用了在文章[开始使用 QUnit](https://www.sitepoint.com/getting-started-qunit/) : `max()`中开发的一个函数。这个函数接受任意数量的参数并返回最大值。该函数的代码如下所示:

```
function max() {
   var max = -Infinity;
   for (var i = 0; i < arguments.length; i++) {
      if (arguments[i] > max) {
         max = arguments[i];
      }
   }

   return max;
}
```

现在，想象一下这个函数通常对一组非常大的参数起作用。我们希望避免用户的浏览器在结果计算出来之前被阻塞。为此，我们将在传递给`window.setTimeout()`的回调中调用`max()`，延迟值为`0`。

异步测试该函数的代码如下所示，这将让您对使用`QUnit.start()`有所了解:

```
QUnit.asyncTest('max', function (assert) {
   expect(1);

   window.setTimeout(function() {
      assert.strictEqual(max(3, 1, 2), 3, 'All positive numbers');
      QUnit.start();
   }, 0); 
});
```

在上面的代码中，我将对`max()`函数的调用包装为对`window.setTimeout()`的回调。在使用`max()`的断言被执行之后，我们调用`QUnit.start()`方法来允许 testrunner 恢复它的执行。如果我们避免调用这个方法，testrunner 将被卡住，我们的测试将悲惨地失败(实际上测试暂停了，没有做任何其他事情，所以这不是真正的断言失败)。

前面的例子应该很容易理解，因为它与它的同步对应物非常相似。但是，只测试一种情况不允许我们信任我们的代码。此外，我们还没有机会看到`QUnit.stop()`的行动。为了解决这个问题，我们将在传递给`QUnit.asyncTest()`的函数中实现我们在上一篇文章中看到的所有断言。

完整代码报告如下:

```
QUnit.asyncTest('max', function (assert) {
   expect(4);
   QUnit.stop(3);

   window.setTimeout(function() {
      assert.strictEqual(max(), -Infinity, 'No parameters');
      QUnit.start();
   }, 0);

   window.setTimeout(function() {
      assert.strictEqual(max(3, 1, 2), 3, 'All positive numbers');
      QUnit.start();
   }, 0);

   window.setTimeout(function() {
      assert.strictEqual(max(-10, 5, 3, 99), 99, 'Positive and negative numbers');
      QUnit.start();
   }, 0);

   window.setTimeout(function() {
      assert.strictEqual(max(-14, -22, -5), -5, 'All positive numbers');
      QUnit.start();
   }, 0);   
});
```

在测试中，我们设置了我们期望运行的断言数量，正如我们在[开始使用 QUnit](https://www.sitepoint.com/getting-started-qunit/) 中所讨论的。然后，该函数调用`QUnit.stop()`方法。这是必要的，因为在测试中我们执行四个异步调用。当我们使用`QUnit.asyncTest()`时，框架只等待一个对`QUnit.start()`的调用。如果我们省略对`QUnit.stop()`的调用，指定对`QUnit.start()`的另外三个调用，测试将失败，因为预期的断言数量与执行的断言数量不同。

代码的现场演示，包括对`expect()`的调用，如下所示，并且[可以作为 JSBin](http://jsbin.com/sevuwazo/1/edit?html,js,output) 获得。

[与 QUnit 的异步测试](http://jsbin.com/sevuwazo/1/embed?html,js,output)