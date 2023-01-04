# KnockoutJS 初学者指南:第 2 部分

> 原文：<https://www.sitepoint.com/beginners-guide-to-knockoutjs-part-2/>

## KnockoutJS 初学者指南:绑定

Knockout 为最常见的任务和场景提供了一整套有用的内置绑定。这些绑定中的每一个都允许您绑定简单的数据值或使用 JavaScript 表达式来计算适当的值。这提供了很大的灵活性，使得用最少的努力创建非常动态的 ui 变得容易。

使用内置绑定的语法是在 HTML 元素的 data-bind 属性中包含挖空绑定名称和视图模型属性对。

```
// syntax: data-bind="bindingName: bindingProperty"
<span data-bind="text: msg"></span>

```

如果要将数据绑定到 HTML 元素中的多个属性，只需使用以下语法用逗号分隔绑定:

```
<span data-bind="text: msg, visible: toggle"></span>
```

您应该记住，大多数绑定都试图将任何参数转换为布尔值。如果你给的值实际上不是布尔值，它会被不严格地解释为[真或假。这意味着非零数字、非空对象和非空字符串都将被解释为真，而零、空、未定义和空字符串将被解释为假。](https://www.sitepoint.com/javascript-truthy-falsy/)

如果您了解如何使用一种类型的数据绑定，那么其他的应该很容易学会。现在，我们将通过提供描述和简短的示例来解释它们。

## 简单绑定

在前面的教程中，我们已经看到了`text`绑定在处理可观察对象时的作用。它将关联元素的文本设置为您的参数值。这相当于设置 DOM 元素的`innerText`(对于 IE)或`textContent`(对于其他浏览器)属性。如果您的参数不是数字或字符串，那么绑定会将`toString()`的结果赋给元素。

如果这个参数是一个可观察的值，那么每当值改变时，绑定将更新元素的文本。如果参数不可见，它将只设置元素的文本一次，以后不会再更新。这对所有绑定都有效。

`text`绑定通常用于显示`span`或`div`元素中的值。当它被使用时，任何先前的文本将被覆盖。

```
<p>The tip of the day is: <span data-bind="text: tipOfTheDay"></span></p> 

function viewModel() {
 var self = this;
 self.tipOfTheDay = ko.observable('Relax.Take it easy!')
};
ko.applyBindings(new viewModel());
```

绑定将关联元素的值设置为参数的值。这通常用于像`input`、`select`和`textarea`这样的表单元素。当用户编辑关联表单控件中的值时，它会更新视图模型中的值。同样，当您更新视图模型中的值时，也会更新屏幕上表单控件的值。这就是所谓的双向绑定。如果你的参数不是数字或字符串，那么绑定会将`toString()`的结果赋给元素。

默认情况下，当用户在`change`事件上将焦点转移到另一个 DOM 节点时，挖空会更新您的视图模型，但是您可以使用下面描述的`valueUpdate`参数来控制何时更新该值。如果您的绑定还包含一个名为`valueUpdate`的参数，它将定义哪个浏览器事件剔除应该用来检测更改。

`"change"`是默认事件，当用户将焦点移动到不同的控件时，或者对于`<select>`元素，在发生任何变化后，它会立即更新您的视图模型。

`"afterkeydown"`–一旦用户开始输入字符，就更新您的视图模型。这通过捕获浏览器的 keydown 事件并异步处理该事件来实现。如果你想保持你的视图模型实时更新，使用`"afterkeydown"`将是最好的选择。

`"keyup"`–当用户释放一个键时更新您的视图模型

`"keypress"`–当用户键入一个键时，更新您的视图模型。如果用户按住一个键，它会重复更新

```
<input data-bind="value: name, valueUpdate: 'afterkeydown'"></input>
<p data-bind="text: name"></p>

function viewModel() {
 var self = this;
 self.name = ko.observable()
};
ko.applyBindings(new viewModel());
```

## 控制标记

`html`绑定不经常使用，但是对于在视图模型中呈现 HTML 内容非常方便。这个绑定将关联元素的 HTML 设置为您的参数值，相当于在 DOM 元素上设置`innerHTML`属性。如果你的参数不是数字或字符串，那么绑定会将`toString()`的结果赋给元素。

由于这个绑定使用`innerHTML`来设置元素的内容，所以您应该小心不要将它与不受信任的模型值一起使用，因为这可能会导致脚本注入攻击。如果您不能保证显示内容是安全的，那么您可以使用`text`绑定。

```
<div data-bind="html: markup"></div>

function viewModel() {
 var self = this;
 self.markup = ko.observable('<p><strong>Knockout</strong> is so <em>cool</em>!</p>')
};
ko.applyBindings(new viewModel());

```

虽然 Knockout 有许多内置绑定，但您肯定会遇到一些不存在的情况。对于这些，Knockout 提供了`attr`绑定，允许您将任何属性数据绑定到视图模型属性。参数应该是一个 JavaScript 对象，其中属性名是属性，属性值是将绑定到属性的值。这在很多常见场景中非常有用，比如绑定`a`元素的`href`和`title`或者`img`元素的`src`和`alt`。

```
<img data-bind="attr: {src: url, alt: details}" />

function viewModel() {
 var self = this;
 self.url = ko.observable(images/logo.png)
 self.details = ko.observable('This is logo')
};
ko.applyBindings(new viewModel());
```

## 添加样式

您可以使用`css`和`style`内置绑定来绑定带有挖空的样式。

绑定为相关元素设置一个或多个 CSS 类。该参数应该是一个 JavaScript 对象，其中属性名称对应于所需的 CSS 类，属性值计算为 true 或 false，指示是否应该应用该类。您可以一次设置多个 CSS 类。

```
<style>
.colorize {color: red}
</style>

<p data-bind="css: { colorize: on }">Text</p>

function viewModel() {
 var self = this;
 self.on = ko.observable(true)
};
ko.applyBindings(new viewModel());
```

您可以使用表达式来确定何时应用该类。

```
<p data-bind="css: { colorize: on() > 3 }">Text</p>
```

虽然尽可能使用 css 类更好，但有时您可能也想设置特定的样式。Knockout 通过它的`style`内置绑定支持这一点，它为相关元素设置一个或多个样式值。参数应该是一个对象，其属性对应于 CSS 样式名称，其值对应于您希望应用的样式值。通常，这个参数值是使用 JSON 声明的。

```
<p data-bind="style: {color: on() > 3 ? 'red' : 'black'}">Text</p>
function viewModel() {
 var self = this;
 self.on = ko.observable(5)
};
ko.applyBindings(new viewModel());
```

注意:当你有一个属性或者 CSS 类，它的名字不是合法的 JavaScript 变量名，那么你应该用引号把标识符的名字括起来，这样它就变成了一个字符串。如果您想要应用名称不是合法 JavaScript 标识符的样式，您必须使用该样式的 JavaScript 名称。

```
//incorrect:
<div data-bind="attr: { data-something: someValue }">...</div>
<div data-bind="css: { my-class: someValue }">...</div>
<div data-bind="style: { font-weight: someValue }">...</div>

//correct:
<div data-bind="attr: { 'data-something': someValue }">...</div>
<div data-bind="css: { 'my-class': someValue }">...</div>
<div data-bind="style: { fontWeight: someValue }">...</div>
```

## 处理事件

Knockout 通过其`event`内置绑定支持绑定到任何事件。它将指定事件的事件处理程序添加到关联的 DOM 元素中。您可以使用它来绑定到任何已定义的 HTML 事件。在事件处理程序中，您可以访问当前视图模型数据项、事件对象，甚至是作为事件绑定的一部分传递的自定义参数。要使用`event`绑定，您需要传递一个对象文字，其中包含事件名称和视图模型方法的名称值对，用逗号分隔。

```
<p data-bind="event: { mouseover: hello, mouseout: goodbye }"> Mouse over me! </p>
<p data-bind="text: helloEnabled"></p>
<p data-bind="text: goodbyeEnabled"></p>

function viewModel() {
var self = this;
self.helloEnabled = ko.observable()
self.goodbyeEnabled = ko.observable()
 self.hello = function() {
 self.helloEnabled('Hello!');
 self.goodbyeEnabled('');
 }
 self.goodbye = function() {
 self.goodbyeEnabled('Goodbye!');
 self.helloEnabled('');
 }
};
ko.applyBindings(new viewModel());

```

正如你所猜测的，绑定处理点击事件。因为它是事件最常用的绑定，所以它只是`event`绑定的一个快捷方式。

```
<button data-bind="click: writeMSG">Show</button>
<p data-bind="text: msg"></p>

function viewModel() {
 var self = this;
 self.msg = ko.observable()
 self.writeMSG = function() {
 self.msg('Hello!')
 }
};
ko.applyBindings(new viewModel());
```

`submit`绑定是处理表单元素提交事件的快捷方式。当您在表单上使用`submit`绑定时，Knockout 将阻止浏览器对该表单的默认提交操作。换句话说，浏览器将调用您的处理函数，但不会将表单提交给服务器。这是一个有用的默认设置，因为当您使用`submit`绑定时，通常是因为您将表单用作视图模型的接口，而不是常规的 HTML 表单。如果您确实想让表单像普通 HTML 表单一样提交，只需从提交处理程序返回 true。

您可以使用单击提交按钮，而不是在表单上使用提交。但是使用提交绑定的好处是可以使用其他方式提交表单，比如在文本框中键入内容时按 enter 键。

## 控制用户界面

`visible`绑定根据绑定参数值设置关联元素的可见性。绑定试图将任何参数转换为布尔值。Knockout 的`visible`绑定应该绑定到评估为 true 或 false 的属性。这优先于您使用 CSS 定义的任何显示样式。

```
<button data-bind="click: show">Show Message</button>
<button data-bind="click: hide">Hide Message</button>
<p data-bind="visible: msg">Hello, Knockout!</p>

function viewModel() {
 var self = this;
 self.msg = ko.observable()
 self.show = function() {
 self.msg(true)
 }
 self.hide = function() {
 self.msg(false)
 }
};
ko.applyBindings(new viewModel());

```

`enable/disable` binding 根据提供的值设置相关元素的禁用属性。这通常用于像`input`、`select`和`textarea`这样的表单元素。Knockout 提供内置绑定来启用和禁用输入元素。如果 input 元素绑定到的属性评估为 true，那么`enable`绑定将启用该元素，如果评估为 false，将禁用该元素。`disable`绑定的作用正好相反

```
<input data-bind="value: val, valueUpdate: 'afterkeydown'">
<button data-bind="enable: val">Send</button>

function viewModel() {
 var self = this;
 self.val = ko.observable()
};
ko.applyBindings(new viewModel());
```

Knockout 有一个名为`hasfocus`的内置绑定，用于确定和设置哪个元素具有焦点。当访问者打开页面时，如果您希望将焦点设置到表单上的特定元素(例如搜索表单),那么`hasfocus`绑定非常方便

```
<input data-bind="value: val, hasfocus: on"> 
<button data-bind="enable: on">Send</button>
function viewModel() {
 var self = this;
 self.val = ko.observable()
 self.on = ko.observable(false)
};
ko.applyBindings(new viewModel());
```

## 处理复选框和单选按钮

复选框可以是绑定到 Knockout 的`checked`绑定的数据。`checked`绑定应该绑定到计算结果为 true 或 false 的属性或表达式。因为视图模型属性被定义为可观察的，所以当源属性更改时，复选框会更新。同样，当用户选中或取消选中复选框时，视图模型属性中的值也会更新。此绑定设置单选按钮和复选框的选中状态。对于复选框，绑定会尝试将任何参数转换为布尔值。对于单选按钮，绑定将按钮值属性与绑定参数进行比较。

```
<p>Let me choose my favorite car: <input type="checkbox" data-bind="checked: car" /></p>
<div data-bind="visible: car">
 Preferred model:
 <div><input type="radio" name="modelsGroup" value="ferrari" data-bind="checked: model" /> Ferrari</div>
 <div><input type="radio" name="modelsGroup" value="lamborghini" data-bind="checked: model" /> Lamborghini</div>
 <div><input type="radio" name="modelsGroup" value="bugatti" data-bind="checked: model" /> Bugatti</div>
</div>

function viewModel() {
 var self = this;
 self.car = ko.observable(),
 self.model = ko.observable("lamborghini") // Initially selects Lamborghini
};
ko.applyBindings(new viewModel());

```

## 创建下拉列表

下拉列表有几个重要的属性，可以加载项目列表、显示值、使用不同的键值和存储用户的选择。Knockout 为其中的每一个都提供了内置绑定。`options`绑定设置将出现在下拉列表元素中的选项。该值应为数组。此绑定不能用于除了`<select>`元素之外的任何元素。对于多选列表，要设置选择了哪些选项，或者要读取选择了哪些选项，请使用`selectedOptions`绑定。对于单选列表，您还可以使用`value`绑定来读取和写入选中的选项。

`options`绑定标识了要显示的值的列表，通常来自视图模型上的数组属性。

```
<p>Choose your destiny: <select data-bind="options: availableRoles"></select></p>

function viewModel() {
 var self = this;
 self.availableRoles = ko.observableArray(['an artist', 'an actor', 'an author'])
};
ko.applyBindings(new viewModel());
```

`selectedOptions`绑定控制多选列表中的哪些元素当前被选中。当用户选择或取消选择多选列表中的某个项目时，会在视图模型的数组中添加或删除相应的值。

```
<p>Choose your destiny: <select data-bind="options: availableRoles, selectedOptions: selected" multiple="true"></select></p>

function viewModel() {
 var self = this;
 self.availableRoles = ko.observableArray(['an artist', 'an actor', 'an author'])
 self.selected = ko.observableArray(['an author'])
};
ko.applyBindings(new viewModel());
```

有时您希望在下拉列表中显示一个值，但是当用户从列表中选择一个项目时使用另一个值。Knockout 的内置`optionsText`和`optionsValue`绑定有所帮助。从选项绑定中，`optionsText`绑定被设置为要在下拉列表中显示的属性的字符串名称。`optionsValue`绑定被设置为属性的字符串名称，以便为下拉列表中的项目的选定值绑定。当你不想默认选择任何特定的选项时,`optionsCaption`很有用。此参数在选项列表顶部设置一个文本，如“选择一个项目…”并在没有选择任何特定项目时显示该文本。

```
<p>Locales: <select data-bind="options: locales, selectedOptions: selected, optionsCaption: 'Select your locale...', optionsText: 'country', optionsValue: 'code'"></select></p>
<p data-bind="text: selected"></p>

function viewModel() {
  var self = this;
  self.locales = [
   { country: 'USA', code: 'en_US' },
   { country: 'Spain', code: 'es_ES' },
   { country: 'French', code: 'fr_FR' }
  ]
  self.selected = ko.observableArray();
 }
ko.applyBindings(new viewModel()); 
```

这部分就到这里，敬请期待大结局，下周同一时间！

## 分享这篇文章