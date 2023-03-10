# 从回调地狱中拯救

> 原文：<https://www.sitepoint.com/saved-from-callback-hell/>

**回调地狱是真实的。开发人员经常将回调视为纯粹的邪恶，甚至到了回避回调的地步。JavaScript 的灵活性对此毫无帮助。但不一定要避免回调。好消息是，有一些简单的步骤可以从回调地狱中拯救出来。**

![Devil standing over offce worker with receivers hanging everywhere](img/6f3d8551cff750d49001603a2f9b5d90.png)

消除代码中的回调就像截掉一条好腿。回调函数是 JavaScript 的支柱之一，也是它的优点之一。当你替换回调时，你通常只是在交换问题。

有人说回调是丑陋的疣，是学习更好语言的理由。试镜有那么难看吗？

在 JavaScript 中使用回调有它自己的好处。没有理由回避 JavaScript，因为回调可能会变成丑陋的疣。我们可以确保这不会发生。

让我们深入了解声音编程通过回调提供了什么。我们倾向于坚持[坚实的原则](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))，看看这会把我们带向何方。

## 什么是回调地狱？

你可能想知道什么是回调，为什么你应该关心。在 JavaScript 中，**回调**是一个充当委托的函数。委托在未来的任意时刻执行。在 JavaScript 中，[委托](https://en.wikipedia.org/wiki/Delegation_(computing))发生在接收函数调用回调的时候。接收函数可以在其执行过程中的任意时刻这样做。

简而言之，回调是作为参数传递给另一个函数的函数。没有立即执行，因为接收函数决定何时调用它。下面的[代码示例](https://github.com/sitepoint-editors/FunWithCallbacks/commit/9b5fad82a3b8dbd848c879dabfe1c6be84792c1a)说明了:

```
function receiver(fn) {
  return fn();
}

function callback() {
  return 'foobar';
}

var callbackResponse = receiver(callback); 
// callbackResponse == 'foobar' 
```

如果您曾经编写过 Ajax 请求，那么您会遇到回调函数。异步代码使用这种方法，因为无法保证回调何时执行。

回调的问题源于依赖于另一个回调的异步代码。我们可以使用`setTimeout`来模拟带有回调函数的异步调用。

请随意跟随。回购可以在 GitHub 上获得，大多数代码片段都来自那里，所以你可以跟着玩。

看，末日金字塔！

```
setTimeout(function (name) {
  var catList = name + ',';

  setTimeout(function (name) {
    catList += name + ',';

    setTimeout(function (name) {
      catList += name + ',';

      setTimeout(function (name) {
        catList += name + ',';

        setTimeout(function (name) {
          catList += name;

          console.log(catList);
        }, 1, 'Lion');
      }, 1, 'Snow Leopard');
    }, 1, 'Lynx');
  }, 1, 'Jaguar');
}, 1, 'Panther'); 
```

看上面的代码，`setTimeout`得到一个一毫秒后执行的回调函数。最后一个参数只是向回调提供数据。这类似于 Ajax 调用，除了返回的参数来自服务器。

SitePoint 上有一个关于 setTimeout 函数的很好的概述。

在我们的代码中，我们通过异步代码收集了一系列凶猛的猫。每个回调给我们一个猫名，我们把它添加到列表中。我们试图实现的目标听起来很合理。但是考虑到 JavaScript 函数的灵活性，这是一个噩梦。

## 匿名函数

注意前面例子中匿名函数的使用。**匿名函数**是未命名的函数表达式，被赋给一个变量或作为参数传递给其他函数。

一些[编程标准](https://github.com/felixge/node-style-guide#name-your-closures)不推荐在你的代码中使用匿名函数。给它们起名字比较好，所以用`function getCat(name){}`代替`function (name){}`。将名字放在函数中可以增加程序的清晰度。这些匿名函数很容易输入，但是它们会把你送上通往地狱的高速公路。当你发现自己走在这条蜿蜒曲折的道路上时，最好停下来重新思考一下。

打破这种混乱的回调的一个简单方法是使用函数声明:

```
setTimeout(getPanther, 1, 'Panther');

var catList = '';

function getPanther(name) {
  catList = name + ',';

  setTimeout(getJaguar, 1, 'Jaguar');
}

function getJaguar(name) {
  catList += name + ',';

  setTimeout(getLynx, 1, 'Lynx');
}

function getLynx(name) {
  catList += name + ',';

  setTimeout(getSnowLeopard, 1, 'Snow Leopard');
}

function getSnowLeopard(name) {
  catList += name + ',';

  setTimeout(getLion, 1, 'Lion');
}

function getLion(name) {
  catList += name;

  console.log(catList);
} 
```

您不会在回购中找到这个片段，但是可以在 [this commit](https://github.com/sitepoint-editors/FunWithCallbacks/commit/59017f3c859603cfdcbba8e009c123a1da8456af) 中找到增量改进。

每个函数都有自己的声明。一个好处是我们不再看到可怕的金字塔。每个功能都被隔离开来，专注于自己的特定任务。每个功能现在都有一个改变的理由，所以这是朝着正确方向迈出的一步。注意，例如，`getPanther()`被分配给参数。JavaScript 不关心我们如何创建回调。但是缺点是什么呢？

关于这些差异的完整分析，请参见这篇关于函数表达式和函数声明的文章。

但是，缺点是每个函数声明不再局限于回调函数。现在，每个函数都粘在了外部作用域上，而不是使用回调作为闭包。这就是为什么`catList`在外部作用域中被声明，因为这授予了回调对列表的访问权。有时，破坏全局范围并不是一个理想的解决方案。还有代码复制，因为它将一只猫添加到列表中，并调用下一个回调函数。

这些是从回调地狱继承的代码味道。有时候，努力进入回调自由需要毅力和对细节的关注。可能会开始觉得疾病比治疗更好。有没有更好的编码方式？

## 依赖性倒置

依赖倒置原则说我们应该编码抽象，而不是实现细节。在核心部分，我们把一个大问题分解成小的依赖项。在实现细节不相关的情况下，这些依赖性变得独立。

这个坚实的原理[陈述了](https://en.wikipedia.org/wiki/Dependency_inversion_principle):

> 当遵循该原则时，从高级策略设置模块到低级依赖模块建立的传统依赖关系被颠倒，从而使得高级模块独立于低级模块实现细节。

那么这一团文字是什么意思呢？好消息是，通过给参数分配回调，我们已经在这么做了！至少在某种程度上，为了解耦，可以把回调看作依赖。这种依赖变成了契约。从现在开始，我们正在进行可靠的编程。

获得回拨自由的一个方法是创建一个合同:

```
fn(catList); 
```

这定义了我们计划对回调做什么。它需要跟踪一个参数——也就是我们的凶猛猫列表。

这种依赖性现在可以通过一个参数来提供:

```
function buildFerociousCats(list, returnValue, fn) {
  setTimeout(function asyncCall(data) {
    var catList = list === '' ? data : list + ',' + data;

    fn(catList);
  }, 1, returnValue);
} 
```

注意，函数表达式`asyncCall`的作用域是闭包`buildFerociousCats`。当与异步编程中的回调结合使用时，这种技术非常强大。契约异步执行并获得它需要的`data`，所有这些都是通过合理的编程实现的。契约获得了它所需要的自由，因为它与实现分离了。漂亮的代码利用了 JavaScript 的灵活性。

接下来需要发生的事情就不言自明了。我们可以这样做:

```
buildFerociousCats('', 'Panther', getJaguar);

function getJaguar(list) {
  buildFerociousCats(list, 'Jaguar', getLynx);
}

function getLynx(list) {
  buildFerociousCats(list, 'Lynx', getSnowLeopard);
}

function getSnowLeopard(list) {
  buildFerociousCats(list, 'Snow Leopard', getLion);
}

function getLion(list) {
  buildFerociousCats(list, 'Lion', printList);
}

function printList(list) {
  console.log(list);
} 
```

这里没有重复的代码。回调现在不用全局变量就能跟踪自己的状态。像`getLion`这样的回调可以与契约后面的任何东西链接起来——也就是说，任何将凶猛的猫的列表作为参数的抽象。这个样本代码可以在 [GitHub](https://github.com/sitepoint-editors/FunWithCallbacks/blob/master/callbackDependencyInversion.js) 上找到。

## 多态回调

好吧，让我们疯狂一下。如果我们想将行为从创建逗号分隔的列表改为管道分隔的列表呢？我们可以设想的一个问题是,`buildFerociousCats`已经被粘在了一个实现细节上。请注意使用`list + ',' + data`来完成此操作。

简单的答案是带有回调的多态行为。原则仍然是:像对待合同一样对待回调，并使实现变得无关紧要。一旦回调上升到一个抽象，具体的细节可以随意改变。

多态性开辟了 JavaScript 中代码重用的新途径。将**多态**回调视为定义严格契约的一种方式，同时允许足够的自由，实现细节不再重要。请注意，我们仍然在讨论依赖倒置。多态回调只是一个花哨的名字，它指出了进一步发展这一思想的一种方式。

让我们来定义合同。我们可以在本合同中使用`list`和`data`参数:

```
cat.delimiter(cat.list, data); 
```

然后我们可以对`buildFerociousCats`做一些调整:

```
function buildFerociousCats(cat, returnValue, next) {
  setTimeout(function asyncCall(data) {
    var catList = cat.delimiter(cat.list, data);

    next({ list: catList, delimiter: cat.delimiter });
  }, 1, returnValue);
} 
```

JavaScript 对象`cat`现在封装了`list`数据和`delimiter`函数。`next`回调链异步回调——以前称为`fn`。注意，JavaScript 对象可以自由地对参数进行分组。`cat`对象需要两个特定的键— `list`和`delimiter`。这个 JavaScript 对象现在是合同的一部分。代码的其余部分保持不变。

要启动它，我们可以这样做:

```
buildFerociousCats({ list: '', delimiter: commaDelimiter }, 'Panther', getJaguar);
buildFerociousCats({ list: '', delimiter: pipeDelimiter }, 'Panther', getJaguar); 
```

回调被交换。只要合同得到履行，实施细节就无关紧要。我们可以轻松地改变行为。回调，现在是一个依赖，变成了一个高级契约。这个想法将我们已经知道的回调提升到了一个新的水平。减少对合同的回调提升了抽象性，并分离了软件模块。

这里的激进之处在于单元测试自然地从独立的模块中流出。契约是一个纯函数。这意味着，给定一些输入，我们每次都得到相同的输出。这种级别的可测试性增加了解决方案工作的信心。毕竟，模块化独立性赋予了自我评估的权利。

围绕管道分隔符的有效单元测试可能如下所示:

```
describe('A pipe delimiter', function () {
  it('adds a pipe in the list', function () {
    var list = pipeDelimiter('Cat', 'Cat');

    assert.equal(list, 'Cat|Cat');
  });
}); 
```

我将让您想象一下实现细节是什么样的。请随意查看 GitHub 上的[提交。](https://github.com/sitepoint-editors/FunWithCallbacks/commit/f5cd1988ecb29d97b10180cbe1ec246adbc53d03)

## 承诺

一个[承诺](https://www.sitepoint.com/overview-javascript-promises/)仅仅是一个回调机制的包装器，并且允许一个*接着*继续执行流程。这使得代码更加可重用，因为你可以返回一个承诺并[将承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises#chaining)链接起来。

让我们在多态回调的基础上构建，并围绕承诺进行包装。调整`buildFerociousCats`函数，让它返回一个承诺:

```
function buildFerociousCats(cat, returnValue, next) {
  return new Promise((resolve) => { //wrapper and return Promise
    setTimeout(function asyncCall(data) {
      var catList = cat.delimiter(cat.list, data);

      resolve(next({ list: catList, delimiter: cat.delimiter }));
    }, 1, returnValue);
  });
} 
```

注意`resolve`的用法:这是解决承诺的方法，而不是直接使用回调。消费代码可以应用一个`then`来继续执行流程。

因为我们现在返回一个承诺，所以代码必须在回调执行中跟踪承诺。

让我们更新回调函数来返回承诺:

```
function getJaguar(cat) {
  return buildFerociousCats(cat, 'Jaguar', getLynx); // Promise
}

function getLynx(cat) {
  return buildFerociousCats(cat, 'Lynx', getSnowLeopard);
}

function getSnowLeopard(cat) {
  return buildFerociousCats(cat, 'Snow Leopard', getLion);
}

function getLion(cat) {
  return buildFerociousCats(cat, 'Lion', printList);
}

function printList(cat) {
  console.log(cat.list); // no Promise
} 
```

最后一次回调没有连锁承诺，因为它没有返回的承诺。记录承诺对于保证最后的延续是很重要的。通过类比，当我们做出承诺时，信守承诺的最好方式就是记住我们曾经做出过承诺。

现在让我们用一个可用的函数调用来更新主调用:

```
buildFerociousCats({ list: '', delimiter: commaDelimiter }, 'Panther', getJaguar)
  .then(() => console.log('DONE')); // outputs last 
```

如果我们运行代码，我们会看到最后打印出“DONE”。如果我们忘记在流程中的某个地方返回一个承诺，“完成”将会出现混乱，因为它失去了对最初承诺的跟踪。

请随意查看 GitHub 上的[承诺。](https://github.com/sitepoint-editors/FunWithCallbacks/commit/d741be73c7e4fadb6d19a285395f2159f02869f2)

## 异步/等待

最后，我们可以把 async/await 看作是承诺的语法糖。对于 JavaScript 来说，async/await 实际上是一个承诺，但是对于程序员来说，它读起来更像同步代码。

从我们目前拥有的代码来看，让我们去掉`then`并将调用包装在 async/await 上:

```
async function run() {
  await buildFerociousCats({ list: '', delimiter: pipeDelimiter }, 'Panther', getJaguar)
  console.log('DONE');
}
run().then(() => console.log('DONE DONE')); // now really done 
```

输出“DONE”在`await`之后立即执行，因为它的工作方式很像同步代码。只要调用 into `buildFerociousCats`返回一个承诺，我们就可以等待调用。`async`将该函数标记为一个返回承诺的函数，因此仍然有可能将调用链接到`run`和另一个`then`。只要我们调用的 into 返回一个承诺，我们就可以无限地链接承诺。

您可以在 GitHub 上的[异步/等待提交中检查这一点。](https://github.com/sitepoint-editors/FunWithCallbacks/commit/85fb597c380a57b0c2c70692fb17ecb179ae2a73)

请记住，所有这些异步代码都是在单线程的上下文中运行的。JavaScript 回调非常适合这种单线程范式，因为回调的排队方式不会阻止进一步的执行。这使得 JavaScript 引擎可以更容易地跟踪回调，并立即获得回调，而不必处理同步多个线程。

## 结论

掌握 JavaScript 中的回调就是理解所有的细节。我希望您能看到 JavaScript 函数中的细微变化。当我们缺乏基本面时，回调函数就会被误解。一旦 JavaScript 功能清晰了，坚实的原则很快就会随之而来。需要对基础知识有很强的掌握，才能尝试可靠的编程。语言固有的灵活性将责任的重担放在了程序员身上。

我最喜欢的是 JavaScript 赋予了优秀的编程能力。对所有细节和基础的良好掌握将使我们在任何语言中走得更远。这种回调函数的方法在普通 JavaScript 中非常重要。不可避免地，所有的角落和缝隙都会让我们的技能更上一层楼。

## 分享这篇文章