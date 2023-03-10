# 验证 Sass 混合和函数中的输入

> 原文：<https://www.sitepoint.com/validating-input-in-sass-mixins-and-functions/>

当你写 Sass 而其他人使用它时，他们很可能在你的代码中犯错误。实际上，说实话，当我编写 Sass 并在几天后(甚至几小时后)使用它时，我的代码会出错。你可能也是。幸运的是，Sass 有许多函数可以帮助我们验证开发人员输入到我们编写的 Sass 中的输入。

这些技术对于共享 Sass mixins 或维护一个初学者工具包或一组 mixin 和函数的团队特别有用。开发人员在使用共享的 Sass 库时有两种选择:要么他们可以通过电子邮件、聊天、ping 或其他方式相互打断以获得自定义 mixin 的帮助，要么使用包括代码验证在内的详细文档来帮助彼此轻松地解决代码问题。(不管怎样，这不仅仅是一件无礼的事情:任何共享代码都需要通过中断或文档进行交流。)现在让我们了解几个最有用的 Sass 验证方法。

## 验证单个值

混合和函数需要参数。如果您正在将代码传递给另一个开发人员，或者发布一个开源库，您会希望确保参数符合您的意图。这些函数对于验证参数中的变量非常有用。

### 确保变量存在:`variable-exists()`

如果您的函数或 mixin 依赖于开发人员定义的变量，请使用名副其实的 [variable-exists()函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#variable_exists-instance_method)确保该变量存在。该函数根据变量是否被创建和定义，返回`true`或`false`。

```
@mixin create-font-size() {
 @if global-variable-exists(base-font) {
 font-size: $base-font;
  } @else {
 @error "Please define the variable `$base-font`.";
  }
 @if global-variable-exists(line-height) {
 line-height: $line-height;
  } @else {
 @error "Please define the variable `$line-height`.";
  }
}

// developer's code
$base-font: 18px;
$line-height: 1.5;
.element {
 @include create-font-size;
}
```

然而，比依赖开发人员正确设置全局变量更好的选择是在库中包含这些默认变量:

```
// Your plugin:
$base-font: 18px !default;
$line-height: 1.5 !default;

@mixin create-font-size() {
  // etc...
}

// developer's code:
$base-font: 16px;
p {
 @include create-font-size();
}
```

### 检查值的数据类型:`type-of()`

如果你需要知道一个变量代表什么类型的值，使用 [`type-of()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#type_of-instance_method) 。该函数将返回以下字符串之一:

*   线
*   颜色
*   数字
*   弯曲件
*   空
*   目录
*   地图

这对于验证某些类型的输入很有用。您可以确保开发人员只将数值传递给创建大小的 mixin:

```
@mixin size($height, $width: $height) {
 @if type-of($height) == number {
 height: $height;
  } @else {
 @warn "Make sure that `$height` is a number.";
  }
 @if type-of($width) == number {
 width: $width;
  } @else {
 @warn "Make sure that `$width` is a number.";
  }
}
```

您也可以使用`type-of()`来确保颜色混合只处理颜色:

```
@function color-fade($color) {
 @if type-of($color) == 'color' {
 @return rgba($color, .8);
  } @else {
 @warn "Make sure you pass a valid color to the color-fade() function.";
  }
}
```

如果您需要开发人员为主题创建配置设置的映射，您可以确保他们有一个有效的映射:

```
@mixin generate-theme($settings) {
 @if type-of($settings) == 'map' {
    // output here
  } @else {
 @warn "Make sure `$settings` is a Sass map.";
  }
}
```

### 确定数字的单位:`unit()`

有时函数或 mixin 中的数学需要在它的参数中有一个特定的单位。你可以使用 [`unit()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#unit-instance_method) 来确认一个值有正确的单位。例如，您可以使用 mixin 来创建像素和 rem 大小。(*注意，使用任务运行器包可能会更好，但是如果您需要将它保存在 Sass 中，请继续阅读。*

```
$rem-size: 16px !default;

@mixin px-rem($property, $value) {
 @if unit($value) == 'px' {
 #{$property}: $value;
 #{$property}: $value / $rem-size * 1rem;
  } @elseif unit($value) == 'rem' {
 #{$property}: $value * $rem-size / 1rem;
 #{$property}: $value;
  } @else {
 @warn "Make sure `$value` is in px or rem.";
  }
}
```

## 验证列表和映射

我们已经看到了如何使用`type-of()`来确保一个变量包含一个列表或一个映射。我们还可以测试两件重要的事情:一个值是否在列表中，一个键是否在映射中。

### 在列表中查找值:`index()`

index()函数会告诉你是否在列表中找到了一个值。从技术上讲，它将返回值在列表中的位置(一个数字)或`null`。这不是一个真正的布尔函数，但是对于我们的目的来说，truthy 和 falsy 已经足够好了。

`index()`函数有两个参数:列表和要在列表中查找的值。此功能对于测试测量混合中的某些值非常有用。如果我们有一个 mixin 使用 CSS 顶部、右侧、底部或左侧速记输出填充或边距计算，我们会希望确保我们不会试图计算像`initial`、`inherit`或`auto`这样的值。

```
$rem-size: 16px !default;

@mixin margin-rem($values...) {
 $output: ();
 @each $value in $values {
 @if index(auto inherit initial 0, $value) {
 $output: append($output, $value);
    } @else {
 $output: append($output, $value / $rem-size * 1rem);
    }
  }
 margin: #{$output};
}
```

### 确保地图有一个键:`map-has-key()`

如果您正在检查一个特定键的映射，您可以使用 [map-has-key()函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_has_key-instance_method)来确保该键存在于您正在检查的映射中。如果您使用`$breakpoints`地图和媒体查询 mixin，这将非常有用:

```
$breakpoints: (
 small: 480px,
 medium: 720px,
 large: 1024px
);

@mixin bp($label) {
 @if map-has-key($breakpoints, $label) {
 @media screen and (min-width: map-get($breakpoints, $label)) {
 @content;
    }
  } @else {
 @warn "`#{$label}` is not a valid key in `$breakpoints`.";
  }
}
```

## 验证混合和函数

偶尔你会编写一个依赖于现有 mixin 或函数的 mixin 或函数，或者可能依赖于另一个 Sass 库。让我们更新上一个例子中的`bp()` mixin，依赖于[断点 Sass](http://breakpoint-sass.com/) 库。我们可以这样展开它:

```
$breakpoints: (
 small: 480px,
 medium: 720px,
 large: 1024px
);

@mixin bp($label) {
 @if map-has-key($breakpoints, $label) {
 $width: map-get($breakpoints, $label);
 @if mixin-exists(breakpoint) {
 @include breakpoint($width) {
 @content;
      }
    } @else {
 @media screen and (min-width: $width) {
 @content;
      }
    }
  } @else {
 @warn "`#{$label}` is not a valid key in `$breakpoints`.";
  }
}
```

现在我们的`bp()` mixin(它更短并且使用贴图值)将使用`breakpoint()` mixin，如果它存在的话。如果没有，它将依靠我们自己的媒体查询 mixin 代码。

有一个匹配函数叫做 [`function-exists()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#function_exists-instance_method) 。你可以用它来测试一个特定的函数是否存在。如果你有依赖于非标准函数的数学，你可以确保有包含该函数的库。Compass 为指数数学增加了一个`pow()`函数。如果您正在创建需要该功能的字体大小标尺，请测试它:

```
$base-font-size: 16px;
$scale-interval: 1.2;

@function scale-font($steps, $size: $base-font-size, $scale: $scale-interval) {
 @if function-exists(pow) {
 @return pow($scale, $steps) * $size;
  } @else {
 @error "A `pow()` function is required for scale math: please include Compass or another library with a `pow()` function.";
  }
}
```

## 上报问题:`@warn`和`@error`

正如您可能在上面的代码示例中注意到的，当我们的验证捕捉到一些不正确的输入时，我会小心翼翼地向开发人员提供良好的反馈。很多时候我用的是 [@warn](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_6) 。该指令向开发人员的控制台发送一条消息，但允许编译器完成运行。

偶尔，当我需要完全停止编译器时(如果没有特定的输入或函数，大量的输出会被破坏)，我使用 [@error](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_7) 向控制台发送一条消息并停止编译器。

要了解更多关于`@warn`和`@error`之间区别的信息，你可能想看看[我以前关于这个主题的文章](https://www.sitepoint.com/using-sass-error-warn-and-debug-directives/)或者[SitePoint 的 Sass 参考中的相应章节](https://www.sitepoint.com/introducing-the-sitepoint-sass-reference/)。

## 结论

没有人是完美的——使用我们代码的人没有，甚至在我们写完代码几个小时后的我们也没有！这就是为什么通过验证我们的 mixins 和函数中的输入来帮助我们自己和他人是重要的。这些技术不仅帮助您更有效地使用您自己的代码，还使团队共享和维护 Sass 库变得简单得多。

你如何防止你的 Sass 中的错误？请在评论中告诉我们！

## 分享这篇文章