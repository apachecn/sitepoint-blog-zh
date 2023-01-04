# 在 Sass 中负责任地使用@error

> 原文：<https://www.sitepoint.com/using-error-responsibly-in-sass/>

从 Ruby Sass 3.4 和 LibSass 3.1 开始，可以使用 [`@error`](https://sass-lang.com/documentation/file.SASS_REFERENCE.html#_7) 指令。该指令类似于`@warn`，旨在终止执行进程，并向当前输出流(可能是控制台)显示一条定制消息。

不用说，这个特性在构建包含少量 Sass 逻辑的函数和 mixins 时非常有用，可以控制作者的输入，并在出错时抛出错误。你不得不承认这比让编译器悲惨地失败要好，不是吗？

一切都很好。除了 Sass 3.3 还在广泛使用。甚至在某些地方 Sass 3.2。更新 Sass 并不总是一件容易的事情，尤其是在大型项目中。有时候，花时间和预算去更新一些运行良好的东西是不可取的。对于那些旧版本，`@error`没有任何意义，被视为自定义 at 指令，出于向前兼容的原因，这在 Sass 中是完全允许的。

那么这是否意味着我们不能使用`@error`除非我们决定只支持最近的 Sass 引擎？嗯，你可以想象有一种方法，因此这篇文章。

## 有什么想法？

想法很简单:如果支持`@error`，我们就使用它。否则，我们使用`@warn`。虽然`@warn`并没有阻止编译器继续前进，所以我们可能想在警告后触发一个编译错误，这样编译就永远崩溃了。享受它，它不是经常你可以不顾一切地撞毁一些东西。

这意味着我们需要将整个东西包装在一个 mixin 中，姑且称之为`log(..)`。我们可以这样使用它:

```
@include log('Oops, something is wrong with what you just did!');
```

你得承认，这很棒，不是吗？好了，吹牛吹够了，我们来造吧。

## 构建记录器

所以我们的 mixin 的工作方式与`@error`或`@warn`完全一样，因为它只是一个包装器。因此，它只需要一个参数:消息。

```
@mixin log($message) { ... }
```

你可能会问自己，我们将如何寻找支持。起初，我想出了一个涉及版本嗅探的蹩脚解决方案，但那太糟糕了。此外，我完全忽略了一个事实，即 Sass 的核心设计者是聪明人，他们考虑了整个事情，并为`feature-exists(..)`函数引入了一个`at-error`键，返回该特性是否受支持。

```
@mixin log($message) {
 @if feature-exists('at-error') == true {
 @error $message;
  } @else {
 @warn $message;
  }
}
```

如果你是一个补丁注释读者，你可能知道`feature-exists(..)`函数只在 Sass 3.3 中引入。它不包括 Sass 3.2！嗯，这是事实的一部分。在 Sass 3.2 中，`feature-exists('at-error')`被评估为一个字符串，该字符串为*真值*。通过添加`== true`，我们确保 Sass 3.2 不会进入这种状态，并转移到`@warn`版本。

到目前为止，一切顺利。虽然我们必须在警告后触发编译错误。我们要怎么做？好吧，有很多方法可以解决这个问题，但最理想的是，我们需要一些你能识别的东西。不久前，埃里克·苏姗娜提出了一个想法:调用一个函数而不使用`@return`语句足以导致崩溃。这种模式通常被称为 *noop* ，表示*不操作*。基本上就是一个空白函数，什么都不做。由于 Sass 的工作方式，它会使编译器崩溃。哪个好！

```
@function noop() {}
```

关于这个函数，最后但同样重要的一点是，我们如何调用它？Sass 函数只能在特定的位置调用。有几种方法可供我们使用:

*   虚拟变量，例如:`$_: noop()`
*   虚拟属性，例如:`crash: noop()`
*   空条件，例如:`@if noop() {}`
*   你也许可以找到其他方法来调用这个函数。

我想警告你不要使用`$_`，因为它是 Sass 库和框架中常用的变量。虽然在 Sass 3.3+中这可能不是问题，但在 Sass 3.2 中，这会覆盖任何全局`$_`变量，这在某些情况下会导致难以跟踪的问题。让我们用空条件的东西，因为它与一个 *noop* 配对时是有意义的。函数的一个条件。

```
@mixin log($message) {
 @if feature-exists('at-error') == true {
 @error $message;
  } @else {
 @warn $message;
  // Because functions cannot be called anywhere in Sass,
  // we need to hack the call in a dummy condition.
    @if noop() {}
  }
}
```

好吧！让我们用之前的代码对此进行测试:

```
@include log('Oops, something is wrong with what you just did!');
```

下面是 LibSass:

```
message:
path/to/file.scss
1:1  Oops, something is wrong with what you just did!
Details:
column: 1
line: 1
file: /path/to/file.scss
status: 1
messageFormatted: path/to/file.scss
1:1  Oops, something is wrong with what you just did!
```

下面是 Sass 3.4:

```
Error: Oops, something is wrong with what you just did!
on line 1 of path/to/file.scss, in `log'
Use --trace for backtrace.
```

下面是 Sass 3.2 和 3.3(输出是一个大胆的猜测，因为我不能再在我的终端上测试这些版本):

```
WARNING: Oops, something is wrong with what you just did!
on line 1 of path/to/file.scss, in `log'
ERROR: Function noop finished without @return
on line 1 of path/to/file.scss, in `log'
Use --trace for backtrace.
```

那似乎起作用了！在任何引擎中，即使是旧的引擎，编译器都会退出。在那些支持`@at-error`的机器上，他们会马上得到错误信息。对于那些没有这样做的，它们得到警告消息，然后由于`noop`函数编译崩溃。

## 使得记录内部函数成为可能

我们当前设置的唯一问题是我们不能从函数内部抛出错误，因为我们构建了一个 mixin。mixin 不能包含在函数中(因为它可能会打印 CSS 代码，这与 Sass 函数无关)！

如果我们把 mixin 转换成一个函数会怎么样？在这一点上，发生了一些奇怪的事情:`@error`在 Sass 3.3-中没有被识别为一个函数的有效 At 指令，因此很不幸地失败了:

> 函数只能包含变量声明和控制指令。

很公平。这意味着我们不再需要`noop` hack，因为不受支持的引擎会崩溃，而我们不必强迫它。尽管我们必须将`@warn`指令放在流程的上部，以便消息在崩溃前打印在作者的控制台中。

```
@function log($message) {
 @if feature-exists('at-error') != true {
 @warn $message;
  } @else {
 @error $message;
  }
}
```

然后，我们可以提供一个 mixin 来拥有一个比肮脏的空条件和虚拟变量 hacks 更友好的 API。

```
@mixin log($message) {
  // Because functions cannot be called anywhere in Sass,
  // we need to hack the call in a dummy condition.
  // There are other ways to do this, such as `log: log(..)`.
 @if log($message) {}
}
```

## 最后的想法

就是这样！我们现在可以在函数内部使用`log(..)`函数(由于限制)，在其他任何地方使用`log(..)` mixin 来负责任地抛出错误。相当整洁！

以下是完整的代码:

在 SassMeister 上玩这个要诀。