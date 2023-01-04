# JavaScript 单元测试中的承诺:权威指南

> 原文：<https://www.sitepoint.com/promises-in-javascript-unit-tests-the-definitive-guide/>

[承诺](https://www.sitepoint.com/overview-javascript-promises/)是 JavaScript 代码的常见部分。

尽管异步代码变得更简单，但是在单元测试中处理承诺是一件麻烦的事情。您需要将测试的断言连接到承诺的回调中，这将向测试中添加额外的代码。这样一来，测试本身就变得有点复杂，也更难看出发生了什么。

在本文中，我将向您展示如何解决这个问题，并讨论能够简化测试阶段常见承诺场景的有用模式。

我已经创建了一个[示例项目，您可以从我的网站](https://github.com/sitepoint-editors/promise-testing-samples)下载，它展示了本文中介绍的技术。

## 入门指南

对于这个项目，我将使用 [Mocha](https://mochajs.org/) 作为测试框架，使用 [Chai](http://chaijs.com/) 库来提供断言。你马上就会明白为什么了。

我们可以简单地运行以下命令来安装 duo:

```
npm install mocha chai
```

当您第一次在单元测试中遇到承诺时，您的测试可能看起来有点像典型的单元测试:

```
var expect = require('chai').expect;

it('should do something with promises', function(done) {
//define some data to compare against
var blah = 'foo';

//call the function we're testing
var result = systemUnderTest();

//assertions
result.then(function(data) {
expect(data).to.equal(blah);
done();
}, function(error) {
assert.fail(error);
done();
});
});
```

我们有一些测试数据，并调用被测系统——我们正在测试的那段代码。但是，承诺出现了，代码变得复杂了。

对于这个承诺，我们添加了两个处理程序。第一个用于已解决的承诺，其中有一个比较相等性的断言，而第二个用于已拒绝的承诺，其中有一个失败的断言。我们还需要它们中的`done()`调用。由于承诺是异步的，我们必须告诉 Mocha 这是一个异步测试，并在完成时通知它。

但是我们为什么需要`assert.fail`？该测试的目的是将成功承诺的结果与一个值进行比较。如果承诺被拒绝，测试应该失败。这就是为什么没有失败处理程序，测试可能会报告一个假阳性！

假阳性是指测试应该失败，但实际上没有失败。例如，假设我们移除了拒绝回调。您的代码应该如下所示:

```
result.then(function(data) {
expect(data).to.equal(blah);
done();
});
```

在这种情况下，如果承诺被拒绝，就不会有错误，因为测试中没有错误处理程序来检查它。但是很明显，在这种情况下，测试应该会失败，因为预期不会运行。这绝对是承诺在考试中变得复杂的主要原因之一。

## 摩卡和承诺

我决定在这个项目中使用 Mocha，因为它内置了对承诺的支持。这意味着一个被拒绝的承诺会让你的测试失败。例如:

```
it('should fail the test', function() {
var p = Promise.reject('this promise will always be rejected');

return p;
});
```

上面的测试返回一个被拒绝的承诺，这意味着它每次都失败。我们可以使用我们所学的知识来改进我们之前的测试，如下面的代码片段所示:

```
var expect = require('chai').expect;

it('should do something with promises', function() {
var blah = 'foo';

var result = systemUnderTest();

return result.then(function(data) {
expect(data).to.equal(blah);
});
});
```

测试现在返回承诺。我们不再需要失败处理程序或`done`回调，因为 Mocha 处理承诺。如果承诺落空，摩卡就通不过考验。

## 用柴的许诺进一步改进考试

如果能直接在承诺上做断言不是很好吗？有了 [chai-as-promised](https://github.com/domenic/chai-as-promised/) ，我们就能！

首先，我们需要安装它运行:

```
npm install chai-as-promised
```

我们可以这样使用它:

```
var chai = require('chai');
var expect = chai.expect;

var chaiAsPromised = require('chai-as-promised');
chai.use(chaiAsPromised);

it('should do something with promises', function() {
var blah = 'foo';

var result = systemUnderTest();

return expect(result).to.eventually.equal(blah);
});
```

我们用 Chai 断言替换了整个`then`设置。这里的关键是`eventually`。当用柴比较数值时，我们可以用

```
expect(value).to.equal(something);
```

但是如果`value`是一个承诺，我们插入`eventually`并返回它:

```
return expect(value).to.eventually.equal(something)
```

现在，柴处理这个承诺。

**注意:**不要忘记还承诺，不然摩卡不知道需要办理！

我们可以把柴的任何一个论断和`eventually`一起用。例如:

```
//assert promise resolves with a number between 1 and 10
return expect(somePromise).to.eventually.be.within(1, 10);

//assert promise resolves to an array with length 2
return expect(somePromise).to.eventually.have.length(2);
```

## 测试中有用的承诺模式

### 比较对象

如果您的承诺的解析值应该是一个对象，那么您可以像平常一样使用相同的方法进行比较。例如，用`deep.equal`你可以写一个这样的语句:

```
return expect(value).to.eventually.deep.equal(obj)
```

同样的警告也适用于没有承诺的情况。如果你正在比较对象，`equal`将会比较引用，当对象具有相同的属性，但却是不同的对象时，你的测试将会失败。

chai-as-promised 有一个方便的比较对象的助手:

`return expect(value).to.eventually.become(obj)`

使用`eventually.become`和做深度相等比较是一样的。您可以将它用于大多数与承诺的等式比较——字符串、数字等等——除非您特别需要引用比较。

### 针对对象的特定属性进行断言

有时，您可能希望只检查承诺对象中的单个属性。有一种方法可以做到:

```
var value = systemUnderTest();

return value.then(function(obj) {
expect(obj.someProp).to.equal('something');
});
```

但是，有了柴的承诺，还有另一种方式。我们可以利用你可以连锁承诺的事实:

```
var value = systemUnderTest().then(function(obj) {
return obj.someProp;
});

return expect(value).to.eventually.equal('something');
```

作为最后一种选择，如果您使用 ECMAScript 2015，您可以使用 fat [arrow 函数语法](https://www.sitepoint.com/javascript-arrow-functions/)使它更简洁一些:

```
var value = systemUnderTest()

return expect(value.then(o => o.someProp)).to.eventually.equal('something');
```

### 多重承诺

如果你在测试中有多个承诺，你可以像在非测试代码中那样使用`Promise.all`。

```
return Promise.all([
expect(value1).to.become('foo'),
expect(value2).to.become('bar')
]);
```

但是请记住，这类似于在单个测试中有多个断言，这可以被视为代码味道。

### 比较多个承诺

如果您需要比较两个(或更多)承诺，那么可以使用以下模式:

```
return Promise.all([p1, p2]).then(function(values) {
expect(values[0]).to.equal(values[1]);
});
```

换句话说，我们可以使用`all`来解析这两个承诺，并使用`then`中的函数对返回值运行普通的 Chai 断言。

### 断言失败

有时，您可能希望检查某个调用是否会导致承诺失败而不是成功。在这种情况下，你可以使用柴的`rejected`断言:

```
return expect(value).to.be.rejected;
```

如果您想确保拒绝带有特定类型的错误或消息，您也可以使用`rejectedWith`:

```
//require this promise to be rejected with a TypeError
return expect(value).to.be.rejectedWith(TypeError);

//require this promise to be rejected with message 'holy smokes, Batman!'
return expect(value).to.be.rejectedWith('holy smokes, Batman!');
```

### 测试挂钩

您可以像在任何其他测试函数中一样，在测试挂钩中使用承诺。这与`before`、`after`、`beforeEach`和`afterEach`一起工作。例如:

```
describe('something', function() {
before(function() {
return somethingThatReturnsAPromise();
});

beforeEach(function() {
return somethingElseWithPromises();
});
});
```

这些工作类似于承诺在测试中的工作方式。如果承诺被拒绝，Mocha 将抛出一个错误。

## 承诺和嘲笑/存根

最后，让我们看看如何使用带有存根的承诺。我用的是西农。JS 下面的例子。为此，您需要通过执行以下命令来安装它:

```
npm install sinon
```

### 从存根返回承诺

如果你需要一个存根或者一个模拟来回复一个承诺，答案相当简单:

```
var stub = sinon.stub();

//return a failing promise
stub.returns(Promise.reject('a failure'));

//or a successful promise
stub.returns(Promise.resolve('a success'));
```

### 窥探承诺

您可以像使用其他函数一样使用 spies 作为 promise 回调，但是由于 promise 是异步的，所以它可能没有用。如果你需要对一个承诺做一个断言，你最好用 chai-as-promised 来做。

```
var spy = sinon.spy();
var promise = systemUnderTest();

promise.then(spy);
```

### 西农如约

为了稍微简化存根和承诺，我们可以使用 sinon-as-promised。它可以通过 npm 安装:

```
npm install sinon-as-promised
```

它在存根上提供了助手函数`resolves`和`rejects`

```
var sinon = require('sinon');

//this makes sinon-as-promised available in sinon:
require('sinon-as-promised');

var stub = sinon.stub();

//return a failing promise
stub.rejects('a failure');

//or a successful promise
stub.resolves('a success');
```

## 结论

承诺可以简化我们的异步代码，它们甚至可以简化异步测试——只要你在其中加入一些有用的库。

Mocha 的内置承诺支持与 Chai 和 chai-as-promised 相结合，使得测试承诺返回代码变得简单。把 SinonJS 和 sinon-as-promised 加到这个组合中，你也可以很容易地把它们消灭掉。

要记住的一件重要的事情是:当在测试中使用承诺时，**总是从测试中返回一个承诺，否则 Mocha 不会知道，你的测试可能会无声无息地失败，而不会告诉你。**

正如我在介绍中提到的，我已经创建了一个[示例项目，您可以从我的网站](https://github.com/sitepoint-editors/promise-testing-samples)下载，它展示了本文中介绍的技术。请随意下载并使用它。

## 分享这篇文章