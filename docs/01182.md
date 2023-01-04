# 如何有效地使用 Sass 中的警告和错误

> 原文：<https://www.sitepoint.com/warnings-and-errors-in-sass/>

以下是雨果·吉劳德和米丽娅姆·苏珊娜写的《快速起步与 T2》一书的节选。这是 Sass 入门的终极指南。SitePoint Premium 会员可以通过他们的会员身份访问，或者您可以在世界各地的商店购买一份。

我们不可思议的萨斯之旅正慢慢接近尾声，到目前为止你做得很好！在我们查看项目架构之前，还有一个技术章节，然后您将完全有能力在自己的项目中编写 Sass 代码。

现在我们来看看警告和错误。两者都形成了程序(在本例中是 Sass)和开发人员(您)之间的单向通信系统。如果你想知道 CSS 世界中的错误点，记住你已经知道答案了。每当你忘记了分号或者错误地使用了一个函数，Sass 就会抛出一个错误，解释你做错了什么以及如何修复它，谢天谢地！如果不得不钻研代码来找出哪里出了问题，那将是一件非常痛苦的事情。

Sass 很早就提供了一种从样式表发出警告的方法，但是直到最近才增加了对抛出错误的支持——而且理由很充分！在过去的几年里，Sass 允许作者构建复杂的系统来抽象困难或重复的模式和概念，比如网格。这些系统必须能够与作者通信，如果出现任何问题，就用自定义的错误消息停止编译过程。

警告和错误都在当前输出通道中发出。当通过命令行界面(CLI)手工或使用工具编译 Sass 时，如 [Grunt](http://gruntjs.com/) 或 [Gulp](http://gulpjs.com/) ，输出流是控制台。对于包含用户界面的工具，比如 [Codekit](https://incident57.com/codekit/) 或 [Prepros](https://prepros.io/) ，它们很可能会捕捉并显示警告和错误，作为其界面的一部分。像 [CodePen](http://codepen.io/) 和 [SassMeister](http://sassmeister.com) 这样的在线游戏平台能够捕捉错误，但不能捕捉警告，所以如果你不能在那里测试它们，不要惊慌。

## 警告信息

如前所述，在 Sass 中发出警告的能力并不是新的。可以通过`@warn`指令向标准输出流显示消息或任何 SassScript 表达式的值。

一个**警告**对编译过程没有影响；这并不妨碍编译以任何方式追求或改变它。它的唯一目的是在控制台中显示一条消息。

在 Sass 中使用警告有很多原因。这里有几个，但你可能会找到自己的:

*   通知用户关于代码的假设，以避免意外和难以跟踪的错误
*   作为库或框架的一部分，建议不推荐使用的函数或 mixin

发送警告非常简单:从`@warn`指令开始，然后声明它是什么。警告通常是为了提供一些信息和背景，所以它们通常会有一个解释情况的句子。也就是说，你不必使用字符串；你可以用一个数字、一个列表、一张地图——无论什么——来警告。在这里，我们打印一个字符串:

```
@warn 'Uh-oh, something looks weird.'; 
```

使用常规 CLI 客户端，此警告将发出以下输出:

```
WARNING: Uh-oh, something looks weird.
         on line 1 of /Users/hgiraudel/jump-start-sass/warning.scss 
```

嘿，那很好，不是吗？虽然这个警告一点用都没有。它说某事看起来很奇怪，但没有说什么，为什么，或者可以做什么来阻止它看起来奇怪。我们将进一步讨论如何改进警告。

现在我们知道如何使用这个特性，让我们来看一个更严肃的例子。假设我们有一个 Sass 自定义函数，它试图转换以`em`为单位的像素值:

```
@function px-to-em($value, $base-font-size: 16px) {
  @return ($value / $base-font-size) * 1em;
}

// Usage
.foo {
  font-size: px-to-em(42px); // 2.625em
} 
```

一切都好。现在，当向函数传递一个无单位数(比如`42`)时会发生什么？也许你已经猜到了，但是因为不是很明显，我给你答案:

```
2.625em/px isn’t a valid CSS value. 
```

发生这种情况是因为您试图在不兼容的单位(`px`和`em`)之间执行计算。为了避免这个问题，我们可以假设无单位值用像素表示，并首先转换它:

```
@function px-to-em($value, $base-font-size: 16px) {
  @if unitless($value) {
    @warn 'Assuming value `#{$value}` to be in pixels; attempting to convert it.';
    $value: $value * 1px;

  }

  @return ($value / $base-font-size) * 1em;
} 
```

该函数需要一个以像素表示的值。我们仍然可以用一个没有单位的值使它工作；但是，我们不能确定这是预期的行为。我们只能假设它足够好。

因为我们假设什么是我们函数的正确行为，所以让开发人员知道我们在做什么以及为什么这样做是很重要的。否则可能会导致难以跟踪的错误，这不是你的目标。

另一个实际的例子是警告不要使用不赞成使用的函数或 mixin。你可能已经听说过或者使用过 [Bourbon](http://bourbon.io/) ，一个用于 Sass 的轻量级 mixin 库。波旁是积极维护的，有时需要从图书馆移除助手。为了避免突然打破一个人的颂歌，波旁威士忌在实际删除混音之前就警告未来的贬值:

```
@mixin inline-block {
  display: inline-block;

  @warn 'The `inline-block` mixin is deprecated and will be removed in the next major version release.';
} 
```

聪明！仍然在使用波旁酒的`inline-block` mixin 的人意识到这个库将在下一个版本中完全移除它，所以他们知道开始更新他们的代码库来移除 mixin。

### `@warn`和`@debug`的区别

您可能熟悉也可能不熟悉`@debug`指令，它以与`@warn`相同的方式将 SassScript 表达式的值打印到标准输出流中。您可能想知道为什么有两个功能执行相同的任务，这两者之间可能有什么区别。

嗯，警告一个值和调试一个值有两个主要区别。第一个是可以使用`quiet`选项关闭警告。另一方面，调试程序总是会被打印出来，这样当你用完它们的时候，你会记得把它们移除。

第二个区别是警告伴随着一个**堆栈跟踪**——一个在程序执行期间某个时间点的活动堆栈帧的报告。因此，你知道它们是从哪里发出的。调试只打印值，以及调用它们的行，但是它们不提供额外的信息。

当你想知道一个变量里面有什么时,`@debug`指令真的可以派上用场，例如:

```
@debug $base-font-size; 
```

## 错误

在 Sass 中，警告和错误的行为非常相似，所以既然您已经非常熟悉警告，那么了解错误将是轻而易举的事情！一个 **错误** 和一个警告之间的唯一区别是——你可能已经猜到了——错误会停止编译过程。

例如，在验证 mixins 和函数的参数时，使用错误会很方便。在前面的部分中，即使给定的参数不完全符合预期，这也是可行的，但是我们不能(也不应该)总是这样做。大多数情况下，如果参数无效，最好抛出一个错误，以便样式表作者可以解决问题。

您可以使用`@error`指令抛出一个错误。至于警告，您可以向该指令传递任何内容—不一定是字符串，尽管提供清晰的上下文通常更有意义。参数(您赋予`@error`指令的内容)将打印在标准输出流中，同时还有一个堆栈跟踪来提供关于问题的更多信息。编译过程将立即停止。

让我们从甘道夫认可的错误开始:

```
@error 'YOUUUUU! SHALL NOT. PASS.'; 
```

输出可能取决于您编译样式表的方式，因为有些工具会以某种方式捕捉和增强错误。使用标准的`sass` Ruby 二进制文件(gem)，它看起来是这样的:

```
Error: YOUUUUU! SHALL NOT. PASS.
        on line 1 of /Users/hgiraudel/jump-start-sass/error.scss
  Use --trace for backtrace. 
```

使用`trace`选项，您可以从 Sass 本身获得完整的
堆栈跟踪，这没有多大用处，除非在预处理器的某个地方有一个
实际错误。因此它被隐藏为默认的
。

时间来看看 *真实* 的实际例子。让我们从编写一个小函数来帮助访问地图中的深度值开始，`map-deep-get(..)`:

```
@function map-deep-get($map, $keys...) {
  @each $key in $keys {
    $map: map-get($map, $key);

    @if (type-of($map) == 'null') {
      @return $map;
    }
  }

  @return $map;
}
```

让我们用自定义错误来增强它。但是首先，考虑下面的地图和`map-deep-get(..)`调用:

```
$map: (
  'foo': (
    'bar': (
      'baz': 42
    )
  )
);

$value: map-deep-get($map, 'foo', 'bar', 'baz', 'qux');
```

您可能已经注意到，地图缺少嵌套在`baz`中的`qux`键。的确，`baz`甚至与地图也没有关联；而是映射到一个数字(`42`)。如果我们尝试执行这段代码，它将产生:

```
Error: 42 is not a map for `map-get`
        on line 1 of /Users/hgiraudel/jump-start-sass/error.scss 
```

Sass 试图对`42`执行一个`map-get(..)`并发出一个错误，因为它无法完成。虽然错误消息是正确的，但它不是很有帮助。知道导致问题的键的名称会有所帮助。我们能做到！

我们已经检查了`$map`是否为`null`以执行提前返回，从而避免在键不存在的情况下出现编译错误。我们可以执行第二次检查，以确保该地图实际上是一个地图，否则我们会抛出一个有意义的错误:

```
@function map-deep-get($map, $keys...) {
  @each $key in $keys {
    $map: map-get($map, $key);

    // If `$map` does not contain the next key, return `null`
    @if type-of($map) == 'null' {
      @return $map;
    }

    // If `$map` is not a map, throw an error
    @if type-of($map) != 'map' {
      @error 'Key `#{$key}` is not associated with a map but a #{type-of($map)} (`#{$map}`).';
    }
  }

  @return $map;
} 
```

如果我们再次运行前面的代码片段，输出如下:

```
Error: Key `baz` is not associated with a map but a number (`42`).
        on line 1 of /Users/hgiraudel/jump-start-sass/error.scss 
```

那就好多了！由于有用的错误消息，现在很容易修复我们的映射和/或函数调用。

## 包装东西

在本章中，我们学习了如何使用 Sass 在标准输出流中发出警告并抛出错误。这通常是控制台，但它可能会根据编译样式表的方式而有所不同。

警告有助于向样式表作者发出不重要的消息——特别是对于框架和库作者——例如不推荐使用警告或代码假设。另一方面，错误被用来阻止编译继续进行，这清楚地表明代码需要在继续下一步之前被修复。

总之，警告和错误在函数和 mixins 中特别有用，可以验证用户输入，确保样式表按照预期编译。

## 分享这篇文章