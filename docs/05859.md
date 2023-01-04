# 了解淘汰

> 原文：<https://www.sitepoint.com/understanding-knockout/>

KnockoutJS 是一个优雅的 JavaScript 库，基于模型-视图-视图模型模式，帮助我们毫不费力地创建丰富的用户界面。如果您正在开发一个应用程序，其中的部分在底层数据模型发生变化时会动态更新，那么 Knockout 确实可以帮助您。Knockout 中的双向数据绑定和模板特性使得实现动态视图的过程变得轻而易举。本教程将带您开始使用 Knockout，并向您展示如何在您自己的项目中使用它。

## 安装脱模装置

安装 Knockout 就是在 HTML 页面中包含一个小的 JavaScript 文件。前往[淘汰赛网站](http://knockoutjs.com/downloads/index.html)下载产品发布。或者，您可以包括来自 CDN 的淘汰。只需在 HTML 文档中放置下面的`<script>`标签。

```
<script type='text/javascript' src='http://cdnjs.cloudflare.com/ajax/libs/knockout/3.0.0/knockout-min.js'></script>
```

## MVVM 模式

为了有效地使用击倒，你必须首先了解什么是 MVVM 模式。如果你已经理解了 MVVM 模式，你可以跳到下一部分。

**Model:**MVVM 的 **M** 代表 Model，通常是应用持久化的业务数据。在大多数情况下，您最初将通过 Ajax 调用从服务器读取这些数据，并将其显示在 UI 上。例如，如果您想从服务器检索一个笔记列表，您可以向服务器发出一个 Ajax `GET`请求。

在 Knockout 中，视图只是一个显示视图模型的 HTML 页面(我们将会谈到)。每当这些视图模型改变时，视图中链接到视图模型的特定部分也会改变。

**ViewModel:** 简单来说，ViewModel 就是视图所呈现的模型。它是数据及其支持的操作的纯代码表示。ViewModel 通常不会持久化，它保存用户正在处理的未保存的更改。如果希望以后保存更改，可以将这些数据发送回服务器。在 Knockout 中，视图模型由 POJOs(普通旧 JavaScript 对象)实现。例如，如果您正在显示一个 todo 注释列表，那么您的 ViewModel 可能包含一个这样的注释对象列表，并公开几个函数来修改/添加注释。

## 入门指南

作为学习 Knockout 的第一步，让我们检查视图模型和数据绑定。以下代码片段创建了一个简单的视图模型:

```
function NameViewModel() {
this.name = 'John Doe';
}
```

或者，ViewModel 可以写成一个对象，如下所示。

```
var nameViewModel = {
name: 'John Doe'
}
```

现在，在 HTML 中，您只需要编写以下声明性绑定来连接 ViewModel 的`name`属性。

```
Hello, <span data-bind="text:name"></span>
```

这个代码片段只是将 UI 与 ViewModel 的`name`属性连接起来。这里，`name`的值被`innerHTML`运算到`span`标签中。现在，作为最后一步，我们需要告诉 Knockout】属性属于哪个视图模型。为此，只需添加以下代码。

```
ko.applyBindings(new NameViewModel());
```

这会导致 Knockout 执行数据绑定。因此，在 HTML 中，我们在`span`元素中看到了`name`的值。

**注意:**在复杂的应用程序中，你可能有几个视图模型，而不是只有一个。在这种情况下，您可以通过向`ko.applyBindings()`传递第二个参数，将特定的视图模型绑定到 UI 的特定部分。这方面的一个例子如下所示。

```
ko.applyBindings(new ContactViewModel(), document.getElementById('contacts-area'));
ko.applyBindings(new NoteViewModel(), document.getElementById('notes-area'));
```

最后要注意的是，在文档准备好之前，不要调用`ko.applyBindings()`。如果您使用的是 jQuery，将调用包装在`$(document).ready()`中。在 [VanillaJS](http://vanilla-js.com/) 中，你可以使用`DOMContentLoaded`事件处理程序。

## 具有可观测量的双向绑定

我们刚刚学习了如何将模型属性绑定到 UI。然而，我们可以走得更远，让这个东西充满活力。使用 observables，您可以将属性绑定到 UI，还有一个额外的好处——每当您的 ViewModel 属性发生变化时，UI 都会自动更新。此外，我们可以使用 Knockout 的声明性绑定特性，以便每当 UI 中的值(例如输入字段值)发生变化时，ViewModel 属性也会更新。这使您的视图模型和视图保持同步。

为了根据属性值更新视图，您需要使属性可观察。这很简单。只需修改我们之前的代码，如下所示:

```
function NameViewModel() {
this.name = ko.observable(''); //initially empty
}
```

每当`name`属性改变时，我们的视图就会更新。现在，让我们向 HTML 添加一个输入字段，并将其绑定到`name`,这样每当用户向其中键入内容时，属性都会发生变化，我们可以在`span`标签中看到更新后的值。

```
<input type="text" data-bind="value:name,valueUpdate:'input'" placeholder="start typing a name here"/>
Hello, <span data-bind="text:name"></span>
```

这里，我们利用了 Knockout 的声明性绑定语法。在`data-bind`属性中，`value`表示我们想要绑定到哪个属性。第二个参数`valueUpdate`指定何时更新 ViewModel 属性。因为我们已经将它设置为`'input'`，每当输入字段值改变时，ViewModel 中的属性都会更新。要了解这一功能的实际应用，请看一看[这款](http://embed.plnkr.co/GjztPqq3a0Tx2Eym42vT/preview) plunker。

您还可以在任何可观察到的值发生变化时收到通知。下面的例子展示了如何使用`subscribe()`函数来实现这一点。

```
function NameViewModel() {
this.name = ko.observable('');
this.name.subscribe(function(newVal) {
console.log(newVal); //logs whenever the value changes
});
}
```

## 使用计算的可观测量

有时，您可能希望使用其值取决于一个或多个其他属性的派生属性。如果您在视图中显示此衍生属性，则只要它所依赖的属性发生变化，就应该更新它。要做到这一点，我们需要一个计算的可观测值，它是这样创建的:

```
function ContactViewModel() {
this.phone = ko.observable();
this.email = ko.observable();

this.contactInfo = ko.computed(function() {
return this.phone() + ", " + this.email();
}, this);
}
```

现在，在视图中，我们可以使用下面的 HTML 绑定计算出的可观察值。

```
Contact Information: <span data-bind="text: contactInfo"></span>
```

通过查看您传递给`ko.compute()`的回调函数(或赋值函数)，Knockout 知道您计算的可观察值依赖于哪些可观察值。每当它们中的任何一个发生变化，你的赋值函数就会被自动调用。

`ko.compute()`的第二个参数是应该在赋值函数中用作`this`的对象。值得指出的是，您可以使用闭包实现相同的功能，如下所示。

```
function ContactViewModel() {
var self = this;

self.phone = ko.observable();
self.email = ko.observable();

self.contactInfo = ko.computed(function() {
return self.phone() + ", " + self.email();
});
}
```

最后要注意的是，当你使你的属性可见时，你不应该再在你的代码中直接访问它们。相反，我们需要像函数一样调用它们。这就是为什么在前面的例子中使用函数调用来检索`phone`和`email`的值。要设置可观察值，只需将新值作为函数参数传递即可(即`self.phone(4657324573)`)。

## 可观察阵列

每当我们想要检测单个 ViewModel 属性中的变化时，observables 就是最好的方法。但是，在许多场景中，我们感兴趣的是了解对象集合是否发生了变化。在这种情况下，我们可以使用可观察的数组，它是用下面的代码创建的。

```
function NotesViewModel() {
this.notes = ko.observableArray();
}
```

在创建一个可观察的数组之后，您通常会遍历这些项目，并在 UI 上显示它们。每当您向收藏中添加新项目或删除项目时，您的视图将自动更新。

您也可以像这样将初始值传递给可观察数组:

```
this.notes = ko.observableArray(['one', 'two', 'three']);
```

还可以进行`pop()`、`push()`、`shift()`、`unshift()`、`reverse()`、`sort()`、`splice()`等多种数组操作。很甜蜜，不是吗？

## 一个简单的淘汰赛应用程序

在本节中，我们将创建一个具有简单用户界面的淘汰制应用程序来管理手机数据。当添加新手机时，详细信息将显示在表格中。用户还可以从表中删除项目。看一看展示最终结果的[演示](http://embed.plnkr.co/IcH8ye51fIARjwrraTkb/preview)！

在进一步操作之前，请确保您已经安装了挖空和挖空贴图插件。稍后我会谈到映射插件。现在，就像这样把它包含在淘汰库之后:

```
<script src="//cdnjs.cloudflare.com/ajax/libs/knockout.mapping/2.4.1/knockout.mapping.js"/>
```

### 第一步

让我们假设每部手机都有三个属性:`name`、`os`和`price`。我们需要创建一个 ViewModel，并添加一些示例电话细节:

```
function PhonesViewModel() {
var self = this;

self.phones = ko.observableArray([{
name: 'Sony Xperia Z1',
os: 'Android',
price: 599
}, {
name: 'Apple iPhone 5S',
os: 'iOS',
price: 199
}, {
name: 'Google Nexus 5',
os: 'Android',
price: 299
}]);
}
```

如你所见，我们创建了一个可观察的数组来保存手机的详细信息。我们将遍历视图中的这些数组元素。

### 第二步

创建一个对象来保存在视图中输入的当前电话详细信息。我们需要将以下代码添加到我们的视图模型中。

```
self.currentPhone = ko.mapping.fromJS({
name: '',
os: '',
price: ''
});
```

`currentPhone`对象保存正在 UI 上输入的电话详细信息。我们将属性`name`、`os`和`price`绑定到 HTML 上的输入字段。我们还希望在添加电话详细信息后清除这些字段。为了清除字段，我们需要使这些属性可见，然后在添加电话的函数中清除它们。挖空贴图插件的`ko.mapping.fromJS()`功能自动使物体属性可见，这样我们就不必为每个属性写`ko.observable()`。

### 第三步

接下来，我们想提供一种向列表中添加新手机的方法。只需将下面的函数添加到我们的视图模型中。

```
self.addPhone = function() {
self.phones.push(ko.mapping.toJS(self.currentPhone));
self.currentPhone.name('');
self.currentPhone.os('');
self.currentPhone.price('');
};
```

创建并返回一个具有正常属性而非可见属性的对象。然后，我们将这个对象添加到我们的电话列表中，并清除 currentPhone 的属性，以便它反映在视图中。

### 第四步

在这一步中，我们将允许用户从列表中删除电话。这是使用以下函数完成的。

```
self.removePhone = function() {
self.phones.remove(this);
};
```

这里，`this`表示我们的表中将要删除的特定行。

### 第五步

接下来，添加以下标记:

```
<table>
<thead>
<tr>
<td></td>
<th>Name</th>
<th>OS</th>
<th>Price</th>
</tr>
</thead>
<tbody data-bind="foreach: phones">
<tr>
<td><a href="#" data-bind="click: $parent.removePhone">Remove</a></td>
<td data-bind="text: name"></td>
<td data-bind="text:os"></td>
<td data-bind="text:price"></td>
</tr>
</tbody>
</table>
<hr/>
<h3>Add a new Phone</h3>
<form data-bind="submit:addPhone">
<input type="text" data-bind="value:currentPhone.name,valueUpdate:'input'" placeholder="Phone Name" />
<br/>
<input type="text" data-bind="value:currentPhone.os,valueUpdate:'input'" placeholder="OS" />
<br/>
<input type="text" data-bind="value:currentPhone.price,valueUpdate:'input'" placeholder="Price" />
<br/>
<button type="submit">Add</button>
</form>
```

在这个标记中，我们使用了`foreach`数据绑定来遍历电话列表并显示它们。我们还使用了`click`绑定来从表中删除一个条目。这在我们的视图模型上调用了`removePhone()`函数。当我们在`foreach`绑定中时，一个新的上下文已经为我们创建了。为了获得对根视图模型的引用，我们使用了`$parent`。

接下来要注意的是`submit`绑定。这阻止了单击 submit 按钮时通常的表单提交过程。这让我们可以指定一个将被调用的自定义函数。在这种情况下，`addPhone()`被调用，添加一个新的电话。在表单内部，我们有三个输入字段，它们与`currentPhone`的属性同步。因此，只要有人按下提交按钮，我们就有了`currentPhone`对象中的详细信息，只需要将它推送到我们的电话列表中。

### 第六步

使用下面的代码激活 Knockout，并观察一切工作！

```
ko.applyBindings(new PhonesViewModel());
```

## 结论

对于使用 MVVM 模式构建动态 ui 来说，KnockoutJS 无疑是一个很好的框架。本教程涵盖了库的基础知识，但当然也有一些您应该了解的高级概念。以下资源可以帮助您:

*   [计算出的可观测量的详细信息](http://knockoutjs.com/documentation/computedObservables.html)。
*   [详细的声明性绑定语法](http://knockoutjs.com/documentation/foreach-binding.html)。
*   [与 JSON 合作](http://knockoutjs.com/documentation/json-data.html)。

本文演示的完整源代码可以在 [GitHub](https://github.com/jsprodotcom/source/blob/master/understanding-knockout-demo.zip) 上找到。

## 分享这篇文章