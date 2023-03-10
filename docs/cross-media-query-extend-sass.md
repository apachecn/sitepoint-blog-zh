# Sass 中的跨媒体查询@extend 指令

> 原文：<https://www.sitepoint.com/cross-media-query-extend-sass/>

如果你读过[我以前关于 Sass 的@extend 指令](https://www.sitepoint.com/sass-extend-nobody-told-you/)的文章，或者如果你对 Sass 的工作方式有相当扎实的了解，你可能会意识到你不能这样做:

```
%example {
  color: blue;
  font-weight: bold;
  font-size: 2em;
}

@media (max-width: 800px) {
  .generic-class {
    @extend %example;
  }
}
```

这里发生的事情是，我们试图从另一个范围(在本例中是根)扩展媒体查询中的占位符。它就是不工作，[如果你尝试它](https://sassmeister.com/gist/10003500)你会得到下面的错误信息:

> 您不能从@media 内部@扩展外部选择器。在同一个指令中只能@extend 选择器。

太糟糕了，不是吗？所以有一天，我卷起袖子，深入研究代码，寻找一种方法来创建一个适用于各种媒体查询的`@extend`指令。这是一次奇妙的旅程，最终我找到了一个不那么糟糕的解决方案。然而，请记住，这项技术基本上是一个黑客，所以它主要是实验性的。但是如果您不能等待本地 Sass 解决方案，我想您可以尝试一下。

## 它是如何工作的？

这是一个非常复杂的解决方案，因此您可能需要反复检查几次。首先，它依赖于从一开始就知道现有的`@media`范围。一般来说，这不应该是个问题，因为我们的代码通常会有[一个命名断点列表](https://css-tricks.com/naming-media-queries/)。接下来，关键是在每个作用域中创建相同的占位符，这样无论我们当前在哪个作用域中，我们都可以使用占位符。然后，棘手的部分是知道我们当前在哪个范围内。比如:我们是在根层次吗？我们是在一个*小*断点里面吗？一个*大的*一个？不是一个简单的任务。

因为这相当复杂，所以产生的 Sass 代码相当重。也就是说，我有一个座右铭:

> 核心代码再复杂也没关系，只要 API 简单就好。

所以这就是我真正想要的:一个简单的 API，这样开发者就不必做大量复杂的事情来扩展媒体查询的占位符。

在开始逐步构建我们的 API 所基于的代码之前，让我们先来看看 API 本身，将其与我们通常使用占位符所做的事情进行比较:

```
/* Regular single-scope placeholder */
%my-placeholder {
  property: value;
}

/* Enhanced multi-scope placeholder */
@include placeholder(my-placeholder) {
  property: value;
}
```

虽然多作用域版本(上面的第二个例子)比标准占位符语法要长一点，但它同样容易理解。此外，它稍微长一点也没什么关系，因为你不会每两分钟就创建一个占位符。为了简化，您可以将其重命名为`ph()`甚至`p()`，或者为其创建别名。

现在我们已经定义了占位符，我们可以扩展它。下面是我们的做法，首先举例说明在没有跨范围优势的情况下，您通常会如何做:

```
/* Regular @extend, with cross-scope limitations */
@extend %my-placeholder;

/* Enhanced cross-scope @extend */
@include _(my-placeholder);
```

好了，现在我知道这看起来很奇怪，但是在吐之前，听我说:因为我们需要将占位符*的名称作为参数传递给 mixin，所以我给了 mixin(稍后您将看到它的定义)一个非常短的名称(在本例中，是一个字符)。这使得它简单易用。*

起初，我想使用`%()`来与更常见的占位符语法保持一致。然而，这样做需要用反斜杠`\`(例如`\%(my-placeholder)`)对%字符进行转义。这将违背目的，并使它成为一个痛苦的使用。因此有了下划线:`_()`。

*注意:记住 [Sass 认为下划线和连字符是一样的](https://sass-lang.com/documentation/file.SASS_REFERENCE.html#keyword_arguments)。所以你可以用`@include -(my-placeholder)`来代替。否则，请随意创建您想要的别名— `extend()`、`gloubiboulga()`、`god-i-love-sass-so-much()`或其他名称。*

最后我觉得 API 也没那么差。一开始可能看起来很奇怪，但我想你会很快习惯的，所以应该足够好了。

## 为我们的 API 编写库

让我们总结一下为了使整个事情顺利进行，我们需要什么:

1.  一些设置，包括断点列表
2.  一个处理媒体查询的 mixin(姑且称之为`breakpoint()`)
3.  一个 mixin 生成一个占位符(`placeholder()`)
4.  扩展占位符(`_()`)的 mixin

从这一点开始，您可以按照代码出现的顺序将每个代码示例复制并粘贴到一个 Sass 文件中(或者粘贴到一个类似于 [SassMeister](https://sassmeister.com/) 的工具中)，慢慢地构建前面几节中讨论的 API 所使用的代码。你也可以在 SassMeister 上查看最终代码[。](https://sassmeister.com/gist/9638030)

### 设置和配置

第一，配置。正如我所说的，从一开始我们就需要知道不同的断点(这是我们的媒体查询范围)。为此，我们可以使用一个简单的映射将一个关键字与一个`max-width`值关联起来。像这样:

```
$breakpoints: (
  "small"  : 600px,
  "medium" : 900px,
  "large"  : 1200px
);
```

当然，您可以选择您想要的名称和宽度值。我决定用 3 个非常基本的关键字和 3 个标准宽度来简化演示，但是你可以随意做。

接下来，我们需要一个变量来存储我们所在的当前断点(或范围)。姑且称之为`$current-breakpoint`。我们还需要给它一个默认值，可以是任何值。我决定称之为`root`，因为我们从根级别开始。

```
$default-breakpoint: root;
    $current-breakpoint: $default-breakpoint;
```

最后，我们需要一个变量来存储所有生成的占位符的名称。别担心，这个变量会自动填充。首先，占位符不存在，所以我们将其初始化为一个空列表。

```
$placeholders: ();
```

我想我们已经准备好了！

## 媒体质疑混音

媒体查询 mixin 意在[开启一个新的范围](https://css-tricks.com/media-queries-sass-3-2-and-codekit/)。这意味着不是用`@media (min-width: 900px) {}`手动打开一个`@media`范围，而是通过传递一个关键字(例如`@include breakpoint(small) {}`)来包含`breakpoint` mixin。当然，关键字必须在`$breakpoint`地图中定义(这是它的全部意义)。然后，mixin 为我们打开了正确的范围。

有趣的是这个 mixin 还更新了`$current-breakpoint`变量来跟踪当前的作用域。下面是代码，通过注释使每一部分都清晰明了:

```
@mixin breakpoint($breakpoint) {
      // Get the width from the keyword `$breakpoint`
      // Or `null` if the keyword doesn't exist in `$breakpoints` map
      $value: map-get($breakpoints, $breakpoint);

  // If `$breakpoint` exists as a key in `$breakpoints`
  @if $value != null {
        // Update `$current-breakpoint`
        $current-breakpoint: $breakpoint !global;

    // Open a media query block
    @media (min-width: $value) {
      // Let the user dump content
      @content;
    }

    // Then reset `$current-breakpoint` to `$default-breakpoint` (root)
    $current-breakpoint: $default-breakpoint !global;
  }

  // If `$breakpoint` doesn't exist in `$breakpoints`, 
  // Warn the user and do nothing
  @else {
    @warn "Invalid breakpoint `#{$breakpoint}`.";
  }
}
```

正如你所看到的，就在转储用户内容(`@content`)之前，我们用传递给 mixin 的关键字更新了`$current-breakpoint`变量(因此在我们的例子中是`small`、`medium`或`large`)，在转储之后，我们将其重置为`root`(即我们的`$default-breakpoint`变量)。

*注意:`!global`标志[已经被添加到 Sass 3.3](https://blog.sass-lang.com/posts/184094-sass-33-is-released) 中，以区分定义作用域变量和重定义全局变量。在我们的例子中，我们覆盖了全局的`$current-breakpoint`。*

## 生成占位符的 Mixin

我们的 mixin 相当简单。但是，请注意我们在做任何事情之前进行的一项验证:我们只在占位符不存在时才生成占位符。为了确保它们不会，我们对照现有占位符列表(`$placeholders`)检查传递给 mixin 的名称。如果它还不存在于列表中，我们添加它，然后生成占位符。这确保占位符不会生成两次，否则会导致错误。

```
@mixin placeholder($name) {
      // If placeholder doesn't exist yet in `$placeholders` list
      @if not index($placeholders, $name) {
        // Store its name
        $placeholders: append($placeholders, $name) !global;

    // At root level
    @at-root {
      // Looping through `$breakpoints`
      @each $breakpoint, $value in $breakpoints {
            // Opening a media query block
            @media (min-width: $value) {
              // Generating a placeholder
              // Called $name-$breakpoint 
              %#{$name}-#{$breakpoint} {
            @content;
          }
        }
      }

      // And dumping a placeholder out of any media query as well
      // so basically at root level
      %#{$name}-#{$default-breakpoint} {
        @content;
      }
    }
  }

  // If placeholder already exists, just warn the user
  @else {
    @warn "Placeholder `#{$name}` already exists.";
  }
}
```

## 扩展占位符

我们所剩下的就是我们的短 mixin 来扩展占位符，不管我们在哪个媒体块中:

```
@mixin _($name) {
      @extend %#{$name}-#{$current-breakpoint} !optional;
}
```

这是我们实际使用`$current-breakpoint`值的地方，从我们所在的范围扩展准确的占位符。[这里的`!optional`标志只是作为一种安全措施。如果，出于任何原因，占位符不存在(这不应该发生，但是你永远不知道)，那么`@extend`不会崩溃。](https://sass-lang.com/documentation/file.SASS_REFERENCE.html#the__flag)

## 一个工作实例

让我们创建一个简单的例子作为概念验证:clearfix 占位符。我们会保持简单；它转储一个`clear: both`用于简单浮点清除，转储一个`overflow: hidden`用于内部浮点清除。那肯定不是最好的 clearfix 方法；我们只是用它来达到我们的目的。

首先，我们需要创建占位符:

```
@include placeholder('clear') {
  clear: both;
  overflow: hidden;
}
```

现在我们可以使用它:

```
.a {
  @include _(clear);
}

.b {
  @include _(clear);
}

.c {
  @include breakpoint(medium) {
    @include _(clear);
  }
}

@include breakpoint(medium) {
  .d {
    @include _(clear);
  }
}

.e {
  @include _(clear);

  @include breakpoint(large) {
    @include _(clear);
  }
}
```

这将产生以下 CSS:

```
@media (min-width: 900px) {
  .c, .d {
    clear: both;
    overflow: hidden;
  }
}
@media (min-width: 1200px) {
  .e {
    clear: both;
    overflow: hidden;
  }
}
.a, .b, .e {
  clear: both;
  overflow: hidden;
}
```

不错，是吗？

## 最后的想法

所以最后，我们设法创建了一个 mixin，它能够扩展一个占位符，而不管我们在哪个`@media`块中，从而产生一些整洁和优化的 CSS 输出。同样，在我看来，API 并不比通常的`%placeholder {}` / `@extend %placeholder`工作流复杂多少。

现在，它真的有用吗？如果你问我，我不确定。就我而言，我还没有遇到过真正需要跨范围`@extend`的情况。我想这在我身上只发生过一次，我设法没有太大困难地解决了这个问题。我觉得混合和占位符通常定义了元素的核心，这意味着它们应该应用于根级别，而不是精确地应用于给定的断点——至少，我在代码中是这样做的。

除此之外，当陷入无法访问根占位符的`@media`块时，使用 mixin 可能要简单得多。此外，gzip 积极地粉碎重复的字符串，所以我不确定当我们关心最终文件大小时，使用 mixins 而不是占位符是不是 T2 的坏主意，但那是另一个故事了。

无论如何，这是一个有趣的实验。希望你喜欢它！对了，你可以在萨斯迈斯特玩代码！

在 SassMeister 上玩这个要诀。