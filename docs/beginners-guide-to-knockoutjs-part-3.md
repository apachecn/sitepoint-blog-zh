# KnockoutJS 初学者指南:第 3 部分

> 原文：<https://www.sitepoint.com/beginners-guide-to-knockoutjs-part-3/>

# KnockoutJS 初学者指南:模板及更多

有四个控制流绑定:`foreach`、`if`、`ifnot`和`with`。这些控件绑定允许您以声明方式定义控制流逻辑，而无需创建命名模板，如下所示。

`foreach`绑定为数组中的每个条目复制一段标记，并将该标记的每个副本绑定到相应的数组项。这适用于呈现列表或表格。如果您的数组是一个可观察数组，那么无论何时您添加或移除数组条目，绑定都会通过插入或移除列表项或表行的更多副本来更新 UI，而不会影响任何其他 DOM 元素。请参见以下示例:

```
<table>
 <thead>
  <tr><th>Title</th><th>Author</th></tr>
 </thead>
 <tbody data-bind="foreach: books">
  <tr>
   <td data-bind="text: title"></td>
   <td data-bind="text: author"></td>      
  </tr>
 </tbody>
</table>

<script type="text/javascript">
  function viewModel() {
   var self = this;
   self.books = ko.observableArray([
     { title: 'The Secret', author: 'Rhonda Byrne' },
     { title: 'The Power', author: 'Rhonda Byrne' },
     { title: 'The Magic', author: 'Rhonda Byrne' }
   ]);
  }
  ko.applyBindings(new viewModel());    
</script>
```

这里，将为 books 数组中的每个数组条目自动创建一个表行。

有时您可能需要引用数组条目本身，而不仅仅是它的一个属性。在这种情况下，您可以使用伪变量`$data`。当在`foreach`块中使用时，表示“当前项目”。

```
<ul data-bind="foreach: daysOfWeek">
 <li>
 <span data-bind="text: $data"></span>
 </li>
</ul>

<script type="text/javascript">
function viewModel() {
  var self = this;
  self.daysOfWeek = ko.observableArray([
   'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'
  ]);
};

ko.applyBindings(new viewModel());
</script>
```

这将列出一周中的所有日子，而不需要分别重复每个项目的代码。

在 Knockout 中，您可以嵌套任意多的控制流绑定。当你这样做的时候，通常需要回到层次结构，从父上下文中访问数据或函数。在这种情况下，您可以使用以下伪变量:

`$parent`–表示当前`foreach`块之外的数据项

`$parents`–表示所有外部控制流范围的数据项的数组。`$parents[0]`与`$parent`相同。`$parents[1]`表示来自祖父控制流范围的项目，依此类推。

`$root`–表示最外面的控制流范围中的项目。通常这是您的顶层视图模型对象。

在下面的例子中，我们使用了`$parent`伪变量，以便从 books 数组中正确地删除一个图书条目:

```
<table>
 <thead>
  <tr><th>Title</th><th>Author</th></tr>
 </thead>
 <tbody data-bind="foreach: books">
  <tr>
   <td data-bind="text: title"></td>
   <td data-bind="text: author"></td>
   <td><a href="#" data-bind="click: $parent.removeBook">Remove</a></td>
  </tr>
 </tbody>
</table>

<script type="text/javascript">
  function viewModel() {
   var self = this;
   self.books = ko.observableArray([
     { title: 'The Secret', author: 'Rhonda Byrne' },
     { title: 'The Power', author: 'Rhonda Byrne' },
     { title: 'The Magic', author: 'Rhonda Byrne' }
   ]);

  self.removeBook = function() {
   self.books.remove(this);
  }
  }
  ko.applyBindings(new viewModel());    
</script>
```

在某些情况下，您可能希望复制一部分标记，但是您没有任何容器元素来放置 foreach 绑定。然后，您可以使用以下语法:

```
<ul>
<li><strong>Days of week:</strong></li>
 <!-- ko foreach: daysOfWeek -->
 <li>
  <span data-bind="text: $data"></span>
 </li>
 <!-- /ko -->
</ul>

<script type="text/javascript">
function viewModel() {
  var self = this;
  self.daysOfWeek = ko.observableArray([
   'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'
  ]);
};

ko.applyBindings(new viewModel());
</script>
```

在这个例子中，您不能使用普通的`foreach`绑定。如果你把它放在`<ul>`上，这将复制标题项，如果你想在`<ul>`中再放一个容器，你不能，因为在`<ul>`中只允许有`<li>`元素。解决方案是使用无容器控制流语法，其中`<!-- ko -->`和`<!-- /ko -->`注释定义一个“虚拟元素”,里面包含标记，这个语法 Knockout 理解并绑定这个虚拟元素，就像你有一个真正的容器元素一样。这种语法对于`if`和`with`绑定也是有效的。

只有当指定的表达式计算结果为 true 时，`if`绑定才会使一部分标记出现在文档中。然后，包含的标记将出现在文档中，文档中的任何数据绑定属性都将被应用。另一方面，如果表达式的计算结果为 false，则包含的标记将从文档中移除，而不会先对其应用任何绑定。

```
<label><input type="checkbox" data-bind="checked: showList" />Show me list</label>
<ul data-bind="if: showList">
  <li>Item</li>
  <li>Item</li>
  <li>Item</li>
</ul>

<script type="text/javascript">
   function viewModel() {
    var self = this;
    self.showList = ko.observable(false);
   }
  ko.applyBindings(new viewModel());    
</script>
```

`with`绑定创建了一个新的绑定上下文，因此后代元素被绑定在指定对象的上下文中。要用作绑定后代元素的上下文的对象。如果您提供的表达式计算结果为 null 或 undefined，子代元素将根本不会被绑定，而是将从文档中移除。`with`绑定将数据上下文更改为您指定的任何对象。这在处理具有多个父/子关系的对象图时特别有用。

```
<p data-bind="text: book"> </p> 
<ul data-bind="with: details">
 <li>Category: <span data-bind="text: category"> </span></li>
 <li>Author: <span data-bind="text: author"> </span></li>
 <li>Publisher: <span data-bind="text: publisher"> </span></li>
</ul>

<script type="text/javascript">
  function viewModel() {
   var self = this;
   self.book = ko.observable('The Secret');
   self.details = ko.observable({category: 'Psychology', author: 'Rhonda Byrne', publisher: 'Simon & Schuster Ltd'});
  }
 ko.applyBindings(new viewModel());    
</script>
```

## 模板

`template`绑定用呈现模板的结果填充关联的 DOM 元素。模板是构建复杂的 UI 结构的一种简单而方便的方法——可能带有重复或嵌套的块——作为视图模型数据的一种功能。使用模板有两种主要方式。第一个，本地模板，是支撑`foreach`、`if`、`with`和其他控制流绑定的机制。在内部，这些控制流绑定捕获元素中包含的 HTML 标记，并将其用作模板来呈现任意数据项。这个特性内置在 Knockout 中，不需要任何外部库。您可以在这里看到创建模板的基本方案:

```
<div data-bind="template: 'myTemplate'"></div>

<script type="text/html" id="myTemplate">
// template code here
</script>

```

在下面的示例中，您可以看到如何使用它:

```
<div data-bind="template: 'book-template'"></div>

<script type="text/html" id="book-template">  
  <h3 data-bind="text: title"></h3>
  <p>Written by: <span data-bind="text: author"></span></p>
</script>

<script type="text/javascript">
   function viewModel() {
    var self = this;
    self.title = ko.observable('The Secret')
    self.author = ko.observable('Rhonda Byrne')
   }
 ko.applyBindings(new viewModel());    
</script>
```

这里，我们必须使用一个等于模板名称的 id，以便将模板绑定到我们的标记的其余部分。在这种情况下，它是“图书模板”。

我们可以向模板绑定传递更多的参数，而不是使用上面描述的短语法，这将给我们提供对最终输出的更精确的控制。

```
//syntax: <div data-bind="template: { name: 'myTemplate', data: myData, afterRender: myLogic }"></div>

<div data-bind="template: { name: 'book-template', data: bestseller, afterRender: msg }"></div>

//template here

<script type="text/javascript">
   function MyViewModel() {
    var self = this;
    self.bestseller = { title: 'The Secret', author: 'Rhonda Byrne' };
    self.ordinary = {title: 'Some Name', author: 'Some Author'};
    self.msg = function(elements) {
      alert('Hip Hip Hooray!!! :)'); 
    }
   }
 ko.applyBindings(new MyViewModel());    
</script>
```

这里，`name`是包含您希望呈现的模板的元素的 id；`data`是一个对象，作为数据提供给模板进行渲染；而`afterRender`是一个针对呈现的 DOM 元素调用的回调函数。

下面的例子相当于一个`foreach`绑定。在这里，`foreach`作为参数被传递给`template`绑定。

```
//syntax: <div data-bind="template: { name: 'myTemplate', foreach: myArray }"></div>

<div data-bind="template: { name: 'book-template', foreach: books }"></div>

//template here

<script type="text/javascript">
   function MyViewModel() {
    var self = this;
    self.books = [
    { title: 'The Secret', author: 'Rhonda Byrne' },
    { title: 'The Power', author: 'Rhonda Byrne' }
    ]
   }
 ko.applyBindings(new MyViewModel());    
</script>
```

通过将匿名模板直接嵌入到使用 foreach 绑定的元素中，可以获得完全相同的结果:

```
<div data-bind="foreach: books">  
  <h3 data-bind="text: title"></h3>
  <p>Written by: <span data-bind="text: author"></span></p>
</div>
```

使用模板的第二种方法是将 Knockout 连接到第三方模板引擎。挖空会将您的模型值传递到外部模板引擎，并将结果标记字符串注入到您的文档中。对于使用 *jquery.tmpl* 和*下划线*模板引擎的例子，查看[文档](http://knockoutjs.com/documentation/template-binding.html)。

## 扩展可观测量

挖空可观测量提供了支持读/写值并在值发生变化时通知订户所需的基本功能。但是，在某些情况下，您可能希望向可观察对象添加额外的功能，比如向可观察对象添加额外的属性。敲除延伸器提供了一种简单灵活的方法来实现这一点。

创建一个扩展程序需要向`ko.extenders`对象添加一个函数。该函数将可观察对象本身作为第一个参数，并将任何选项作为第二个参数。然后，它可以返回可观测值，或者返回新的东西，比如以某种方式使用原始可观测值的计算可观测值。

现在我们将创建一个可观察的扩展器，它将增加显示提示消息的能力。

```
<input data-bind='value: name, hasfocus: name.on' />
<span data-bind="visible: name.on, text: name.hint"></span>
<br />
<input data-bind='value: pass, hasfocus: pass.on' />
<span data-bind="visible: pass.on, text: pass.hint"></span>

<script type="text/javascript">

// begin observable extender
ko.extenders.hints = function(target, hint) {
 target.on = ko.observable() 
 target.hint = ko.observable()

 function showHint(value){
  target.on(value ? false : true);
  target.hint(value ? "" : hint);
 }

 showHint(target());

 return target;
}; 
// end observable extender

 function viewModel() {
  var self = this;
  self.name = ko.observable().extend({hints: 'Type your name here'})
  self.pass = ko.observable().extend({hints: 'Type your password here'})
 };
ko.applyBindings(new viewModel());
</script>
```

## 自定义绑定

Knockout 的内置绑定允许您处理大多数绑定场景，但是如果您遇到未涉及的特殊绑定场景，您可以使用 Knockout 创建自定义绑定，这为您提供了很大的灵活性，以易于重用的方式封装复杂的行为。例如，您可以以自定义绑定的形式创建交互式组件，如网格、选项卡集等。

剔除绑定包括两种方法:`init`和`update`。创建一个绑定就像用这两种方法创建一个对象并使用`ko.bindingHandlers`用挖空注册该对象一样简单，如下所示。

```
ko.bindingHandlers.yourBindingName = {   
  init: function(element, valueAccessor, allBindingsAccessor, viewModel) {

  },   
  update: function(element, valueAccessor, allBindingsAccessor, viewModel) {

  } 
};

// once created, you can use your custom binding in similar way as any built-in binding
<div data-bind="yourBindingName: someValue"> </div>

```

`init`函数将仅在第一次为该元素评估绑定时运行。这通常用于运行一次性初始化代码或连接事件处理程序，使您能够基于 UI 中触发的事件更新视图模型。

`update`函数提供了一种在相关的可观察值被修改时做出响应的方法。通常，这用于根据对视图模型的更改来更新您的 UI。

`init`和`update`功能有四个参数。一般来说，你会想把重点放在`element`和`valueAccessor`参数上，因为它们是将你的视图模型链接到你的 UI 的标准方式。实际上，你不必同时提供`init`和`update`回调——如果你需要的话，你可以只提供其中一个。

`element`参数让您可以直接访问包含绑定的 DOM 元素。

`valueAccessor`参数是一个函数，可以让您访问传递给绑定的内容。如果你传递了一个可观测值，那么这个函数的结果将是那个可观测值(而不是它的值)。如果你在绑定中使用了一个表达式，那么`valueAccessor`的结果将是这个表达式的结果。

`allBindingsAccessor`参数让您可以访问同一数据绑定属性中列出的所有其他绑定。这通常用于访问与此绑定交互的其他绑定。这些绑定可能没有任何关联的代码，只是向绑定传递附加选项的一种方式，除非您选择将一个具有多个属性的对象传递到主绑定中。例如，`optionsValue`、`optionsText`和`optionsCaption`是仅用于将选项传递给`options`绑定的绑定。

`viewModel`参数将为模板之外的绑定提供对整体视图模型的访问。在模板内部，这将被设置为绑定到模板的数据。例如，当使用模板绑定的`foreach`选项时，`viewModel`参数将被设置为通过模板发送的当前数组成员。大多数情况下，`valueAccessor`会给你想要的数据，但是当你调用/应用函数时，如果你需要一个对象作为你的目标，`viewModel`参数特别有用。

在下面的例子中，我们将创建一个自定义绑定，当它处于焦点时缩放一个文本区域。

```
<textarea data-bind="scaleOnFocus: scaleArea, scaleUp: {height: '200', width: '400'}, scaleDown: {height: '15', width: '150'}"></textarea>

<script type="text/javascript">

// begin custom binding
ko.bindingHandlers.scaleOnFocus = {

 init: function(element, valueAccessor) {
  $(element).focus(function() {
   var value = valueAccessor();
   value(true);
  });
  $(element).blur(function() {
   var value = valueAccessor();
   value(false);
  });     
 },

 update: function(element, valueAccessor, allBindingsAccessor) {
  var value = valueAccessor();
  var allBindings = allBindingsAccessor();
  var up = allBindings.scaleUp;
  var down = allBindings.scaleDown;
   if (ko.utils.unwrapObservable(value))
    $(element).animate(up);
   else 
    $(element).animate(down);
 }
};
// end custom binding

function viewModel() {
 var self = this;
 self.scaleArea = ko.observable()
};

ko.applyBindings(new viewModel());
</script>

```

首先，在`init`函数中，我们声明当元素处于焦点时，它的值将被设置为 true，反之亦然。然后在`update`函数中，我们使用`allBindingAccessor`参数向我们的绑定添加额外的选项—`scaleUp`和`scaleDown`。我们使用`ko.utils.unwrapObservable`来获取当前绑定的值，并检查它是否被设置为 true。如果是，则放大 DOM 元素，否则缩小。

最后，我们来看一个结合了提示可观察扩展器和 scaleOnFocus 自定义绑定的示例:

```
<input data-bind='value: name, hasfocus: name.on' />
<span data-bind="visible: name.on, text: name.hint"></span>
<br />
<input data-bind='value: email, hasfocus: email.on' />
<span data-bind="visible: email.on, text: email.hint"></span>
<br />
<textarea data-bind="value: msg.hint, scaleOnFocus: scaleArea, scaleUp: {height: '200', width: '400'}, scaleDown: {height: '50', width: '150'}"></textarea>

<script type="text/javascript">
ko.extenders.hints = function(target, hint) {
 target.on = ko.observable() 
 target.hint = ko.observable()

 function showHint(value){
  target.on(value ? false : true);
  target.hint(value ? "" : hint);
 }

 showHint(target());

 return target;
}; 

ko.bindingHandlers.scaleOnFocus = {

 init: function(element, valueAccessor) {
  $(element).focus(function() {
   var value = valueAccessor();
   value(true);
  });
  $(element).blur(function() {
   var value = valueAccessor();
   value(false);
  });     
 },

 update: function(element, valueAccessor, allBindingsAccessor) {
  var value = valueAccessor();
  var allBindings = allBindingsAccessor();
  var up = allBindings.scaleUp;
  var down = allBindings.scaleDown;
   if (ko.utils.unwrapObservable(value))
    $(element).animate(up);
   else 
    $(element).animate(down);
 }
};

function viewModel() {
 var self = this;
 self.name = ko.observable().extend({hints: 'Type your full name'})
 self.email = ko.observable().extend({hints: 'Type a valid email'})
 self.msg = ko.observable().extend({hints: 'Leave a message...'})
 self.scaleArea = ko.observable()
};
ko.applyBindings(new viewModel());
</script>
```

您可以将提示 observable 和 scaleOnFocus 绑定放在一个单独的文件中，然后将它们包含在主文件中。这使得代码模块化，并允许您随时重用它。

就这样，伙计们！我希望你喜欢这个系列。现在，您已经掌握了开始并继续学习和试验 Knockout 的所有必要知识。更多全面的例子和教程，你可以去淘汰赛网站，我建议你这样做。

## 分享这篇文章