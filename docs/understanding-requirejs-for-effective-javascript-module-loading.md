# 理解有效加载 JavaScript 模块的要求

> 原文：<https://www.sitepoint.com/understanding-requirejs-for-effective-javascript-module-loading/>

模块化编程用于将大型应用程序分解成更小的可管理代码块。基于模块的编码简化了维护工作，增加了可重用性。然而，管理模块之间的依赖关系是开发人员在整个应用程序开发过程中面临的主要问题。RequireJS 是管理模块间依赖关系的最流行的框架之一。本教程考察了模块化代码的必要性，并展示了 RequireJS 如何提供帮助。

## 加载 JavaScript 文件

大型应用程序通常需要大量的 JavaScript 文件。一般都是用`<script>`标签一个一个加载。此外，每个文件都可能依赖于其他文件。最常见的例子是 jQuery 插件，它们都依赖于核心 jQuery 库。因此，jQuery 必须在它的任何插件之前加载。让我们看一个在实际应用程序中加载 JavaScript 文件的简单例子。假设我们有以下三个 JavaScript 文件。

**T2`purchase.js`**

```
function purchaseProduct(){
console.log("Function : purchaseProduct");

var credits = getCredits();
if(credits > 0){
reserveProduct();
return true;
}
return false;
}
```

**T2`products.js`**

```
function reserveProduct(){
console.log("Function : reserveProduct");

return true;
}
```

**T2`credits.js`**

```
function getCredits(){
console.log("Function : getCredits");

var credits = "100";
return credits;
}
```

在这个例子中，我们试图购买一个产品。首先，它检查是否有足够的信用额度来购买产品。然后，在信用验证时，它保留产品。另一个脚本`main.js`，通过调用`purchaseProduct()`初始化代码，如下所示。

```
var result = purchaseProduct();
```

### 什么会出错？

在这个例子中，`purchase.js`依赖于`credits.js`和`products.js`。因此，需要在调用`purchaseProduct()`之前加载那些文件。那么，如果我们按照下面的顺序包含 JavaScript 文件，会发生什么呢？

```
<script src="products.js"></script>
<script src="purchase.js"></script>
<script src="main.js"></script>
<script src="credits.js"></script>
```

这里，初始化是在加载`credits.js`之前完成的。这将导致如下所示的错误。这个例子只需要三个 JavaScript 文件。在一个更大的项目中，事情很容易失去控制。这就是 RequireJS 发挥作用的地方。

![Plain JavaScript Example](img/4c7e0d7f7661ca1a3789dd7589e06647.png)

## 要求介绍

RequireJS 是一个众所周知的 JavaScript 模块和文件加载器，在最新版本的流行浏览器中受支持。在 RequireJS 中，我们将代码分成模块，每个模块处理一个单独的职责。此外，加载文件时需要配置依赖关系。让我们从下载 [RequireJS](http://requirejs.org/docs/download.html) 开始吧。下载完成后，将文件复制到项目文件夹中。让我们假设我们的项目的目录结构现在类似于下图。

![Folder Structure](img/3147d60292388cabde04a3235c094886.png)

所有的 JavaScript 文件，包括 RequireJS 文件，都位于`scripts`文件夹中。文件`main.js`用于初始化，其他文件包含应用程序逻辑。让我们看看脚本是如何包含在 HTML 文件中的。

```
<script data-main="scripts/main" src="scripts/require.js"></script>
```

这是使用 RequireJS 包含文件所需的唯一代码。您可能想知道其他文件发生了什么，它们是如何被包含进来的。属性定义了应用程序的初始化点。这种情况下就是`main.js`。RequireJS 使用`main.js`来寻找其他脚本和依赖项。在这种情况下，所有文件都位于同一个文件夹中。使用逻辑，您可以将文件移动到任何您喜欢的文件夹。现在，我们来看看`main.js`。

```
require(["purchase"],function(purchase){
purchase.purchaseProduct();
});
```

在 RequireJS 中，所有代码都包装在`require()`或`define()`函数中。这些函数的第一个参数指定了依赖关系。在前面的例子中，初始化依赖于`purchase.js`，因为它定义了`purchaseProduct()`。请注意，文件扩展名已被省略。这是因为 RequireJS 只考虑`.js`文件。

`require()`的第二个参数是一个匿名函数，它采用一个对象来调用相关文件中的函数。在这个场景中，我们只有一个依赖项。可以使用以下语法加载多个依赖项。

```
require(["a","b","c"],function(a,b,c){
});
```

## 使用 RequireJS 创建应用程序

在这一节中，我们将把上一节中讨论的普通 JavaScript 示例转换成 RequireJS。我们已经讨论了`main.js`，所以让我们继续讨论其他文件。

**T2`purchase.js`**

```
define(["credits","products"], function(credits,products) {

console.log("Function : purchaseProduct");

return {
purchaseProduct: function() {

var credit = credits.getCredits();
if(credit > 0){
products.reserveProduct();
return true;
}
return false;
}
}
});
```

首先，我们声明购买功能依赖于信用和产品。在`return`语句内部，我们可以定义每个模块的功能。在这里，我们对传递的对象调用了`getCredits()`和`reserveProduct()`函数。`product.js`和`credits.js`类似，如下图所示。

**T2`products.js`**

```
define(function(products) {
return {
reserveProduct: function() {
console.log("Function : reserveProduct");

return true;
}
}
});
```

**T2`credits.js`**

```
define(function() {
console.log("Function : getCredits");

return {
getCredits: function() {
var credits = "100";
return credits;
}
}
});
```

这两个文件都被配置为独立的模块，这意味着它们不依赖于任何东西。需要注意的重要一点是使用了`define()`而不是`require()`。在`require()`和`define()`之间进行选择取决于代码的结构，这将在下一节讨论。

## 使用`require()`与`define()`

前面我提到过，我们可以同时使用`require()`和`define()`来加载依赖项。理解这两个功能之间的区别对于管理依赖关系至关重要。`require()`功能用于运行即时功能，而`define()`用于定义在多个位置使用的模块。在我们的例子中，我们需要立即运行`purchaseProduct()`函数。于是，`require()`被用在了`main.js`里面。然而，其他文件是可重用的模块，因此使用`define()`。

## 为什么要求很重要

在普通的 JavaScript 示例中，由于文件加载顺序不正确而产生了一个错误。现在，删除 RequireJS 示例中的`credits.js`文件，看看它是如何工作的。下图显示了浏览器检查工具的输出。

![RequireJS Example](img/c7de3acaab8760478e77bd73e7af375d.png)

这里的区别在于 RequireJS 示例中没有执行任何代码。我们可以确认，因为控制台上什么也没有显示。在普通的 JavaScript 示例中，在生成错误之前，我们在控制台上打印了一些输出。RequireJS 在执行该功能之前会一直等待，直到加载了所有的依赖模块。如果缺少任何模块，它不会执行任何代码。这有助于我们保持数据的一致性。

## 管理相关文件的顺序

RequireJS 使用**异步模块加载** (AMD)来加载文件。每个依赖模块将以给定的顺序通过异步请求开始加载。即使考虑了文件顺序，由于异步的本质，我们也不能保证第一个文件在第二个文件之前被加载。因此，RequireJS 允许我们使用`shim`配置来定义需要以正确顺序加载的文件序列。让我们看看如何在 RequireJS 中创建配置选项。

```
requirejs.config({
shim: {
'source1': ['dependency1','dependency2'],
'source2': ['source1']
}
});
```

RequireJS 允许我们使用`config()`函数提供配置选项。它接受一个名为`shim`的参数，我们可以用它来定义强制性的依赖序列。您可以在 [RequireJS API 文档](http://requirejs.org/docs/api.html#config)中找到完整的配置指南。

```
define(["dependency1","dependency2","source1","source2"], function() {

);
```

在正常情况下，这四个文件将按照给定的顺序开始加载。这里，`source2`取决于`source1`。因此，一旦`source1`完成加载，`source2`就会认为所有的依赖项都已加载。不过，`dependency1`和`dependency2`可能还在加载。使用 shim 配置，必须在`source1`之前加载依赖项。因此，不会产生错误。

## 结论

希望这篇教程能帮助你入门 RequireJS。虽然看起来很简单，但在管理大型 JavaScript 应用程序中的依赖关系时，它确实非常强大。单靠本教程不足以涵盖 RequireJs 的所有方面，所以希望你使用官网学习所有的高级配置和技术。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，比如 [Simply JavaScript](https://learnable.com/books/simply-javascript?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。有关于 JavaScript 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章