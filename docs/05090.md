# QUnit 高级概念:模块和配置

> 原文：<https://www.sitepoint.com/qunit-advanced-concepts-modules-configuration/>

在过去的几周里，我已经在教程[<cite>【QUnit 入门】</cite>](https://www.sitepoint.com/getting-started-qunit/) 和 [<cite>如何在 QUnit</cite>](https://www.sitepoint.com/test-asynchronous-code-qunit) 中测试异步代码中介绍了 QUnit 的几个特性。我描述了如何设置 QUnit 测试框架来开始测试您的 JavaScript 项目，什么是 assert，QUnit 提供了什么方法，以及如何为同步或异步代码创建测试。

在本文中，我将讨论如何在模块中组织测试代码，以及如何使用框架提供的配置属性调整 QUnit 以更好地满足您的需求。

## 在模块中组织 QUnit

将项目组织成更小、更易管理的部分的能力在软件开发中并不是一个新概念。开发人员总是努力通过将代码库分成多个文件或模块来保持代码的简单和有序。测试也不例外。保持我们的测试有组织并且在多个模块中，特别是如果我们为一个大项目编写测试，这是非常有用的，并且通常增强了它的可维护性。

QUnit 提供了一个名为`QUnit.module()`的方法，允许我们将测试分组到模块中。此方法的签名如下所示:

```
QUnit.module(name[, lifecycle])
```

`name`参数是一个用于识别模块的字符串，而`lifecycle`是一个包含两个可选函数的对象，在每次测试之前(*设置*)和之后(*拆卸*)运行。

要指定哪些测试属于给定的模块，您不需要像这样对测试进行任何包装:

```
// No, it's not like that!
QUnit.module('My first module, {
setup: function() {},
teardown: function() {},
tests: function() {
// Tests here
}
});
```

一个测试属于一个给定的模块，如果它是在一个对`QUnit.module()`的调用之后，但在另一个对`QUnit.module()`的调用被发现之前定义的。在下一个例子中，我们有属于“模块 1”的名为“测试 1”和“测试 2”的测试，以及属于“模块 2”的另一个测试“测试 3”。

```
// It's like that and that's the way it is
QUnit.module('Module 1');

QUnit.test('Test 1', function(assert) {
// assertions here
});

QUnit.test('Test 2', function(assert) {
// assertions here
})

QUnit.module('Module 2');

QUnit.test('Test 3', function(assert) {
// assertions here
});
```

理想情况下，模块名表示项目的一个独立部分。例如，jQuery 库有以下模块:`ajax`、`core`、`css`、`event`、`selector`等。

既然您已经清楚了测试是如何在模块中分组的，那么让我们学习更多关于`setup`和`teardown`函数的知识。假设您想要对以下对象运行几个测试:

```
var myObj = {
name: 'Aurelio De Rosa',
items: []
};
```

您希望确保在执行测试之前，`items`属性被数值`1`、`2`和`3`填充。此外，您希望每次测试结束时，任何不是`name`或`items`的附加属性都从对象中删除。这样的目标可以通过下面的代码来实现:

```
QUnit.module('My module', {
setup: function() {
myObj.items = [1, 2, 3];
},
teardown: function() {
for (var prop in myObj) {
if (prop !== 'name' && prop !== 'items') {
delete myObj[prop];
}
}
}
});

QUnit.test('Test 1', function(assert) {
expect(2);

// Set a new property of the myObj object
myObj.color = 'red';

assert.strictEqual(myObj.items.length, 3, 'The setup function has pushed 3 elements');
assert.strictEqual(myObj.items, [1, 2, 3], 'The setup function has pushed the expected elements');
});

QUnit.test('Test 2', function(assert) {
expect(1);

assert.ok(!myObj.color, 'The teardown function removed the color property');
});
```

这个例子的一个现场演示如下所示，也可以作为 JSfiddle 使用[。](https://jsfiddle.net/033t97dk/)

[https://jsfiddle.net/033t97dk/embedded/](https://jsfiddle.net/033t97dk/embedded/)

现在，让我们看看如何在 QUnit 中创建自定义配置。

## 如何配置 QUnit

QUnit 框架公开了一系列配置属性，我们可以修改这些属性以更好地满足我们项目的需求。该框架提供了适合大多数情况的默认配置，但是我们可以通过更新`QUnit.config`属性来调整它。此属性是包含下列属性的对象(按字母顺序报告):

*   `altertitle`:一个[布尔值](https://www.sitepoint.com/boolean-data-type/)，通过添加一个复选标记或“x”来指定一个测试套件是通过还是失败，从而启用(`true`)或禁用(`false` ) QUnit 来更新测试页面的标题。默认值为`true`。
*   `autostart`:一个布尔值，如果设置为`false`，指定您希望通过调用`QUnit.start()`自己运行测试，而不是在负载事件被触发时运行。默认值为`true`。
*   `hidepassed`:一个布尔值，指定是否应该隐藏通过的测试(`true`)或不隐藏(`false`)。默认值为`false`。
*   `module`:指定要运行的单个模块的字符串。默认值是`undefined`，所以 QUnit 运行所有定义的模块。
*   `reorder`:一个布尔值，指示 QUnit 是否应该首先运行在先前执行中失败的测试(`true`)或者不运行(`false`)。默认值为`true`。
*   `requireExpects`:一个布尔值，指定是否要在定义的每个测试中强制调用`expect()`(`true`)或不调用`false`。默认值为`true`。
*   `testNumber`:按顺序号运行特定测试块的数组。加载测试块时会设置顺序。默认值为`undefined`。
*   `testTimeout`:表示最大执行时间的数字，超过该时间后所有测试将失败。默认值为`undefined`。
*   `scrolltop`:一个布尔值，指定当所有测试都被执行(`true`)或不被执行(`false`)时，是否要避免 QUnits 转到页面顶部。默认值为`true`。
*   `urlConfig`:一个数组，管理放入 QUnit 工具栏的表单控件。通过扩展这个数组，您可以添加自己的复选框和选择列表。

现在您已经知道了可以修改哪些特性，让我们看看如何使用它们。但是首先，要记住的重要一点是，定制配置必须在包含 QUnit 的 JavaScript 文件之后、定义测试之前编写。

假设您想要强制您团队中的测试人员总是定义要执行的断言的数量。您还希望 QUnit 隐藏已通过的测试，因为 testsuite 非常大。最后，您希望阻止 QUnit 自动滚动到页面顶部。在这种情况下，您可以写:

```
<script src="qunit-1.15.0.js"></script>
<script>
QUnit.config.hidepassed = true;
QUnit.config.requireExpects = true;
QUnit.config.scrolltop = true;
</script>
<script>
QUnit.test('My test', function(assert) {
// assertions go here...
});
</script>
```

在本例中，我们看到了一个基本的自定义配置。您可以扩展它，并创建一个非常复杂的适合您的项目。

## 结论

在本文中，我向您介绍了 QUnit 中的模块，并向您展示了如何创建定制配置。在第一部分中，我们讨论了如何使用`QUnit.module()`方法在 QUnit 中创建一个模块，并学习了框架如何将测试组合在一起。然后，我描述了如何创建在模块中的每个测试之前和之后运行的 setup 和 teardown 函数。在第二部分中，我列出了 QUnit 公开的所有属性，以更改其默认配置，从而更好地满足您的项目需求。

我希望你喜欢这个教程。多亏了这一点，以及我以前的文章，您现在可以开始用 QUnit 测试您的基于 JavaScript 的项目了。

## 分享这篇文章