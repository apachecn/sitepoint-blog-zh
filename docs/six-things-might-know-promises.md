# 关于承诺，你可能不知道的六件事

> 原文：<https://www.sitepoint.com/six-things-might-know-promises/>

承诺是一个简单的概念，即使你还没有机会使用它们，你可能已经读过了。它们是一种有价值的构造，使异步代码能够以更可读的方式构造，而不是作为一堆嵌套的匿名函数。这篇文章涉及了你可能不知道的关于承诺的六件事。

在进入列表之前，这里有一个 JavaScript 承诺的快速提示:

```
var p = new Promise(function(resolve, reject) {
resolve("hello world");
});

p.then(function(str) {
alert(str);
});
```

## 1.`then()`返回一个分叉的承诺

下面两块代码有什么区别？

```
// Exhibit A
var p = new Promise(/*...*/);
p.then(func1);
p.then(func2);
```

```
// Exhibit B
var p = new Promise(/*...*/);
p.then(func1)
.then(func2);
```

如果你认为两个代码块是等价的，你可能会认为承诺只不过是回调的一维数组。然而，事实并非如此。对`then()`的每个调用都返回一个分叉的承诺。因此，在图表 A 中，如果`func1()`抛出异常，`func2()`仍将被正常调用。

在附件 B 中，如果`func1()`抛出异常，`func2()`将不会被调用，因为对`then()`的第一次调用返回了一个新的承诺，该承诺由于`func1()`中的异常而被拒绝。结果是`func2()`被跳过了。

要点:承诺可以像复杂的流程图一样分成多条路径。

## 2.回调应该传递结果

当您运行以下代码时，会收到什么警报？

```
var p = new Promise(function(resolve, reject) {
resolve("hello world");
});

p.then(function(str) {})
.then(function(str) {
alert(str);
});
```

第二个`then()`中的警报不显示任何内容。这是因为在承诺的背景下，回调与其说是回调，不如说是结果的变形。promise 希望您的回调要么返回相同的结果，要么返回替换结果，然后传递给下一个回调。

这种想法类似于使用适配器来转换结果，如下例所示。

```
var feetToMetres = function(ft) { return ft*12*0.0254 };

var p = new Promise(/*...*/);

p.then(feetToMetres)
.then(function(metres) {
alert(metres);
});
```

## 3.仅捕获以前级别的异常

这两个代码块的区别是什么:

```
// Exhibit A
new Promise(function(resolve, reject) {
resolve("hello world");
})
.then(
function(str) {
throw new Error("uh oh");
},
undefined
)
.then(
undefined,
function(error) {
alert(error);
}
);
```

```
// Exhibit B
new Promise(function(resolve, reject) {
resolve("hello world");
})
.then(
function(str) {
throw new Error("uh oh");
},
function(error) {
alert(error);
}
);
```

在图表 A 中，当在第一个`then()`中抛出异常时，它会在第二个`then()`中被捕获，并发出“哦哦”的警报。这遵循的规则是，只捕获先前级别的异常。

在图表 B 中，回调和错误回调在同一层，这意味着当异常在回调中抛出时，它不会被捕获。事实上，只有当承诺
处于拒绝状态或者承诺本身抛出异常时，Exhibit B 的错误回调才会执行。

## 4.错误可以从

在错误回调中，如果您不重新抛出错误，promise 将假设您已经从错误中恢复，并将恢复到已解决的状态。在下面的例子中，因为第一个`then()`中的错误回调没有重新抛出异常，所以显示“我被保存了”。

```
var p = new Promise(function(resolve, reject) {
reject(new Error("pebkac"));
});

p.then(
undefined,
function(error) { }
)
.then(
function(str) {
alert("I am saved!");
},
function(error) {
alert("Bad computer!");
}
);
```

承诺可以被看作是洋葱上的几层。每个`then()`都给洋葱增加了一层。每一层代表一个可以处理的活动。这一层结束后，结果被认为是固定的，并为下一层做好准备。

## 5.承诺可以暂停

仅仅因为你已经在一个`then()`函数中执行了，并不意味着你不能暂停它来完成其他的事情。要暂停当前的承诺，或者让它等待另一个承诺的完成，只需从`then()`内返回另一个承诺。

```
var p = new Promise(/*...*/);

p.then(function(str) {
if(!loggedIn) {
return new Promise(/*...*/);
}
})
.then(function(str) {
alert("Done.");
})
```

在前面的代码示例中，在新的承诺得到解决之前，不会显示警告。这是在现有异步代码路径中引入附加依赖项的一种便捷方式。例如，您可能会发现用户会话已超时，并且您可能希望在继续之前的代码路径之前启动辅助登录。

## 6.下定决心的承诺不会立即兑现

当您运行以下代码时，会收到什么警报？

```
function runme() {
var i = 0;

new Promise(function(resolve) {
resolve();
})
.then(function() {
i += 2;
});
alert(i);
}
```

您可能认为它会提醒 2，因为承诺会立即得到解决，并且`then()`函数会立即(同步)执行。然而，promise 规范要求所有调用都是强制异步的，以达到统一。因此，在修改`i`的值之前调用警报。

链接:
下载 [Promise/A+ API](https://github.com/promises-aplus/promises-spec/blob/master/implementations.md) 的各种实现。

## 分享这篇文章