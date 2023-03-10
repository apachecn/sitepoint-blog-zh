# 解构 JavaScript 设计模式

> 原文：<https://www.sitepoint.com/javascript-design-patterns-deconstructed/>

所有复杂的设计模式都抛出了同一个基本问题——首先使用它有具体的优势吗？如果不了解好处，很容易认为这只是一个毫无意义的冗长和过度设计的解决方案，或者它只是在遵循某种时尚。

但是使用这种模式有真正重要的好处，这并不是试图让 JavaScript“更像”任何其他语言，或者模仿经典继承。它试图充分利用 JavaScript 固有的继承和作用域特性，并为 JavaScript 开发中特有的环境问题提供健壮的解决方案。

## 封装封口

将脚本封装在单个封装中有助于更好的封装。

所有 JavaScript 作者都必须面对脚本相互冲突的问题。全局作用域中的任何定义都将覆盖任何以前的同名定义或内置对象。最终，你的脚本可能会破坏另一个脚本，或者被另一个脚本破坏。但是当然一个作者不一定知道什么其他对象会和他们同时存在，所以唯一的管理方法就是避免把任何东西放在全局范围内。

在实践中，完全避免这一点是不可能的，但是我们可以做的是将我们的脚本封装到单个外壳中，从而*减少*脚本必须放入全局范围的对象数量到一个。所有脚本都受益于干净的环境，所以所有脚本都应该维护一个干净的环境；这不仅仅是库和第三方工具开发者的好习惯，对于所有的*脚本来说也是一个好主意。*

## 具名还是匿名？公立还是私立？

匿名闭包从外部是不可访问的。这是一个好处吗？如果只是意味着其他脚本不能访问它们，为什么要用匿名闭包包装脚本呢？

这就是为什么！以便其他脚本无法访问它们。这里重要的用例是第三方工具的开发——它们提供外部功能，但不希望公开它们的内部机制。有些脚本只是运行后台任务，根本不需要任何输入；因此，如果是这样的话，提供任何输入都没有意义——整个脚本可以包装在一个匿名的外壳中，这样就没有全局冲突的可能。

但是通常一个脚本提供一些公共的功能，这时候在公共和私有数据之间进行划分是有帮助的。一个健壮的代码库不应该允许自己被用户错误破坏，比如一个重要的属性被修改为一个意外的值；但是如果所有的数据都是公开的，用户可以很容易地做到这一点。

这是我自己过去犯的一个错误，给一个允许用户破坏内部值的脚本提供一个 <abbr title="Application Programming Interface">API</abbr> ，仅仅因为那些值被记录为公共属性。如果它们被记录为私有变量，它们将是安全的，因为用户不能从封闭范围之外修改私有变量。

如果你只是为你自己，为你自己的应用程序或网站写脚本，那么你可以说这样的考虑是没有实际意义的。如果您控制了 100%的代码库，那么它的内部交互也完全在您的控制之下，您可以简单地通过更改其中一个名称来解决名称冲突之类的问题。然而，我倾向于使用这种模式，即使不是绝对必要的，因为我发现它更容易管理。

## 这个还是那个？

任何函数的闭包作用域都可以被称为`this`，所以当我们定义一个命名的或者匿名的闭包时，`this`指的是顶层的那个闭包；并且它继续在它的公共方法中引用那个附件。

但是在私有函数中，`this`指的是直接封闭范围(私有函数)，而不是顶级封闭范围。因此，如果我们希望能够引用顶级范围，我们必须创建一个从任何地方引用它的变量。这就是`"THIS"`的目的:

```
function MyScript(){} (function() { var THIS = this; function defined(x) { alert(this); //points to defined() alert(THIS); //points to MyScript() } }).apply(MyScript);
```

它可以被称为任何没有被保留的东西。有人称之为`"that"`或`"self"`；我甚至尝试过使用非英语单词，如`"la"`或`"das."`，但最终我选择了大写字母`"THIS"`，因为在许多语言中，用大写字母声明常量是长期以来的惯例，这似乎符合要求。

每当我需要定义私有常量时，我都使用相同的约定:也就是说，私有值在脚本执行的生命周期中不会改变。浏览器变量是一个很好的例子，使用时:

```
var OLDER_WEBKIT = /applewebkit/([0-4]|[5][0-2])/i.test(navigator.userAgent), KONQUEROR = navigator.vendor == 'KDE'; 
```

理想情况下，我们应该使用`const`而不是`var`来声明它们，因为真正的常量使用更少的内存，但这在 Internet Explorer 中不受支持。

## 特权价值

我最不想看到的是使用特权值的好处。它们实际上只是私有值，可以使用公共方法重新定义。

```
 var options = { x : 123, y : 'abc' }; this.define = function(key, value) { if(defined(options[key])) { options[key] = value; } }; 
```

这样做的目的是为了更好地控制何时以及如何定义这些值。例如，定义函数可以包含详细的验证，将某些成员限制在预定义值的范围内，或者只允许在特定时间或以特定顺序定义它们。

我在我的 [CSSUtilities](http://www.brothercake.com/site/resources/scripts/cssutilities/) 库中很好地使用了这一点，它提供了一种定义全局配置的方法(例如，异步执行模式，只能是真或假)。私有变量与公共定义方法的结合提供了对那些定义的控制，因为用户不能独立于定义函数来重新定义它们，因此不能无效地定义它们。

*缩略图鸣谢:[超级金博](http://www.flickr.com/photos/superkimbo/3121815917/)*

## 分享这篇文章