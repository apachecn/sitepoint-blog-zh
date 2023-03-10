# QUnit 入门

> 原文：<https://www.sitepoint.com/getting-started-qunit/>

软件测试是评估一个软件的过程，以检测给定输入集的预期输出和实际输出之间的差异。测试，尤其是单元测试，应该是每个开发人员生活中必不可少的一部分。不幸的是，许多开发人员似乎害怕这种活动。

在 JavaScript 中，有很多框架可供我们选择来测试我们的代码库。一些例子是[摩卡](http://visionmedia.github.io/mocha/)、[硒](http://docs.seleniumhq.org/)和 [QUnit](http://qunitjs.com/) 。在本文中，我将向您介绍 QUnit。QUnit 是由 jQuery 团队开发和维护的单元测试框架，jQuery 和 jQuery UI 等项目背后的团队也是这个团队。

## 设置 QUnit

很多开发者使用 QUnit 的主要原因之一是它的易用性。从这个框架开始非常简单，几个小时就能掌握主要概念。

使用 QUnit 的第一步显然是下载它。有几种方法可以做到这一点:从网站手动下载，使用 CDN，使用 Bower，或使用 npm。我的建议是，除非你正在开发一个简单的现场演示，否则你不应该依赖 CDN 来测试你的代码。所以，坚持其他选择。

对于本文，我不想设置任何先决条件(read Bower 和 npm)，所以我们将采用第一种方法。所以去 [QUnit 网站](http://qunitjs.com/)下载 JavaScript 文件(名为 qunit-1.14.0.js)和 CSS 文件(名为 qunit-1.14.0.css)的最新版本。

将它们放入一个文件夹中，在那里您还将创建一个`index.html`。在这个文件中，我们将放置在网站主页中显示的 HTML 代码，我在下面为您的商品重复了这些代码。

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8">
      <title>QUnit Example</title>
      <link rel="stylesheet" href="//code.jquery.com/qunit/qunit-1.14.0.css">
   </head>
   <body>
      <div id="qunit"></div>
      <div id="qunit-fixture"></div>
      <script src="//code.jquery.com/qunit/qunit-1.14.0.js"></script>
      <script src="tests.js"></script>
   </body>
</html>
```

如您所见，这段代码使用 CDN 来包含 CSS 和 JavaScript 文件。因此，您必须更新链接，以包含您之前下载的文件。

在标记中，您可以看到有几个`<div>`在适当的位置。第一个是 ID 为`qunit`的，框架使用它来显示测试结果的用户界面。第二个`<div>`，ID 是`qunit-fixture`，应该是你这个开发者用的。该元素允许开发人员测试在 DOM 中添加、编辑或删除元素的代码，而不必担心在每次测试后清理 DOM。如果您将代码创建的元素放在这个`<div>`中，QUnit 会为我们完成重置。

最后，我们包含了一个代表包含测试的文件的`tests.js`文件。我的建议是在实际项目中使用一个文件来存储你的测试。在我为本教程制作的现场演示中，我使用了 JSBin，当然它不允许文件上传。因此，在演示中，你会看到我已经内联了测试代码。

现在您已经知道了标记的每个部分的含义，在浏览器中打开`index.html`页面，看看会发生什么。

如果一切顺利，您应该会看到下面的现场演示所示的界面，它也是作为 JSBin 提供的[:](http://jsbin.com/kuvaliyu/1/edit?html,output)

[QUnit 示例](http://jsbin.com/kuvaliyu/1/embed?html,output)

在这个阶段，这个界面唯一与我们相关的部分是显示 QUnit 在处理测试上花费的时间、定义的断言数量以及通过和失败的测试数量的部分。上面的演示表明我们还没有定义任何测试。让我们解决这个问题。

## 如何用 QUnit 创建测试

QUnit 提供了两种创建新测试的方法:`QUnit.test()`和`QUnit.asyncTest()`。前者用于测试同步运行的代码，后者用于测试异步代码。在这一节中，我将描述如何为同步代码创建测试。

`QUnit.test()`方法的签名是:

```
QUnit.test(name, testFunction)
```

第一个参数`name`，是一个帮助我们识别所创建的测试的字符串。第二个参数`testFunction`是包含框架将执行的断言的函数。框架向该函数传递一个参数，该参数公开了 QUnit 的所有断言方法。

将这个描述放到代码中，我们可以用下面的代码更新文件`tests.js`:

```
QUnit.test('My first test', function(assert) {
   // Assertions here...
});
```

这段代码创建了一个由字符串“My first test”标识的新测试和一个具有空主体的函数。添加一个没有任何断言的测试没有任何用处。要解决这个问题，我们必须学习 QUnit 中可用的断言方法。

## QUnit 的断言方法

断言是软件测试的核心。它们使我们能够验证我们的代码是否如预期的那样工作。在 QUnit 中，我们有很多方法来验证这些期望。它们可以在测试中通过传递给`QUnit.test()`方法的函数的参数来访问(在我们之前的例子中是`assert`)。

下面的列表总结了可用的方法，以及它们的特征和用途:

*   `deepEqual(value, expected[, message])`:递归的、严格的比较，适用于所有 JavaScript 类型。如果`value`和`expected`在属性、值方面是相同的，并且它们具有相同的原型，则断言通过；
*   `equal(value, expected[, message])`:使用非严格比较(`==`，验证提供的`value`等于`expected`参数。
*   `notDeepEqual(value, expected[, message])`:与`deepEqual()`相同，但测试不相等；
*   `notEqual(value, expected[, message])`:与`equal()`相同，但测试不相等；
*   `propEqual(value, expected[, message])`:一个对象的属性和值的严格比较。如果所有属性和值都相同，则断言通过；
*   `strictEqual(value, expected[, message])`:通过严格比较(`===`，验证提供的`value`等于`expected`参数；
*   `notPropEqual(value, expected[, message])`:与`propEqual()`相同，但测试不相等；
*   `notStrictEqual(value, expected[, message])`:与`strictEqual()`相同，但测试不相等；
*   如果第一个参数为真，则断言通过；
*   `throws(function [, expected ] [, message ])`:测试回调是否抛出异常，可选比较抛出的错误；

这些方法接受的参数的含义描述如下:

*   `value`:函数、方法返回的值，或者存储在变量中的值，需要验证；
*   `expected`:要测试的值。在使用`throws()`方法的情况下，这可以是一个<q cite="http://api.qunitjs.com/throws/">错误对象(实例)、错误函数(构造函数)、匹配(或部分匹配)字符串表示的正则表达式，或者一个必须返回 true 以通过断言检查的回调函数</q>；
*   `message`:描述断言的可选字符串；
*   `function`:应该返回错误的要执行的函数；

现在您已经知道了可用的方法和参数，是时候看一些代码了。我将尝试重现一个更真实的例子，而不是为一个函数编写几个测试。无论如何，我将展示给你的测试应该被认为是一个完整的测试套件，但是它们应该给你一个从哪里开始的具体想法。

为了编写上面提到的测试，我们需要定义一些要测试的代码。在这种情况下，我将如下定义一个对象文字:

```
var App = {
   max: function() {
      var max = -Infinity;
      for (var i = 0; i < arguments.length; i++) {
         if (arguments[i] > max) {
            max = arguments[i];
         }
      }

      return max;
   },
   isOdd: function(number) {
      return number % 2 !== 0;
   },
   sortObj: function(array) {
      array.sort(function(a, b) {
         var date1 = new Date(a.timestamp).getTime();
         var date2 = new Date(b.timestamp).getTime();

         if (date1 < date2) {
            return -1;
         } else if (date1 === date2) {
            return 0;
         } else {
            return 1;
         }
      });
   }
};
```

如您所见，我们已经定义了一个包含三个函数的对象文字:`max()`、`isOdd()`和`sortObj()`。第一个函数接受任意数量的参数，并返回最大值。`isOdd()`接受一个数字作为它的参数，并测试它是否是奇数。`sortObj()`接受一个对象数组，这些对象应该有一个名为`timestamp`的属性，并根据这个属性的值对它们进行排序。

这些功能的一组可能的测试如下所示:

```
QUnit.test('max', function (assert) {
    assert.strictEqual(App.max(), -Infinity, 'No parameters');
    assert.strictEqual(App.max(3, 1, 2), 3, 'All positive numbers');
    assert.strictEqual(App.max(-10, 5, 3, 99), 99, 'Positive and negative numbers');
    assert.strictEqual(App.max(-14, -22, -5), -5, 'All positive numbers');
});

QUnit.test('isOdd', function (assert) {
    assert.ok(App.isOdd(5), '5 is odd');
    assert.ok(!App.isOdd(2), '5 is not odd');
    assert.ok(!App.isOdd(0), '0 is not odd');
    assert.throws(function () {
        App.isOdd(null);
    },
        /The given argument is not a number/,
        'Passing null raises an Error');
    assert.throws(function () {
        App.isOdd([]);
    },
    new Error('The given argument is not a number'),
        'Passing an array raises an Error');
});

QUnit.test('sortObj', function (assert) {
    var timestamp = Date.now();

    var array = [{
        id: 1,
        timestamp: timestamp
    }, {
        id: 3,
        timestamp: timestamp + 1000
    }, {
        id: 11,
        timestamp: timestamp - 1000
    }];

    App.sortObj(array);

    assert.propEqual(array, [{
        id: 11,
        timestamp: timestamp - 1000
    }, {
        id: 1,
        timestamp: timestamp
    }, {
        id: 3,
        timestamp: timestamp + 1000
    }]);
    assert.notPropEqual(App.sortObj(array), array, 'sortObj() does not return an array');
    assert.strictEqual(App.sortObj(array), undefined, 'sortObj() returns
});
```

创建的第一个测试由字符串“max”标识。在这个测试中，您可以看到四个使用了`strictEqual()`方法的断言。我们使用这个方法而不是`equal()`,因为我们想避免下面的断言通过的情况:

```
assert.equal(App.max(0, true), 1);
```

在测试中，我们检查几种不同类型的输入。我想在这个测试中建议的是，尽可能地覆盖最大数量的情况:没有参数，所有正数，所有负数，混合情况。我没有涵盖所有的可能性，但这是一个好的开始。

第二个测试用字符串“isOdd”标识，向您展示了`ok()`和`throws()`的用法。前者在需要验证返回布尔值的函数时很有用，比如我们示例中的`isOdd()`函数。您还可以看到`throws()`方法在起作用。使用`throws()`的断言中最有趣的部分可能不是第一个参数，也就是产生错误的函数(在这些情况下，因为我们传递了一个不正确的参数)，而是第二个参数的变化。事实上，我使用了正则表达式和错误实例。

第三个也是最后一个测试由字符串“sortObj”标识，它将其他断言方法付诸实施。第一个断言使用`propEqual()`来验证传递给`sortObj()`函数的数组按照我们期望的顺序返回包含相同对象(相同属性和值)的数组。在这个测试中，`deepEqual()`方法也是一个很好的选择，因为预期的参数与输入数组是相同的(相同的属性、值和原型)。我可能没有使用`strictEqual()`，因为它们是*而不是*同一个对象，也就是说两个对象指向同一个内存地址。
第二个断言有点天真，只是为了展示`notPropEqual()`的用法。这很幼稚，因为我们已经在第三个断言中使用`strictEqual()`方法以更精确的方式验证了期望值。

你喜欢这个例子吗？除了方法的签名之外，你还学到了什么新东西吗？我希望如此。在结束本教程之前，还有一件事要讨论。

## 设定期望

当创建一个测试时，设置我们期望执行的断言的数量是一个最佳实践。通过这样做，如果一个或多个断言没有被执行，测试将会失败。QUnit 框架为此提供了`expect()`方法。这种方法在处理异步代码时特别有用，但是最好在测试同步函数时也使用它。`expect()`方法的签名是:

```
expect(assertionsNumber)
```

其中`assertionsNumber`参数指定预期断言的数量。

有了这个新概念的知识，让我们更新我们的测试来设置我们期望运行的断言的数量:

```
QUnit.test('max', function(assert) {
   expect(4);

   // Assertions here...
});

QUnit.test('isOdd', function(assert) {
   expect(5);

   // Assertions here...
});

QUnit.test('sortObj', function(assert) {
   expect(3);

   // Assertions here...
});
```

代码的现场演示，包括对`expect()`的调用，如下所示，并且[可以作为 JSBin](http://jsbin.com/tusuvixi/1/edit?html,js,output) 获得。

[QUnit](http://jsbin.com/tusuvixi/1/embed?html,js,output)
入门

## 结论

在本教程中，我向您介绍了测试的神奇世界，尤其是如何用 QUnit 对您的 JavaScript 代码进行单元测试。我们已经看到了建立 QUnit 框架是多么容易，以及它提供了什么方法来测试同步功能。此外，您还了解了框架为测试我们的代码而提供的断言函数集。最后，我提到了设置我们期望运行的断言数量的重要性，以及我们如何使用`expect()`方法来设置它们。我希望您喜欢这篇文章，并考虑将 QUnit 集成到您的项目中。

## 分享这篇文章