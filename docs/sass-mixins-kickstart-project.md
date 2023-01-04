# sass mixins 来 kickstarter 你的项目

> 原文：<https://www.sitepoint.com/sass-mixins-kickstart-project/>

Mixins 很棒，不是吗？它们就像输出 CSS 的函数。根据[Sass 文件](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixins):

> Mixins 允许您定义可以在整个样式表中重用的样式，而不需要求助于像`.float-left`这样的非语义类。Mixins 还可以包含完整的 CSS 规则，以及 Sass 文档中其他地方允许的任何内容。他们甚至可以接受参数，这样你就可以用很少的混音产生各种各样的风格。

Mixins 真的很方便，尤其是在处理大型项目时。在上一个项目中，你不得不定义多少次宽度和高度值？你谷歌了多少次如何用 CSS 做一个三角形？或者你有没有希望在使用 CSS 定位时有一个`top`、`right`、`bottom`、`left`的简写？

你可以用 mixins 解决所有这些问题。更好的是:你不必写它们，因为我已经为你写好了。我们开始吧，伙计们，几个 Sass mixins 来启动你们的项目！

## 上浆混合料

我打赌你已经知道这个了。我在我所有的项目中使用它作为 mixin 101:一个定义宽度和高度的 mixin。第一个参数是宽度，第二个参数是高度。如果省略 height，则返回宽度值。

```
@mixin size($width, $height: $width) {
      width: $width;
      height: $height;
}
```

很简单。

### 用法示例

萨斯:

```
.element {
  @include size(100%);
}

.other-element {
  @include size(100%, 1px);
}
```

CSS 输出:

```
.element {
  width: 100%;
  height: 100%;
}

.other-element {
  width: 100%;
  height: 1px;
}
```

## 定位混合

如果有一种速记法我真的认为 CSS 错过了，那就是偏移量的捷径:`top`、`right`、`bottom`和`left`。我们什么都有简称:`padding`、`margin`、`background`、[甚至`text-decoration`、](https://www.sitepoint.com/12-little-known-css-facts/#text-decoration-is-now-a-shorthand-property)！但我们仍然没有一个抵消…所以我建立了自己的。

它的灵感来自手写笔的语法:

```
absolute: top 0 left 1em
```

不幸的是，Sass 没有提供[透明混合](http://learnboost.github.io/stylus/docs/mixins.html)特性，因此，我们最多只能得出这样的结果:

```
@include absolute(top 0 left 1em);
```

…你不得不承认，这并没有那么糟糕。显然，我们也有`relative()`和`fixed()`，它们以同样的方式工作。

我给[写了一整篇博文](http://hugogiraudel.com/2013/08/05/offsets-sass-mixin/)来介绍这个 mixin 的制作，所以我不会在这里讲太多细节，但主要思想是为 mixin 提供一个值列表。每当找到一个关键字(`top`、`right`、`bottom`、`left`)，只要是一个有效值，就直接应用该关键字后的值。

```
@mixin position($position, $args) {
  @each $o in top right bottom left {
        $i: index($args, $o);

    @if $i and $i + 1< = length($args) and type-of(nth($args, $i + 1)) == number  {
          #{$o}: nth($args, $i + 1);
    }
  }

  position: $position;
}

@mixin absolute($args) {
        @include position("absolute", $args);
}

@mixin fixed($args) {
        @include position("fixed", $args);
}

@mixin relative($args) {
        @include position("relative", $args);
}
```

### 用法示例

萨斯:

```
.element {
  @include absolute(top 0 left 1em);
}

.other-element {
  @include fixed(top 1em left "WAT? A STRING?!" right 10% bottom)
}
```

CSS 输出:

```
.element {
  position: absolute;
  top: 0;
  left: 1em;
}

.other-element {
  position: fixed;
  top: 1em;
  right: 10%;
}
```

## 供应商前缀 Mixin

你知道你必须给一些属性加上前缀`-webkit-`或`-moz-`(以及其他前缀)才能让它们跨浏览器工作，因为它们是非标准属性？那你也知道这有多烦人。这正是[Compass](http://compass-style.org/reference/compass/helpers/cross-browser/)和[Bourbon](http://bourbon.io/docs/#prefixer-settings)试图通过为您提供一个处理前缀的 mixins 集合来解决的问题(例如，`@include box-sizing()`)。

我已经使用 Compass 很长时间了，但是最近我决定放弃它。主要原因是我没有在项目中充分使用它；最终，这意味着更快的编译时间。也就是说，我发现自己需要厂商前缀混合。所以我构建了一个(并在迁移到 Sass 3.3 时更新了它)。

### Sass 3.2 版本

想法很简单。第一个参数是属性。第二个参数是该属性的值。第三个可选参数是要使用的前缀列表。默认值转储所有前缀。

```
@mixin prefix($property, $value, $vendors: webkit moz ms o) {
      @if $vendors {
        @each $vendor in $vendors {
          #{"-" + $vendor + "-" + $property}: #{$value};
        }
      }
      #{$property}: #{$value};
}
```

### 用法示例

萨斯:

```
.element {
  @include prefix(transform, rotate(42deg), webkit ms);
}
```

输出:

```
.element {
  -webkit-transform: rotate(42deg);
  -ms-transform: rotate(42deg);
  transform: rotate(42deg);
}
```

### Sass 3.3 版本

我的 Sass 3.3 版本甚至更好，因为它允许一次给多个属性加前缀，并且有更好的语法。基本上，不是有两个不同的属性和值的参数，而是只有一个:声明的映射。键是属性，值是值。

```
@mixin prefix($map, $vendors: webkit moz ms o) {
  @each $prop, $value in $map {
        @if $vendors {
          @each $vendor in $vendors {
            #{"-" + $vendor + "-" + $prop}: #{$value};
          }
        }
        // Dump regular property anyway
        #{$prop}: #{$value};
  }
}
```

### 用法示例

萨斯:

```
.element {
  @include prefix((transform: translate(-50%, -50%)), webkit ms);
}

.other-element {
  @include prefix((
    column-count: 3,
    column-gap: 1em,
    column-rule: 1px solid silver,
    column-width: 20em
  )), webkit moz);
}
```

CSS 输出:

```
.element {
  -webkit-transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}

.other-element {
  -webkit-column-count: 3;
  -moz-column-count: 3;
  column-count: 3;
  -webkit-column-gap: 1em;
  -moz-column-gap: 1em;
  column-gap: 1em;
  -webkit-column-rule: 1px solid silver;
  -moz-column-rule: 1px solid silver;
  column-rule: 1px solid silver;
  -webkit-column-width: 20em;
  -moz-column-width: 20em;
  column-width: 20em;
}
```

### 把事情推进一步

这个 mixin 的酷之处在于，它可以很容易地用于其他 mixin，以避免每次都必须键入`prefix()`和供应商前缀。像这样想一想:

```
@mixin transform($value) {
      @include prefix(transform, $value, webkit ms);
}

@mixin column-count($value) {
      @include prefix(column-count, $value, webkit moz);
}
```

下面是用法示例:

```
.element {
  @include transform(rotate(42deg));
}
```

CSS 输出是:

```
.element {
  -webkit-transform: rotate(42deg);
  -ms-transform: rotate(42deg);
  transform: rotate(42deg);
}
```

## 反向混合

如果你是 Compass 的用户，你可能对`opposite-direction`函数很熟悉(是的，这是一个函数而不是 mixin，但无论如何)。Compass 中的这个是用 Ruby 构建的，但是 Sass 是很容易的。我做的这个依赖于 Sass 3.3，但可以很容易地调整到 Sass 3.2。

我喜欢我的函数，因为它允许你传递一个方向列表，这样你就可以从`opposite-direction(top right)`得到`bottom left`。例如，在处理`background-position`问题时，它会很有用。

```
@function opposite-direction($directions) {
      $opposite-directions: ();
      $direction-map: (
    'top': 'bottom',
    'right': 'left',
    'bottom': 'top',
    'left': 'right',
    'ltr': 'rtl',
    'rtl': 'ltr'
  );

  @each $direction in $directions {
    $opposite-direction: map-get($direction-map, $direction);
        @if $opposite-direction != null { 
      $opposite-directions: append($opposite-directions, #{$opposite-direction});
    }
    @else {
      @warn "No opposite direction can be found for `#{$direction}`.";
    }
  }

  @return $opposite-directions;
}
```

### 用法示例

```
$direction: opposite-direction(top); 
// bottom

$other-direction: opposite-direction(bottom left);
// top right
```

## 断点处理程序混合

如果你曾经不得不做一些响应式设计，你知道处理几种不同的媒体查询会很困难。将各种断点存储在变量中通常是一个好主意，这样就可以轻松地检索它们，而不必每次都重新输入。

如果你想更进一步，你可能想命名你的断点——如果你想听我的意见，这是一个非常好的主意。这基本上意味着一个媒体查询被映射到一个名称，所以你只需要给你的断点处理器 mixin 一个名称，使它转储一个媒体查询。包括克里斯·科伊尔在内的许多聪明的开发者已经让这个想法变得非常流行。

现在，如果您想更进一步，您可以将所有这些断点存储在一个全局映射中，然后让 mixin 根据您传递给它的名称检索断点。请考虑以下代码:

```
$breakpoints: (
  'tiny':   ( max-width:  767px ),
  'small':  ( min-width:  768px ),
  'medium': ( min-width:  992px ),
  'large':  ( min-width: 1200px ),
  'custom': ( min-height:  40em )
);

@mixin breakpoint($name) {
      @if map-has-key($breakpoints, $name) {
        @media #{inspect(map-get($breakpoints, $name))} {
      @content;
    }
  }
  @else {
    @warn "Couldn't find a breakpoint named `#{$name}`.";
  }
}
```

如果传递给`breakpoint` mixin 的字符串与`$breakpoints` map 中的一个键匹配，mixin 就会打开一个`@media`指令，使用`inspect`函数(也来自 Sass 3.3)来转储一个 map。当执行`inspect((key: value))`时，`(key: value)`会在样式表中被打印出来。

如果字符串与现有的断点不匹配，那么用户会通过`@warn`指令得到警告，并在控制台中打印一条错误消息。

### 用法示例

萨斯:

```
.element {
  color: red;

  @include breakpoint(medium) {
    color: blue;
  }
}
```

CSS 输出:

```
.element {
  color: red;
}

@media (min-width: 992px) {
  .element {
    color: blue;
  }
}
```

## 最后的想法

我觉得这是一个好的开始！在为下一个项目编写 CSS 时，这些工具应该有助于摆脱一些耗时的任务。请务必使用它们，调整它们并给出您的反馈！

## 分享这篇文章