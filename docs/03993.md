# 萨斯主题:永无止境的故事

> 原文：<https://www.sitepoint.com/sass-theming-neverending-story/>

构建网格系统和主题引擎是一个永无止境的 Sass 故事。如今，支持 Sass 的网格系统可能和 JavaScript 框架一样多，而且几乎每个人都有自己的方式来处理样式表中的颜色主题。

在今天的文章中，我想解决后一个问题，并快速总结一下用 Sass 构建配色方案的几种方法。但是在继续之前，我们到底在谈论什么？

假设我们有一个组件，比如经典的[媒体对象](http://www.stubbornella.org/content/2010/06/25/the-media-object-saves-hundreds-of-lines-of-code/)。现在让我们假设这个组件有不同的配色方案，例如每个类别有一个调色板。对于类别 A，我们的对象会有一个红色的标题和一个红色的边框，对于类别 B，我们的对象会有一个更蓝的调色板。

最后，假设你不是有两个主题，而是有一打，以及组件内的许多颜色变化。您可能会承认手动管理这是非常不切实际的，这肯定是您希望用工具来自动化的事情。在我们的例子中，这个工具就是 Sass。

好了，现在我们准备好了。虽然分类有点无聊，所以让我们引入一些独角兽和龙。我们的任何方法都将依赖于主题地图。每个主题都是一个子贴图，包含映射到实际颜色的键。

```
$themes: (
  'unicorn': (
    'primary': hotpink,
    'secondary': pink
  ),
  'dragon': (
    'primary': firebrick,
    'secondary': red
  )
) !default;
```

我们的每个主题都由两种颜色组成:主色和次色。我们可以给它们起不同的名字，比如*阿尔法*和*贝塔*，这没关系。例如，重点只是能够获得“主题的主色”。

## 主题化实际上是如何工作的？

当然有不同的方式，但是通常，一个主题只不过是一个绑定了一些特定风格的类。这个类既可以添加到`body`元素中来包装整个页面，也可以添加到特定的组件中来将主题化为一个小模块，就像我们之前考虑的那样。

总而言之，在您的样式表中，您可能会期望这两种变体中的任何一种:

```
.theme-class .component {
  /* Style for the component when child of `.theme-class` */
}

.component.theme-class {
  /* Style for the component when has `.theme-class` */
}
```

## 个体混合方法

让我们从一个简单的开始，可能也是我最喜欢的:个体混合方法。简单地说，您有两个 mixins，它们是以它们想要设计的属性命名的。例如，`theme-color`主题化颜色或`theme-background-color`主题化背景。

使用这些 mixins 可能看起来像这样:

```
/**
 * Media object
 * 1\. Make the border-color use the secondary color of the theme
 */
.media {
 margin: 15px;
 padding: 15px 0;
 border-top: 5px solid;
 float: left;
 @include border-color('secondary'); /* 1 */
}

/**
 * Media title
 * 1\. Make the heading color use the primary color of the theme
 */
.media__title {
 font-size: 1em;
 margin: 0 0 10px;
 @include color('primary'); /* 1 */
}
```

你不得不承认这很优雅。而且，我感觉这是*明显的*，即使没有评论。

### 密码

让我们来看看如何建立这个。我们不打算在每个单独的 mixin 中重复代码逻辑，所以我们需要一个私有的 mixin 来收集所有的代码逻辑，然后在每个后续的 mixin 中使用。

```
/// Themify mixin
/// @access private
/// @author Hugo Giraudel
/// @param {String} $property - Property to themify
/// @param {String} $key - Key color to use from theme
/// @param {Map} $themes [$themes] - Map of themes to use
@mixin themify($property, $key, $themes: $themes) {
  // Iterate over the themes
 @each $theme, $colors in $themes {
    // Create a selector (e.g. `.media.theme-unicorn, .theme-unicorn .media`)
    &.theme-#{$theme},
    .theme-#{$theme} & {
      // Output the declaration
 #{$property}: map-get($colors, $key);
    }
  }
}
```

而现在，还是公共 API:

```
/// Shorthand to themify color through `themify` mixin
/// @access public
/// @see {mixin} themify
@mixin color($arguments...) {
 @include themify('color', $arguments...);
}

/// Shorthand to themify border-color through `themify` mixin
/// @access public
/// @see {mixin} themify
@mixin border-color($arguments...) {
 @include themify('border-color', $arguments...);
}

/// Shorthand to themify background-color through `themify` mixin
/// @access public
/// @see {mixin} themify
@mixin background-color($arguments...) {
 @include themify('background-color', $arguments...);
}
```

就是这样。回到我们之前的例子，下面是 CSS 输出的样子:

```
.media {
  margin: 15px;
  padding: 15px 0;
  border-top: 5px solid;
  float: left;
}

.media.theme-unicorn,
.theme-unicorn .media {
  border-color: pink;
}

.media.theme-dragon,
.theme-dragon .media {
  border-color: red;
}

.media__title {
  font-size: 1em;
  margin: 0 0 10px;
}

.media__title.theme-unicorn,
.theme-unicorn .media__title {
  color: hotpink;
}

.media__title.theme-dragon,
.theme-dragon .media__title {
  color: firebrick;
}
```

### 赞成的意见

*   多亏了属性命名的 mixins，API 既干净又清晰，即使对于没有经验的开发人员也是如此。

### 骗局

*   这种方法涉及到几个 mixinss，而不是一个可能或可能不被认为是额外复杂的 mixin，即使它们中的大多数只是克隆。
*   因为颜色是根据它们的键直接从颜色图中提取的，所以不可能用颜色函数如`lighten`或`mix`来操作它们。不过，用 mixin 的扩展版本可能是可行的。

## 块混合方法

块 mixin 方法使用一个 mixin 而不是几个 mixin，并且严重依赖于使用`@content`指令。使用它看起来会像这样:

```
/**
 * Media object
 * 1\. Make the border-color use the secondary color of the theme
 */
.media {
 margin: 15px;
 padding: 15px 0;
 border-top: 5px solid;
 float: left;
 @include themify {
 border-color: $color-secondary; /* 1 */
  }
}

/**
 * Media title
 * 1\. Make the heading color use the primary color of the theme
 */
.media__title {
 font-size: 1em;
 margin: 0 0 10px;
 @include themify {
 color: $color-primary; /* 1 */
  }
}
```

### 密码

想法很简单:将两种颜色作为变量暴露在`themify` mixin 中。问题是我们实际上无法以干净的方式做到这一点。根据文档，mixin 中定义的变量对于通过`@content`传递的内容是不可访问的:

> 传递给 mixin 的内容块在定义该块的范围内进行评估，而不是在 mixin 的范围内。这意味着 mixin **的局部变量不能在传递的样式块中使用，变量将解析为全局值。**

因为这个限制，我们不得不绕开它。解决方法实际上并不复杂:在输出`@content`之前，我们为主题中的每种颜色定义一个全局变量，在输出`@content`之后，我们取消设置这些变量。这样，他们只能在`themify`呼叫中访问。

```
// Initialize our variables as `null` so that when used outside of `themify`,
// they actually output nothing.
$color-primary: null;
$color-secondary: null;

/// Themify mixin
/// @author Hugo Giraudel
/// @param {Map} $themes [$themes] - Map of themes to use
@mixin themify($themes: $themes) {
  // Iterate over the themes
 @each $theme, $colors in $themes {
    // Create a selector (e.g. `.media.theme-unicorn, .theme-unicorn .media`)
    &.theme-#{$theme},
    .theme-#{$theme} & {
      // Set the theme variables with `!global`
 $color-primary: map-get($colors, 'primary') !global;
 $color-secondary: map-get($colors, 'secondary') !global;

      // Output user content
 @content;

      // Unset the theme variables with `!global`
 $color-primary: null !global;
 $color-secondary: null !global;
    }
  }
}
```

我们前面的例子看起来与第一种方法完全一样:

```
.media {
  margin: 15px;
  padding: 15px 0;
  border-top: 5px solid;
  float: left;
}

.media.theme-unicorn,
.theme-unicorn .media {
  border-color: pink;
}

.media.theme-dragon,
.theme-dragon .media {
  border-color: red;
}

.media__title {
  font-size: 1em;
  margin: 0 0 10px;
}

.media__title.theme-unicorn,
.theme-unicorn .media__title {
  color: hotpink;
}

.media__title.theme-dragon,
.theme-dragon .media__title {
  color: firebrick;
}
```

### 赞成的意见

*   与单独的混合解决方案相反，块混合提供了用函数操纵颜色的能力，因为我们可以直接访问存储在主题变量中的颜色。
*   我觉得 API 仍然可以很快理解这种方法，特别是因为我们在 mixin 调用中使用了实际的 CSS 声明，这对于一些人来说可能更容易理解。

### 骗局

*   我必须说，以这种方式使用全局变量有点不太好。没什么大不了的，但是代码质量并不是真正的优势。

## 大 ol '主题混合方法

这种方法实际上是我已经在 SitePoint 上写过的东西，在去年的[这篇文章中。这个想法是你有一个很大的混合，每次你需要一些东西被*主题化*时，你就更新它。](https://www.sitepoint.com/dealing-color-schemes-sass/)

这意味着这个 mixin 对于项目中的所有组件都是相同的。如果您想以某种方式使这个新组件主题化，您需要打开`themify` mixin 所在的文件，并在其中添加一些额外的规则。

```
// Somewhere in the project, the `themify` mixin
@mixin themify($theme, $colors) {
  // See `Code` section
} @include themify;

/**
 * Media object
 */
.media {
 margin: 15px;
 padding: 15px 0;
 border-top: 5px solid;
 float: left;
}

/**
 * Media title
 */
.media__title {
 font-size: 1em;
 margin: 0 0 10px;
}

@each $theme, $colors in $themes {
 @include themify($theme, $colors);
}
```

### 密码

```
/// Themify mixin
/// @author Hugo Giraudel
/// @param {String} $theme - Theme to print
/// @param {Map} $colors - Theme colors
@mixin themify($theme, $colors) {
  // Output a theme selector
  .theme-#{$theme} {
    // Create the two variations of our selector
    // e.g. `.theme .component, .theme.component`
    .media,
    &.media {
 border-color: map-get($colors, 'primary');
    }

    .media__title
    &.media__title {
 color: map-get($colors, 'secondary');
    }
  }
}
```

### 赞成的意见

*   因为 mixin 内容是手动维护的，所以选择器非常灵活。在那里你可以做任何你想做的事情。
*   出于同样的原因，它还提供了使用类似`darken`的功能来操纵颜色的能力。

### 骗局

*   因为所有主题都存储在这个 mixin 的内容中，所以它不太适合模块化方法。不过，对于使用全局样式表的中小型项目来说，这可能是没问题的。
*   将组件样式分成几个部分可能会令人困惑，例如模块样式表和主题混合。

## 课堂教学方法

类方法实际上是 DOM 驱动的。其思想是，不要从样式表中应用特定的主题样式，而是将主题类添加到标记中，比如`border-color-primary`。这些类是用 Sass 生成的，它们本身什么也不做，但是在与我们的永恒的`theme-$theme`类结合使用时会应用一些样式。

你可以在 Harry Roberts 的这篇文章中读到更多关于这个系统的信息。

```
<div class="media  theme-unicorn  border-color-primary">
  <img class="media__image" src="https://lorempixel.com/100/100" />
  <h2 class="media__title  color-secondary">This is the headline</h2>
  <p class="media__content">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Provident nulla voluptatibus quisquam tenetur quas quidem, repudiandae vel beatae iure odit odio quae.</p>
</div>
```

```
// Somewhere in the stylesheet (once)
@mixin themify($themes: $themes) {
  // See `Code` section
} @include themify($themes);

/**
 * Media object
 */
.media {
 margin: 15px;
 padding: 15px 0;
 border-top: 5px solid;
 float: left;
}

/**
 * Media title
 */
.media__title {
 font-size: 1em;
 margin: 0 0 10px;
}
```

### 密码

```
/// Themify mixin
/// @param {Map} $themes [$themes] - Map of themes to use
@mixin themify($themes: $themes) {
  // Properties to output, more can be added (e.g. `border-left-color`)
 $properties: ('border-color', 'background-color', 'color');

  // Iterate over the themes
 @each $theme, $colors in $themes {
    // Iterate over the colors from the theme
 @each $color-name, $color in $colors {
      // Iterate over the properties
 @each $property in $properties {
        // Create a selector
        // e.g. `.theme .color-primary, .theme.color-primary`
        .theme-#{$theme} .#{$property}-#{$color-name},
        .theme-#{$theme}.#{$property}-#{$color-name} {
 #{$property}: $color;
        }
      }
    }
  }
}
```

这段代码的输出将是:

```
.theme-unicorn .border-color-primary,
.theme-unicorn.border-color-primary {
  border-color: hotpink;
}

.theme-unicorn .background-color-primary,
.theme-unicorn.background-color-primary {
  background-color: hotpink;
}

.theme-unicorn .color-primary,
.theme-unicorn.color-primary {
  color: hotpink;
}

.theme-unicorn .border-color-secondary,
.theme-unicorn.border-color-secondary {
  border-color: pink;
}

.theme-unicorn .background-color-secondary,
.theme-unicorn.background-color-secondary {
  background-color: pink;
}

.theme-unicorn .color-secondary,
.theme-unicorn.color-secondary {
  color: pink;
}

.theme-dragon .border-color-primary,
.theme-dragon.border-color-primary {
  border-color: firebrick;
}

.theme-dragon .background-color-primary,
.theme-dragon.background-color-primary {
  background-color: firebrick;
}

.theme-dragon .color-primary,
.theme-dragon.color-primary {
  color: firebrick;
}

.theme-dragon .border-color-secondary,
.theme-dragon.border-color-secondary {
  border-color: red;
}

.theme-dragon .background-color-secondary,
.theme-dragon.background-color-secondary {
  background-color: red;
}

.theme-dragon .color-secondary,
.theme-dragon.color-secondary {
  color: red;
}
```

它可能看起来像一个可怕的大量生成的 CSS，但这是你可以在整个项目中多次重用的东西，所以它实际上并没有那么糟糕。

### 赞成的意见

*   这种解决方案的优势在于它是一种基于 DOM 的方法，当必须用 JavaScript 动态操作主题时，这种方法变得非常有趣。事实上，这只是在元素中添加/删除主题类的问题；非常方便。
*   虽然`themify` mixin 的输出看起来很大，但它实际上是一个非常枯燥的方法，因为每个主题颜色、边框颜色、背景颜色和其他什么都是通过这些类应用的。

### 骗局

*   在某些情况下，基于 DOM 的方法可能不是正确的处理方式，例如当标记不是手工制作的时候。

## 最后的想法

我肯定我可能忘记了用 Sass 应用主题风格的其他方式，但是我觉得这 4 个不同的版本已经涵盖了这个主题的一个很好的领域。如果我有选择的话，我们要么选择单独的 mixins 方法，要么如果我们想非常模块化(这总是好的)的话，就一直选择 DOM-drive 类方法。

你呢，你是怎么做到的？

## 分享这篇文章