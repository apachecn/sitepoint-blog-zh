# KnockoutJS 初学者指南:第 1 部分

> 原文：<https://www.sitepoint.com/beginners-guide-to-knockoutjs-part-1/>

## KnockoutJS 初学者指南:基础和观察

几乎每个处理 web 技术的人都知道 jQuery，或者至少听说过它。它无与伦比的简单和简洁让全世界数百万 web 开发人员的生活变得更加容易——这太棒了。

不幸的是，jQuery 并不能解决所有问题。一旦你决定创建一些更复杂的 web 应用程序，你就会遇到一个问题——没有简单的方法让你的 UI 和数据动态地相互通信。对于 jQuery 提供的底层 DOM 操作和事件处理，这很难实现。您需要一个库，为您提供 UI 和底层数据模型之间更复杂的通信方式。

这就是[击倒](http://knockoutjs.com/)的地方。Knockout 是一个 JavaScript 库，帮助创建丰富的、类似桌面的 web UIs。它简化了用户交互，并使界面完全响应任何数据源的变化。Knockout 提供了一种简单的双向绑定机制来将数据模型链接到 UI，从而使它们之间的同步变得轻而易举。

虽然您将需要在 jQuery 中同时使用 Knockout，但在某些情况下，如动画过渡，Knockout 本身并不依赖于它。你需要明白的另一件事是 Knockout 并不与 jQuery 竞争——它们都做得很好；每个人都有自己的方向。正如你将看到的，如果你想获得最大的利益，你应该一起使用它们。

在本教程中，我们从 Knockout 的核心概念和功能开始。在第二部分中，我们将深入探讨内置绑定以及如何使用它们。最后，我们将介绍一些高级特性和技术，比如扩展 observables 和如何创建自己的自定义绑定。我们开始吧！

## 基本概念

在我们浏览代码示例之前，首先您可能需要掌握一些基本概念。Knockout 实现了 JavaScript 的模型-视图-视图模型(MVVM)设计模式。在这个模式中，您的应用程序分为三个部分:

保存应用程序数据的模型。这可以是用户输入的数据，也可以是从 web 服务器获取的 JSON 数据。

充当模型和视图之间的连接器和通信层的视图。它保存数据和操作这些数据的操作，并在 UI 中显示这些数据。每次当数据模型改变时，相应的 UI 部件更新，反映这些变化。应用程序中的视图模型由 JavaScript 代码表示。

引用应用程序中所有 UI 元素的视图。它是给定 UI 的结构和外观的表示。视图负责显示数据和接受用户输入。视图由应用程序中的 HTML/CSS 代码表示。

它们是 Knockout 构建时的三个核心概念:

**1。声明性绑定:**这些绑定允许您以简单方便的方式将部分 UI 连接到数据模型。当直接使用 JavaScript 操作 DOM 时，如果您以后更改 DOM 层次结构或元素 id，这可能会导致代码中断。使用声明性绑定，即使您更改了 DOM，所有绑定的部分仍然保持连接。只需在任何 DOM 元素中包含一个数据绑定属性，就可以将数据绑定到 DOM。

**2。依赖跟踪:**感谢绑定和称为 observables 的特殊类型的变量，每次当你的模型数据改变时，与之相关的所有部分都会自动更新。无需担心添加事件处理程序和侦听器。所有这些额外的工作都是由 Knockout 和 observables 在内部完成的，它们会在底层值发生变化时通知侦听器。

**3。模板:**当您的应用程序变得更加复杂，并且您需要一种方法来显示视图模型数据的丰富结构，从而保持您的代码简单时，这就很方便了。Knockout 有一个本地的内置模板引擎，您可以立即使用。但是如果您愿意，也可以使用第三方模板引擎，如 jquery.tmpl 或下划线。

如果这些理论对你来说听起来晦涩难懂，不要担心。当我们浏览教程和代码示例时，一切都会变得更加清晰。

## 入门指南

在我们深入了解 Knockout 之前，您需要[下载](https://github.com/SteveSanderson/knockout/downloads)并在您的 HTML 文档中引用该库。

```
<script type='text/javascript' src='knockout-2.0.0.js'></script> 
```

为了将代码与表示分开，最好创建一个 JavaScript 文件来保存所有的应用程序代码。因为我们将在某些情况下使用 jQuery，所以您也需要引用它。

```
<script type='text/javascript' src='jquery-1.7.1.min.js'></script>
<script type='text/javascript' src='knockout-2.0.0.js'></script>
<script type='text/javascript' src='application.js'></script> 
```

这被认为是最佳实践，但出于培训的目的，为了使事情变得更容易，您可以将 JavaScript 代码放在同一个文档中，方法是将它包含在 head 标记中或者放在您的标记下面。

现在，要创建视图模型，只需像这样声明任何 JavaScript 对象:

```
 function viewModel() {

   // Your code here

  };
```

数据绑定属性(稍后解释)不是 HTML 固有的，浏览器不知道它的意思。因此，为了生效，必须通过在脚本末尾插入`ko.applyBindings()`功能来激活敲除。此外，如果您使用外部 JavaScript 文件，或者您的脚本被放在文档的 head 标签中，您需要在 jQuery ready 函数中包装剔除代码，以便正常工作。下面是开始的基本模板:

```
 $(document).ready(function(){

  function viewModel() {

   // Your code here

  };

  ko.applyBindings(new viewModel()); 

});
```

调用`ko.applyBindings()`方法并传入我们的视图模型，告诉 Knockout 将指定的模型绑定到我们的 UI。如果您只想将这个视图模型绑定到整个 UI 的一部分，甚至可以提供一个 DOM 元素。`ko.applyBindings()`需要两个参数。第一个参数表示您希望将哪个视图模型对象与它激活的声明性绑定一起使用。第二个参数是可选的，它定义了要在文档的哪个部分搜索数据绑定属性。例如，`ko.applyBindings(viewModel, document.getElementById('container'))`将限制 ID 为 container 的元素及其后代的激活。如果您希望拥有多个视图模型，并将每个模型与页面的不同区域相关联，这将非常有用。

## 它是如何工作的

使用 Knockout，您可以通过在标记中包含指定要执行的数据绑定的数据绑定属性，将数据绑定到 DOM 元素。该代码从不引用 DOM 结构，因此您可以自由地更改 HTML，而不会破坏绑定。在以下示例中，我们将文本数据绑定属性添加到`span`元素，如下所示:

```
 // syntax: data-bind="bindingName: bindingValue"
<p>The day of the week is <span data-bind="text: dayOfWeek"></span>. It's time for <span data-bind="text: activity"></span></p> 
```

然后，如果我们想让文本的值动态更新，那么我们必须在视图模型中将其声明为可观察值。

```
function viewModel() {
  this.dayOfWeek = ko.observable('Sunday');
  this.activity = ko.observable('rest');
};

ko.applyBindings(new viewModel()); 

```

这将输出“星期几是星期天”。该休息了”。

## 看得见的

挖空通过用名为`ko.observable()`的自定义函数包装对象属性来实现可观察属性。

```
this.property = ko.observable('value')
```

可观测量被设置为函数。因此，您可以按以下方式使用它们:

```
 // To read the observable's current value, just call the observable with no parameters.
// The following will return Sunday
this.dayOfWeek()

// To write a new value to the observable, call the observable and pass the new value as a parameter.
// The following will change the day of week to Monday
this.dayOfWeek('Monday')

// To write values to multiple observable properties on a model object, you can use chaining syntax.
this.dayOfWeek('Monday').activity('work') 
```

击倒不需要你使用可观察的属性。如果您希望 DOM 元素只接收一次值，但是当源对象中的值改变时不更新，那么简单的对象就足够了。但是，如果您希望源对象和目标 DOM 元素保持同步——双向绑定——那么您应该考虑使用可观察属性。

在某些情况下，您可能需要将两个或多个可观察值合并成一个新值。这可以通过所谓的计算可观测量来实现。计算可观测量是依赖于一个或多个其他可观测量的函数，并且将在这些依赖关系发生变化时自动更新。计算属性在其评估所依赖的任何可观察值发生变化时会自动更新。在下面的例子中，每次当一个或多个`day`、`month`和`year`可观察值改变时，名为`fullDate`的计算可观察值将更新。

```
 <p>Day: <input data-bind="value: day" /></p>
<p>Month: <input data-bind="value: month" /></p>
<p>Year: <input data-bind="value: year" /></p>
<p>The current date is <span data-bind="text: fullDate"></span></p>

function viewModel() {
  this.day = ko.observable('24');
  this.month = ko.observable('02');
  this.year = ko.observable('2012');

  this.fullDate = ko.computed(function() {
   return this.day() + "/" + this.month() + "/" + this.year();
  },this);
};

ko.applyBindings(new viewModel()); 
```

`ko.computed()`采用第二个参数`this`。如果不把它传进去，就不可能提到`this.day()`、`this.month()`或`this.year()`。为了简化事情，您可以创建一个变量`self`，从而避免添加第二个参数。从现在开始，我们将在代码示例中使用这种方法。

```
function viewModel() {
  var self = this;
  self.day = ko.observable('24');
  self.month = ko.observable('02');
  self.year = ko.observable('2012');

  self.fullDate = ko.computed(function() {
   return self.day() + "/" + self.month() + "/" + self.year();
  });
};

ko.applyBindings(new viewModel()); 
```

当你处理一个对象时，你可以很容易地通过把它变成一个可观察的对象来跟踪它的任何变化。但是如果你有多个对象呢？在这种情况下，Knockout 有一个名为`ko.observableArray()`的特殊对象，它可以检测并响应一组事物的变化。这使得显示和/或编辑多个值成为可能，例如，当您需要 UI 的重复部分随着项目的添加和删除而出现和消失时。

您应该记住，可观察数组跟踪数组中的对象，而不是这些对象的状态。简单地将一个对象放入一个可观测的数组中并不能使该对象的所有属性本身都是可观测的。如果你愿意，你可以让这些属性可见，但这完全取决于你。一个可观察的数组只是跟踪它持有的对象，并在对象被添加或删除时通知监听器。

```
this.property = ko.observableArray();
```

当你创建一个可观察的数组时，你可以让它为空，或者用一些初始值填充它。在以下示例中，我们创建了一个用一周中的各天填充的可观察数组:

```
 <p>Today is <span data-bind="text: daysOfWeek()[0]"></span></p>

function viewModel() {
  var self = this;
  self.daysOfWeek = ko.observableArray([
  'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'
  ]);
  alert("The week has " + self.daysOfWeek().length + " days");
};

ko.applyBindings(new viewModel());
```

如您所见，要读取和写入剔除数组，您可以使用任何本地 JavaScript 函数。但是 Knockout 有它自己的等价函数，它的语法更方便一些:

```
array().push('Some value'); // native JavaScript

array.push('Some value'); // Knockout
```

有关可用功能的完整列表，您可以查看[文档](http://knockoutjs.com/documentation/observableArrays.html)。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如[网页 JavaScript 编程](https://learnable.com/courses/javascript-programming-for-the-web-40)。

## 分享这篇文章