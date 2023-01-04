# 掌握 AngularJS 中的$watch

> 原文：<https://www.sitepoint.com/mastering-watch-angularjs/>

*这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

通过三种不同的“观察”方法，AngularJS 提供了许多使用[发布-订阅模式](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)的不同选项。它们中的每一个都带有可选的参数，这些参数可以修改它的行为。

关于`$watch`的官方文档一点也不全面:毕竟，这个问题一直困扰着 AngularJS v1。甚至解释如何进行的网上资源也是分散的。

因此，最终，开发人员很难为给定的情况选择正确的方法。对于 AngularJS 初学者来说尤其如此！结果可能令人惊讶或不可预测，这不可避免地会导致错误。

在这篇文章中，我将假设你对 AngularJS 的概念有些熟悉。如果你觉得需要复习一下，你可能会想读一下 [$scope](https://docs.angularjs.org/guide/scope) 、 [binding](https://www.sitepoint.com/angularjs-tutorial-build-an-app-using-directives-and-data-binding/) 和[、$apply 和$digest](https://www.sitepoint.com/understanding-angulars-apply-digest/) 。

## 检查你的理解

例如，观察数组的第一个元素的最好方法是什么？假设我们在作用域上声明了一个数组，`$scope.letters = ['A','B','C'];`

*   当我们向数组中添加元素时,`$scope.$watch('letters', function () {...});`会触发回调函数吗？
*   当我们改变它的第一个元素时会吗？
*   那`$scope.$watch('letters[0]', function () {...});`呢？效果会一样，还是更好？
*   上面，数组元素是原始值:如果我们用相同的值替换第一个元素会怎么样？
*   现在假设数组保存对象:会发生什么？
*   `$watch`、`$watchCollection`、`$watchGroup`有什么区别？

如果你对所有这些问题感到困惑，请继续阅读。我的目的是通过几个例子尽可能清楚地说明这一点，引导你一路前行。

## $scope。$手表

先说`$scope.$watch`。这是所有手表功能的核心:我们将看到的其他方法只是`$watch`的一个方便快捷的方式。

### $小心

现在，Angular 的伟大之处在于，您可以使用相同的机制显式地在控制器中执行由数据更改触发的复杂操作。例如，您可以在一些数据上设置一个观察器，这些数据会随着以下情况而变化:

1.  超时设定
2.  用户界面
3.  由 web 工作者执行的复杂异步计算
4.  Ajax 调用

您可以只设置一个侦听器来处理任何数据更改，不管是什么原因导致的。

然而，要做到这一点，你需要亲自打电话给`$scope.$watch`。

### 把手放在某物或者某人身上

让我们来看看`$rootscope.watch()`的[代码](https://github.com/angular/angular.js/blob/9cf6b197ab905fd94377148a2d2f04f6c768f9c9/src/ng/rootScope.js)。

这是它的签名:`function(watchExp, listener, objectEquality, prettyPrintExpression)`。

详细来说，它的四个参数:

1.  **`watchExp`** 被人注视的表情。它可以是一个函数，也可以是一个字符串，在每个摘要周期都会被求值。

    这里要注意的一个关键方面是，*如果*表达式被计算为一个函数，那么这个函数需要是幂等的。换句话说，对于同一组输入，它应该总是返回相同的输出。如果不是这种情况，Angular 将假设正在观察的数据已经改变。反过来，这意味着它将不断检测差异，并在摘要周期的每次迭代中调用侦听器。

2.  **`listener`** 一个回调，在手表第一次设置时触发，然后每次在摘要周期中检测到`watchExp`的值发生变化时触发。对 setup 的初始调用是为了存储表达式的初始值。

3.  **`objectEquality`** 当且仅当，这是真的观察者才会进行深度比较。否则，它执行浅层比较，即只比较引用。

    我们以一个数组为例:`$scope.fruit = ["banana", "apple"];`。

    `objectEquality == false`意味着只有重新分配到水果区才会产生对听者的呼叫。

    我们还需要检查“有多深”是一个深度比较:我们稍后将会看到。

4.  `prettyPrintExpression`
    如果通过了，它就覆盖了观看表情。该参数是**而不是用于对`$watch()`的正常调用的**；它由[表达式解析器](https://github.com/angular/angular.js/commit/0d424263ead16635afb582affab2b147f8e71626)内部使用。

    **小心**:正如你自己所看到的，当错误地传递第四个参数时，很容易遇到意想不到的结果。

现在我们准备回答导言中的一些问题。看看我们在这一部分的例子:

查看钢笔[Angular $手表演示–$ scope。$watch()](http://codepen.io/SitePoint/pen/GoYyzB/) 由 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )提供。