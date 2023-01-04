# JavaScript 测试工具对决:Sinon.js vs testdouble.js

> 原文：<https://www.sitepoint.com/javascript-testing-tool-showdown-sinon-js-vs-testdouble-js/>

![Two Greek warriors with shields battle bugs](img/da4feb0d38247eb9060ef6b084c18d84.png)

当单元测试真实世界的代码时，有许多情况使得测试很难编写。如何检查函数是否被调用？如何测试 Ajax 调用？还是用`setTimeout`编码？这时你就要使用*测试替身*——替换代码，让难以测试的东西变得容易测试。

多年来， [Sinon.js](http://sinonjs.org/) 一直是 JavaScript 测试中创建测试替身的事实上的标准。对于任何编写测试的 JavaScript 开发人员来说，这是一个必备的工具，因为没有它，为真正的应用程序编写测试几乎是不可能的。

最近，一个名为 [testdouble.js](https://github.com/testdouble) 的新库掀起了波澜。它拥有与 Sinon.js 相似的功能集，只是有些不同。

在本文中，我们将研究 Sinon.js 和 testdouble.js 都提供了什么，并比较它们各自的优缺点。Sinon.js 会依然是优选，还是挑战者拿奖？

注:如果你对测试双打不熟悉，我推荐你先看一下[我的 Sinon.js 教程](https://www.sitepoint.com/sinon-tutorial-javascript-testing-mocks-spies-stubs/)。它将帮助你更好地理解我们在这里将要讨论的概念。

## 本文中使用的术语

为了确保容易理解所讨论的内容，这里有一个所用术语的快速概述。这些是对 Sinon.js 的定义，在其他地方可能略有不同。

*   **测试 double** 是测试期间使用的函数的替代品。它可以指下面提到的三种类型中的任何一种。
*   一个 **spy** 是一个测试 double，它允许在不影响目标函数行为的情况下检查效果。
*   一个 **stub** 是一个测试 double，它用别的东西代替目标函数的行为，比如返回值。
*   一个**模拟**是一种不同的存根方法。模拟包含内置的验证，可以用来代替单独的断言。

应该注意的是，testdouble.js 的目标之一是减少这类术语之间的混淆。

## Sinon.js 和 testdouble.js 一览

我们先来看看 Sinon.js 和 testdouble.js 在基本用法上的对比。

Sinon 对测试替身有三个独立的概念:间谍、存根和模拟。这个想法是每个代表一个不同的使用场景。这使得来自其他语言的人或读过使用相同术语的书籍的人更熟悉这个库，例如 [xUnit 测试模式](http://xunitpatterns.com/)。但另一面是，这三个概念也会让希能*在第一次使用时*更难理解。

以下是 Sinon 用法的一个基本例子:

```
//Here's how we can see a function call's parameters:
var spy = sinon.spy(Math, 'abs');

Math.abs(-10);

console.log(spy.firstCall.args); //output: [ -10 ]
spy.restore();

//Here's how we can control what a function does:
var stub = sinon.stub(document, 'createElement');
stub.returns('not an html element');

var x = document.createElement('div');

console.log(x); //output: 'not an html element'
stub.restore(); 
```

相比之下，testdouble.js 选择了更简单的 API。它没有使用间谍或存根之类的概念，而是使用 JavaScript 开发人员更熟悉的语言，比如`td.function`、`td.object`和`td.replace`。这使得 testdouble 可能更容易获得，并且更适合某些任务。但另一方面，一些更高级的用途可能根本做不到(有时是故意的)。

下面是 testdouble.js 在使用时的样子:

```
//Here's how we can see a function call's parameters:
var abs = td.replace(Math, 'abs');

Math.abs(-10);

var explanation = td.explain(abs);
console.log(explanation.calls[0].args); //output: [ -10 ]

//Here's how we can control what a function does:
var createElement = td.replace(document, 'createElement');
td.when(createElement(td.matchers.anything())).thenReturn('not an html element');

var x = document.createElement('div');
console.log(x); //output: 'not an html element'

//testdouble resets all testdoubles with one call, no need for separate cleanup
td.reset(); 
```

testdouble 使用的语言更简单。我们“替换”一个函数，而不是“删除”它。我们要求 testdouble“解释”一个函数以从中获取信息。除此之外，到目前为止，它与 Sinon 非常相似。

这也延伸到创建“匿名”测试替身:

```
var x = sinon.stub(); 
```

和...相对

```
var x = td.function(); 
```

西农的间谍和树桩的属性提供了更多关于他们的信息。比如 Sinon 提供了`stub.callCount`、`stub.args`等属性。在 testdouble 的例子中，我们从`td.explain`那里得到这个信息:

```
//we can give a name to our test doubles as well
var x = td.function('hello');

x('foo', 'bar');

td.explain(x);
console.log(x);
/* Output:
{
  name: 'hello',
  callCount: 1,
  calls: [ { args: ['foo', 'bar'], context: undefined } ],
  description: 'This test double `hello` has 0 stubbings and 1 invocations.\n\nInvocations:\n  - called with `("foo", "bar")`.',
  isTestDouble: true
}
*/ 
```

最大的区别之一与如何设置存根和验证有关。使用 Sinon，您可以在存根之后链接命令，并使用断言来验证结果。testdouble.js 只是让你显示你希望函数如何被调用——或者如何“排练”函数调用。

```
var x = sinon.stub();
x.withArgs('hello', 'world').returns(true);

var y = sinon.stub();
sinon.assert.calledWith(y, 'foo', 'bar'); 
```

和...相对

```
var x = td.function();
td.when(x('hello', 'world')).thenReturn(true);

var y = td.function();
td.verify(y('foo', 'bar')); 
```

这可以使 testdouble 的 API 更容易理解，因为您不需要知道可以链接什么操作以及何时链接。

## 更详细地比较常见的测试任务

在高层次上，这两个库相当相似。但是在实际项目中，您可能需要执行的常见测试任务是什么呢？让我们来看看差异开始显现的几个案例。

### testdouble.js 没有间谍

首先要注意的是 testdouble.js 没有“间谍”的概念。虽然 Sinon.js 允许我们替换一个函数调用，以便我们从中获取信息，同时保持函数的默认行为，但这在 testdouble.js 中是完全不可能的。

然而，这不一定是个问题。间谍最常见的用法是使用它们来验证回调是否被调用，这很容易用`td.function`实现:

```
var spy = sinon.spy();

myAsyncFunction(spy);

sinon.assert.calledOnce(spy); 
```

和...相对

```
var spy = td.function();

myAsyncFunction(spy);

td.verify(spy()); 
```

虽然这不是一个大问题，但知道这两者之间存在的差异还是有好处的，否则如果您希望能够在 testdouble.js 中以某种更具体的方式使用间谍，您可能会感到惊讶。

### testdouble.js 需要更精确的输入

您将遇到的第二个差异是 testdouble 对输入更严格。

Sinon 的存根和断言都允许您不精确地给出什么参数。这很容易用一个例子来说明:

```
var stub = sinon.stub();
stub.withArgs('hello').returns('foo');

console.log(stub('hello', 'world')); //output: 'foo'

sinon.assert.calledWith(stub, 'hello'); //no error 
```

和...相对

```
var stub = td.function();
td.when(stub('hello')).thenReturn('foo');

console.log(stub('hello', 'world')); //output: undefined

td.verify(stub('hello')); //throws error! 
```

默认情况下，Sinon 不关心一个函数有多少额外的参数。虽然它提供了诸如`sinon.assert.calledWithExactly`之类的功能，但文档中并未建议将这些功能作为默认功能。像`stub.withArgs`这样的函数也没有“精确”的变体。

另一方面，testdouble.js 默认需要指定的精确参数。这是故意的。这个想法是，如果一个函数在测试中被给定了一些其他未指定的参数，那么它可能是一个 bug，应该测试失败。

允许在 testdouble.js 中指定任意参数是可能的，但这不是默认的:

```
//tell td to ignore extra arguments entirely
td.when(stub('hello'), { ignoreExtraArgs: true }).thenReturn('foo'); 
```

其行为类似于 Sinon.js

### testdouble.js 有内置的承诺支持

虽然在 Sinon.js 中使用 promises 并不复杂，但是 testdouble.js 内置了返回和拒绝 promises 的方法。

```
var stub = sinon.stub();
stub.returns(Promise.resolve('foo'));
//or
stub.returns(Promise.reject('foo')); 
```

和...相对

```
var stub = td.function();
td.when(stub()).thenResolve('foo');
//or
td.when(stub()).thenReject('foo'); 
```

*注意*:使用 [sinon-as-promised](https://www.npmjs.com/package/sinon-as-promised) 可以在 Sinon 1.x 中包含类似的便利功能。Sinon 2.0 和更新的版本包含了`stub.resolves`和`stub.rejects`形式的承诺支持

### testdouble.js 的回调支持更加健壮

Sinon 和 testdouble 都提供了一种简单的方法让存根函数调用回调。然而，它们在工作方式上有一些不同。

Sinon 使用`stub.yields`让存根调用作为参数接收的*第一个函数*。

```
var x = sinon.stub();
x.yields('a', 'b');

//callback1 is called with 'a' and 'b'
x(callback1, callback2); 
```

js 默认为节点样式的模式，其中回调被假定为最后一个参数*。在排练调用时，您也不必指定它:*

```
var x = td.function();
td.when(x(td.matchers.anything())).thenCallback('a', 'b');

//callback2 is called with 'a' and 'b'
x(callback1, callback2); 
```

使 testdouble 的回调支持更加健壮的是，您可以很容易地为具有多个回调的场景或者回调顺序不同的场景定义行为。

假设我们想调用`callback1` …

```
var x = td.function();
td.when(x(td.callback, td.matchers.anything())).thenCallback('a', 'b');

//callback1 is called with 'a' and 'b'
x(callback1, callback2); 
```

注意，我们将`td.callback`作为第一个参数传递给了`td.when`中的函数。这告诉 testdouble 哪个参数是我们希望使用的回调。

有了西农，也有可能改变行为:

```
var x = sinon.stub();
x.callsArgWith(1, 'a', 'b');

//callback1 is called with 'a' and 'b'
x(callback1, callback2); 
```

在这种情况下，我们用`callsArgWith`代替`yields`。我们必须提供调用的具体索引，这可能有点复杂，尤其是对于有许多参数的函数。

如果我们想用一些值调用两个回调函数*呢？*

```
var x = td.function();
td.when(x(td.callback('a', 'b'), td.callback('foo', 'bar'))).thenReturn();

//callback1 is called with 'a' and 'b'
//callback2 is called with 'foo' and 'bar'
x(callback1, callback2); 
```

有了西农，这根本不可能。您可以将多个调用链接到`callsArgWith`，但它只会调用其中一个。

### testdouble.js 有内置的模块替换

除了能够使用`td.replace`替换函数之外，testdouble 还允许您替换整个模块。

这主要在您有一个直接导出您需要替换的函数的模块的情况下有用:

```
module.exports = function() {
  //do something
}; 
```

如果我们想用 testdouble 替换这个，我们可以用`td.replace('path/to/file')`，比如…

```
var td = require('testdouble');

//assuming the above function is in ../src/myFunc.js
var myFunc = td.replace('../src/myFunc');

myFunc();

td.verify(myFunc()); 
```

虽然 Sinon.js 可以替换作为某个对象成员的函数，但它不能以类似的方式替换模块。要在使用 Sinon 时做到这一点，您需要使用另一个模块，如 [proxyquire](https://www.npmjs.com/package/proxyquire) 或 [rewire](https://www.npmjs.com/package/rewire)

```
var sinon = require('sinon');
var proxyquire = require('proxyquire');
var myFunc = proxyquire('../src/myFunc', sinon.stub()); 
```

关于模块替换，另一件值得注意的事情是 testdouble.js 会自动替换整个模块。如果它是一个函数导出，就像这里的例子一样，它会替换函数。如果它是一个包含几个函数的对象，它会替换所有的函数。还支持构造函数和 ES6 类。proxyquire 和 rewire 都要求您分别指定要替换什么以及如何替换。

### testdouble.js 缺少一些 Sinon 的助手

如果你使用的是 Sinon 的[假定时器](http://sinonjs.org/releases/v2.1.0/fake-timers/)、[假 XMLHttpRequest 或假服务器](http://sinonjs.org/releases/v2.1.0/fake-xhr-and-server/)，你会注意到它们在 testdouble 中不见了。

假计时器[作为插件](https://github.com/kuy/testdouble-timers)可用，但是 XMLHttpRequests 和 Ajax 功能需要以不同的方式处理。

一个简单的解决方案是替换您正在使用的 Ajax 函数，比如`$.post`:

```
//replace $.post so when it gets called with 'some/url',
//it will call its callback with variable `someData`
td.replace($, 'post');
td.when($.post('some/url')).thenCallback(someData); 
```

### testdouble.js 使得测试后的清理更加容易

对于 Sinon.js 初学者来说，一个常见的绊脚石往往是清除间谍和存根。事实上，Sinon 提供了三种不同的方式来做这件事。

```
it('should test something...', function() {
  var stub = sinon.stub(console, 'log');
  stub.restore();
}); 
```

或者:

```
describe('something', function() {
  var sandbox;
  beforeEach(function() {
    sandbox = sinon.sandbox.create();
  });

  afterEach(function() {
    sandbox.restore();
  });

  it('should test something...', function() {
    var stub = sandbox.stub(console, 'log');
  });
}); 
```

或者:

```
it('should test something...', sinon.test(function() {
  this.stub(console, 'log');

  //with sinon.test, the stub cleans up automatically
})); 
```

通常，在实践中推荐使用沙箱和 sinon.test 方法，否则很容易意外地留下存根或间谍，这可能会在其他测试中引起问题。这可能导致难以跟踪级联故障。

js 只提供了一种清理测试 double 的方法:`td.reset()`。推荐的方法是在`afterEach`钩子中调用它:

```
describe('something', function() {
  afterEach(function() {
    td.reset();
  });

  it('should test something...', function() {
    td.replace(console, 'log');

    //the replaced log function gets cleaned up in afterEach
  });
}); 
```

这极大地简化了测试加倍的设置和测试后的清理，降低了难以跟踪 bug 的可能性。

## 利弊

我们已经看了两个库中的功能。它们都提供了相似的特性集，但是它们的设计理念有所不同。我们能把这个分成正反两面吗？

先说 Sinon.js，它比 testdouble.js 提供了一些额外的特性，它的某些方面更具可配置性。这为它在更特殊的测试场景中提供了更多的灵活性。Sinon.js 还使用那些来自其他语言的人更熟悉的语言——间谍、存根和模拟等概念存在于各种库中，在测试相关书籍中也有讨论。

这样做的缺点是增加了复杂性。虽然它的灵活性允许专家做更多的事情，但也意味着一些任务比 testdouble.js 中的更复杂，对于那些不熟悉 testdouble 概念的人来说，它也可能有更陡峭的学习曲线。事实上，即使像我这样非常熟悉它的人也很难解释清楚`sinon.stub`和`sinon.mock`之间的区别！

相反，testdouble.js 选择了更简单的接口。它的大部分使用起来相当简单，对于 JavaScript 来说感觉更直观，而 Sinon.js 有时会感觉它是用其他语言设计的。由于这一点和它的一些设计原则，初学者可以更容易地掌握，甚至有经验的测试人员也会发现许多任务更容易完成。例如，testdouble 使用相同的 API 来设置测试 double 和验证结果。由于其更简单的清除机制，它也不容易出错。

testdouble 最大的问题是由它的一些设计原则引起的。例如，间谍的完全缺乏会使它对一些喜欢使用他们而不是存根的人来说是不可用的。这在很大程度上是一个见仁见智的问题，你可能根本找不到问题。除此之外，testdouble.js 正在给 Sinon.js 带来一些严重的竞争，尽管它是一个更新的进入者。

### 逐个功能比较

以下是各项功能的对比:

| 特征 | 不然就是 js | testdouble.js |
| --- | --- | --- |
| 间谍 | 是 | 不 |
| 存根 | 是 | 是 |
| 延迟存根结果 | 不 | 是 |
| 嘲弄 | 是 | 是 <sup>1</sup> |
| 承诺支持 | 是(在 2.0+版本中) | 是 |
| 时间助手 | 是 | 是(通过插件) |
| Ajax 助手 | 是 | 否(替换函数) |
| 模块更换 | 不 | 是 |
| 内置断言 | 是 | 是 |
| 匹配项 | 是 | 是 |
| 自定义匹配器 | 是 | 是 |
| [争论捕获者](https://github.com/testdouble/testdouble.js/blob/master/docs/6-verifying-invocations.md#multi-phase-assertions-with-argument-captors) | 否 <sup>2</sup> | 是 |
| [代理测试替身](https://github.com/testdouble/testdouble.js/blob/master/docs/4-creating-test-doubles.md#objectobjectname) | 不 | 是 |

1.  从技术上来说,没有 Sinon.js 那样的模仿。然而，由于 Sinon 中的 mocks 本质上是包含存根和验证的对象，所以通过使用`td.replace(someObject)`可以实现类似的效果
2.  使用`stub.yield`(不要与`stub.yields`混淆)可以获得一些类似于参数捕捉器的效果

## 总结和结论

Sinon.js 和 testdouble.js 都提供了一组非常相似的功能。从这个意义上说，他们两个都不明显更优越。

两者最大的区别在于它们的 API。Sinon.js 可能略显冗长，但提供了许多关于如何做事的选项。这可能是它的福也可能是它的祸。testdouble.js 有一个更精简的 API，这可以使它更容易学习和使用，但由于它更固执己见的设计，一些人可能会发现它有问题。

### 那么哪个适合我呢？

你同意 testdouble 的设计原则吗？如果是的话，那就没有理由不用。我在许多项目中使用过 Sinon.js，我可以有把握地说 testdouble.js 至少完成了我用 Sinon.js 所做的 95%的工作，剩下的 5%可能可以通过一些简单的变通方法来完成。

如果您发现 Sinon.js 很难使用，或者正在寻找一种更“JavaScripty”的方法来进行测试 double，那么 testdouble.js 可能也适合您。即使作为一个花了很多时间学习使用 Sinon 的人，我也倾向于推荐尝试 testdouble.js，看看你是否喜欢它。

然而，testdouble.js 的某些方面可能会让那些了解 Sinon.js 或者是资深测试人员感到头疼。例如，间谍的完全缺乏可能是一个交易破坏者。对于专家和那些想要最大灵活性的人来说，Sinon.js 仍然是一个很好的选择。

如果你想了解更多关于如何在实践中使用测试替身的知识，请查看我的免费的真实世界指南中的 [Sinon.js。虽然它使用 Sinon.js，但是您也可以在 testdouble.js 中应用相同的技术和最佳实践。](https://codeutopia.net/go/sinon-pdf)

有问题吗？评论？你已经在用 testdouble.js 了吗？看完这篇文章后，你会考虑试一试吗？请在下面的评论中告诉我。

*这篇文章由[詹姆斯·赖特](https://www.sitepoint.com/author/jameswright/)、[琼茵](https://github.com/newjs)、[克里斯蒂安·约翰森](https://www.sitepoint.com/author/cjohansen/)和[贾斯汀·西尔斯](https://github.com/searls)进行同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章