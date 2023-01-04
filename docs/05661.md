# 了解 Angular 的$apply()和$digest()

> 原文：<https://www.sitepoint.com/understanding-angulars-apply-digest/>

对于专家指导的在线角度训练课程，你不能错过托德座右铭的终极角度。尝试他的课程[在这里](https://ultimateangular.com/)，并使用代码 SITEPOINT 获得 25%的折扣，并帮助支持 SitePoint。

`$apply()`和`$digest()`是 AngularJS 的两个核心，有时也是令人困惑的方面。为了理解 AngularJS 如何工作，我们需要完全理解`$apply()`和`$digest()`是如何工作的。这篇文章旨在解释什么是`$apply()`和`$digest()`，以及它们如何在你的日常 AngularJS 编程中有用。

## `$apply`和`$digest`探索过

AngularJS 提供了一个令人难以置信的功能，被称为双向数据绑定，它极大地简化了我们的生活。数据绑定意味着当你改变视图中的某些东西时，`scope`模型*会自动更新*。类似地，每当`scope`模型改变时，视图就用新的值更新自己。安古拉杰是怎么做到的？当您编写一个表达式(`{{aModel}}`)时，Angular 在幕后为`scope`模型设置一个观察器，每当模型发生变化时，它就会更新视图。这个`watcher`就像你在 AngularJS 设置的任何一个观察器一样:

```
$scope.$watch('aModel', function(newValue, oldValue) {
//update the DOM with newValue
});
```

传递给`$watch()`的第二个参数被称为监听器函数，每当`aModel`的值改变时就会被调用。我们很容易理解，当`aModel`的值改变时，这个监听器被调用，更新 HTML 中的表达式。但是，仍然有一个大问题！Angular 如何确定何时调用这个侦听器函数？换句话说，AngularJS 如何知道`aModel`何时改变，以便调用相应的监听器？它是否定期运行一个函数来检查`scope`模型的值是否已经改变？嗯，这就是`$digest`周期介入的地方。

这是观察者被解雇的周期。当一个观察器被触发时，AngularJS 评估`scope`模型，如果它已经改变，那么相应的监听器函数被调用。所以，我们的下一个问题是这个`$digest`周期何时以及如何开始。

调用`$scope.$digest()`的结果是`$digest`周期开始。假设您通过`ng-click`指令改变了处理函数中的`scope`模型。在这种情况下，AngularJS 通过调用`$digest()`自动触发`$digest`周期。当`$digest`周期开始时，它触发每个观察器。这些观察器检查`scope`模型的当前值是否与上次计算的值不同。如果是，则执行相应的监听器函数。因此，如果视图中有任何表达式，它们将被更新。除了`ng-click`之外，还有其他几个内置的指令/服务，可以让您更改模型(例如`ng-model`、`$timeout`等)并自动触发`$digest`周期。

到目前为止，一切顺利！但是，有一个小问题。在上述情况下，Angular 并不直接调用`$digest()`。相反，它调用`$scope.$apply()`，T1 又调用`$rootScope.$digest()`。结果，一个摘要循环从`$rootScope`开始，随后访问所有子作用域，一路上调用观察器。

现在，让我们假设您将一个`ng-click`指令附加到一个按钮上，并向它传递一个函数名。当点击按钮时，AngularJS 将函数调用包装在`$scope.$apply()`内。因此，您的函数照常执行，更改模型(如果有的话)，并且一个`$digest`循环开始确保您的更改在视图中得到反映。

**注**:自动调用`$rootScope.$digest()`。`$apply()`函数有两种风格。第一个将一个函数作为参数，对其求值，并触发一个`$digest`循环。第二个版本不带任何参数，只是在被调用时开始一个`$digest`循环。我们将很快看到为什么前者是首选方法。

## 什么时候手动调用`$apply()`？

如果 AngularJS 通常将我们的代码包装在`$apply()`中并开始一个`$digest`周期，那么什么时候需要手动调用`$apply()`？实际上，AngularJS 让一件事变得很清楚。它将只考虑那些在 AngularJS 的上下文中完成的模型变更(即变更模型的代码被包装在`$apply()`中)。Angular 的内置指令已经做到了这一点，因此您所做的任何模型更改都会反映在视图中。然而，如果你改变了 Angular 上下文之外的任何模型，那么你需要通过手动调用`$apply()`来通知 Angular 这些改变。这就像告诉 Angular 您正在更改一些模型，它应该触发`watchers`以便您的更改正确传播。

例如，如果您使用 JavaScript 的`setTimeout()`函数来更新一个`scope`模型，Angular 无法知道您可能会更改什么。在这种情况下，手动调用`$apply()`是你的责任，这会触发一个`$digest`循环。类似地，如果您有一个设置 DOM 事件监听器的指令，并且更改了处理函数内部的一些模型，那么您需要调用`$apply()`来确保更改生效。

让我们看一个例子。假设您有一个页面，一旦页面加载，您希望在两秒钟的延迟后显示一条消息。您的实现可能类似于下面清单中所示的 JavaScript 和 HTML。

见[码笔](http://codepen.io)上 Sandeep Panda ( [@Sandeep92](http://codepen.io/Sandeep92) )的笔 [fukyn](http://codepen.io/Sandeep92/pen/fukyn/) 。