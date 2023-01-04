# 使用 Sass 的@error、@warn 和@debug 指令

> 原文：<https://www.sitepoint.com/using-sass-error-warn-and-debug-directives/>

反馈方法在任何编程语言中都是必不可少的。在 JavaScript 中，你可能用过`console.log()`或者`alert()`。在 PHP 中，你可以使用`var_dump()`或者`print_r()`。在 Ruby 中，你可以使用`debug`或者`inspect`。所有这些函数都允许您调试任何值，并在逻辑中任何需要帮助的地方找出您的代码在做什么。

Sass 有三个向开发人员提供反馈的指令。他们是`@error`、`@warn`和`@debug`。在这篇文章中，我们将看看如何使用这些指令，它们最适合什么用例，以及它们可以为使用我们代码的其他开发人员提供什么样的反馈。

## 基本语法和用法

这三个指令遵循相同的语法:

```
@directive "String of text to output.";
```

嗯，不完全是这样。这三个指令期望任何内容，不一定是字符串。这意味着您可以警告、抛出或调试一个映射、一个列表、一个数字、一个字符串——基本上是您想要的任何东西。然而，由于我们经常使用这些指令来给出问题的一些上下文，我们通常会传递一个描述这种情况的字符串。

如果您需要在该字符串中插入变量值，您可以使用标准的 Sass 插值(`#{$variable}`)来完成，该值将打印在字符串中。使用这种方法，您可以告诉开发人员变量的名称及其当前值:

```
@error "Sorry, but `#{$variable}` is not a valid value for $variable.";
```

*注意，插值周围的记号(`)不是必需的。您可能希望包含它们，因为它们为开发人员提供了一个明显的变量内容的开始/停止点。*

如果开发人员在使用您的 Sass 代码时出错，这些指令会将指定的消息发送给编译器，编译器会将该消息显示给开发人员。例如，GUI 应用程序(如 [CodeKit](https://incident57.com/codekit/) )会显示一个带有错误的系统通知。某些咕噜咕噜的通知包也可以做到这一点。

如果开发人员使用命令行(Sass、Compass、Grunt 或 Gulp)进行编译，消息很可能会出现在他们的控制台(终端、iTerm2、Putty 等)中。如果你用 [Sassmeister](http://sassmeister.com) 或 [Codepen](http://codepen.io) 在线写 Sass，你只会得到有限的反馈，但你可能会在你的编辑器中得到一个内嵌通知或在输出窗口中得到文本。

既然我们已经知道了如何编写`@error`、`@warn`和`@debug`消息，以及它们将出现在开发人员面前的位置，那么让我们来看看这些指令之间的区别以及它们各自的最佳用途。

## 停止一切——指令

Sass 的[@错误指令](https://www.sitepoint.com/introducing-the-sitepoint-sass-reference/)完全停止 Sass 编译器，并将其文本字符串作为致命错误发送到编译器的输出。当您需要发送一条消息来立即停止开发人员并迫使他们立即纠正错误时，请使用此指令。这对于让开发人员知道他们做错了什么或者输入了完全不兼容的值是非常理想的。Sass 将包含任何致命错误的行号，包括`@error`输出。`@error`指令非常适合验证 mixin 或函数中的参数。

*注意:如果您的编译器比 Sass 3.4 或 LibSass 3.1 旧，`@error`不可用。你可以使用[这个`log()`函数](https://www.sitepoint.com/using-error-responsibly-in-sass/)来模仿旧版本 Sass 中的`@error`。*

举个例子，如果你写了一个依赖于某种输入格式进行计算的函数，你可以验证输入并使用`@error`来停止编译器并提醒开发者他们犯了一个不可忽视的错误。如果您编写了一个依赖于映射中的键/值对的函数，`@error`可以确保从映射中调用的键确实存在。

[这个 Sass 颜色管理工具](https://www.sitepoint.com/managing-color-values-with-sass/)使用`@error`来确保开发者只使用一个有效的 CSS 颜色名称或者一个来自相关地图的键:

**地图:**

```
$colors: (
 brand-red: #c0392b,
 brand-blue: #2980b9,
 text-gray: #2c3e50,
 text-silver: #bdc3c7
);
```

**功能(为简明起见而简化):**

```
@function color-variation($color) {
 @if map-has-key($colors, $color) {
 @return map-get($colors, $color);
  }

 @error "Invalid color name: `#{$color}`.";
}
```

**用法:**

```
.element {
 color: color-variation(brand-orange);
}
```

**输出:**

```
>> Invalid color name: `brand-orange`.
>>   Line 9  Column 7  sass/test.scss
```

在这个例子中，如果开发人员试图用一个既不是有效 CSS 颜色也不是来自`$colors`映射的键的字符串调用`color-variation()`函数，Sass 无法输出有效的颜色。使用`@error`给开发者具体的、可操作的反馈。

## 不要错过这个——指令

[@warn 指令](https://www.sitepoint.com/introducing-the-sitepoint-sass-reference/)远没有`@error`严厉。它将消息发送给编译器供开发人员阅读，但它允许编译器完成其工作并编写所有 CSS。虽然`@error`适用于纠正完全破坏函数或 mixin 的错误，但是`@warn`更适用于反对通知，或者建议开发人员遵循某些最佳实践。

*注意:在编译时使用`--quiet`标志的 Sass 开发者不会看到`@warn`输出。如果开发人员绝对需要看到你的 Sass 发出的反馈，请依靠`@error`。`@warn`关了很少见，但有可能。*

Sass 还将包含一个带有`@warn`输出的行号，以帮助开发人员找到并改进任何需要编辑的代码。例如，您可能有一个装满 mixin 的库，并且您计划在未来的版本中弃用其中的几个 mixin。帮助您的开发人员为这种变化做好准备的一个方法是`@warn`告诉他们即将到来的弃用:

```
// No one should ever prefix border-radius properties ;)
// so let's deprecate this from our imaginary library:

@mixin border-radius($radius) {
 @warn "The `border-radius()` mixin will be deprecated in version 2.0.";

 -webkit-border-radius: $radius;
 -moz-border-radius: $radius;
 -ms-border-radius: $radius;
 -o-border-radius: $radius;
 border-radius: $radius;
}
```

**输出:**

```
WARNING: The `border-radius()` mixin will be deprecated in version 2.0.
  on line 2 of sass/test.scss
  from line 21 of sass/test.scss
```

你可以在波旁萨斯的[这一部分](https://github.com/thoughtbot/bourbon/blob/master/app/assets/stylesheets/_bourbon-deprecated-upcoming.scss#l6)中看到这一点。

`@warn`的另一个好的用例是执行团队代码标准。您可能会编写一个函数，可以成功地处理像素或 rem 单位的输入，但是您可能更希望团队中的每个人都使用 rems。这里有一个`@warn`的例子，可以帮助你的同事遵循这个标准:

```
@function do-math($input) {
 @if unit($input) == "px" {
 @warn "Please remember to use rem values in the `do-math()` function.";
    // do px math and @return a value to prevent the function from going any further
  }

  // do rem math and @return a value
}
```

**输出:**

```
WARNING: Please remember to use rem values in the `do-math()` function.
  on line 3 of sass/test.scss
  from line 12 of sass/test.scss
```

## 如果你好奇的话——`@debug`指令

萨斯的 [@debug 指令](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_5)是所有三个反馈指令中干扰最小的。它打印它包含的任何 Sass 表达式的值(变量、数学等。)到开发人员的控制台。它在开源或团队库中并不完全有用。相反，`@debug`非常适合个人调试工作。如果你在做一个复杂的数学运算，需要知道你的一个变量现在包含什么，使用`@debug`来找出答案。

**用法:**

```
$color-blue: #1c94c6;
$font-sizes: sm, p, bq, heading, hero;
$colors: (
 brand-red: #c0392b,
 brand-blue: #2980b9,
 text-gray: #2c3e50,
 text-silver: #bdc3c7
);

.element {
 @debug $color-blue; // single value
 @debug $font-sizes; // list
 @debug $colors; // map
 @debug 4em * 3; // math expression
 @debug "My very own string just because."; // string
}
```

**输出:**

```
sass/test.scss:5: DEBUG: #1c94c6
sass/test.scss:6: DEBUG: sm, p, bq, heading, hero
sass/test.scss:7: DEBUG: (brand-red: #c0392b, brand-blue: #2980b9, text-gray: #2c3e50, text-silver: #bdc3c7)
sass/test.scss:8: DEBUG: 12em
sass/test.scss:9: DEBUG: My very own string just because.
```

## 结论

在没有反馈的黑洞中编程将是可怕的。幸运的是，Sass 有多个指令可以向编译器发送反馈，以帮助开发人员避免错误、维护标准和排除高级逻辑故障。您可以使用`@error`、`@warn`和`@debug`为您自己和任何使用您代码的人提供节省时间的反馈。

你对这些指令有创造性的使用吗？在评论中分享吧！

## 分享这篇文章