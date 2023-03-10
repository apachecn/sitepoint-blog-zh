# Sass 基础:@-规则和指令

> 原文：<https://www.sitepoint.com/sass-basics-rules-directives/>

Sass 中的@-规则和指令是您至少应该了解的主干特性，尤其是当它涉及到@import 规则时。这些@-规则中的一些是 CSS @-规则的扩展，而另一些是 Sass 特定的指令。无论哪种方式，如果您要使用 Sass，您最好了解这些规则的用途以及它们是如何工作的。

## @导入

Sass 扩展了 CSS @import 规则，因此它可以导入 SCSS/Sass 文件。通常我们使用这个规则将我们的 Sass 导入到一个主文件中。我们将这个主文件作为我们的主 CSS 文件包含在一个项目中，以使用所有的合并规则。导入文件可以访问文件中包含的任何混合或变量。

默认情况下，Sass 在当前目录中查找其他 Sass 文件，然后在 Rails 下的 Sass 文件目录中查找。还可以使用 load paths 选项指定其他目录。如果您正在使用 Compass，您可以通过更改 config.rb 中的`sass_dir`值来指定目录。

`@import`查找要导入的 Sass 文件，但在以下条件下将编译为 CSS @import 规则:

*   文件扩展名是。钢性铸铁
*   文件名以 htp://开头
*   文件名是一个 url()
*   @import 有媒体查询

如果文件扩展名为`.scss`或`.sass`，文件将被导入。如果没有扩展名，Sass 将尝试查找与名称和正确扩展名匹配的文件。例如:

```
@import "sample.scss";

@import "sample";
```

这两个@import 语句都有效，并将导入 sample.scss 文件。第二次导入也会导入 sample.sass(如果存在的话)。

我们还可以在一个@import 语句中导入多个文件，方法是将文件包含在逗号分隔的列表中。

```
@import "sample1", "sample2", "sample3";
```

导入文件时必须小心，因为它们必须以正确的顺序导入。例如，假设我们有一个名为`myVariables`的文件，其中包含了项目中的变量。如果我们有另一个名为`myStyles`的文件使用这些变量，那么必须首先导入`myVariables`文件。

```
@import "myStyles";
@import "myVariables";
```

如果我们按照这个顺序导入，我们将会得到一个错误，因为`myStyles`将会尝试使用尚未定义的变量。

```
@import "myVariables";
@import "myStyles";
```

这是正确的导入方式，因为`myStyles`文件中引用的变量已经导入。

##### 部分@导入

当你导入一个 SCSS/Sass 文件时，它会编译成一个同名的 CSS 文件。通常我们希望将单独的 SCSS/萨斯文件导入一个主 CSS 文件，而不是多个 CSS 文件。为了做到这一点，我们需要@import 文件作为部分。我们通过在名称前添加下划线，将 SASS 萨斯文件转换为部分文件。下划线告诉 Sass 不要将文件编译成单独的 CSS 文件，而是将它与包含@import 语句的当前文件合并。

当我们导入这些部分时，我们不必包含下划线。假设我们把`myVariables`改成了`_myVariables`。我们将以同样的方式导入文件。

```
@import "myVariables";
```

不同之处在于不会创建`myVariables.css`文件，该文件将与主 CSS 文件合并。请务必注意，不能在一个目录中包含同名的部分和非部分。我们不能让`_myVariables.scss`和`myVariables.scss`在同一个地方。

##### 嵌套@import

大多数情况下，我们将@import 放在文档的顶层，我们可以将它们包含在规则中。导入的规则将嵌套在它们被导入的位置。例如，假设我们有一个文件名`test`,其规则如下

```
.try {
 color: blue;
}
```

我们可以将这个导入嵌套在主文件中。

```
#why {
 @import "test";
}
```

这将编译成:

```
#why .try {
  color: blue;
}
```

像@mixin 这样的指令只允许在文档的基本级别使用，不能出现在嵌套导入的文件中。同样，您也不能将@import 嵌套在 mixins 或 control 指令中。

## @媒体

@media 的行为与 CSS @media 规则相同，只是它们可以嵌套在 CSS 规则中。当@media 指令嵌套在 CSS 规则中时，它们会冒泡到样式表的顶层。

```
.container {
 width: 60%;
 @media (min-width: 200px) and (max-width:600px) {
 width: 100%;
  }
}
```

将编译为:

```
.container {
  width: 60%;
}
@media (min-width: 200px) and (max-width: 600px) {
  .container {
    width: 100%;
  }
}
```

如您所见，我们为媒体查询获得了一个单独的规则，尽管它嵌套在`.container`类中。我们还可以将@media 查询嵌套在另一个查询中。这些查询将使用`and`进行组合。

```
@media screen {
  .main {
 @media (max-width: 600px) {
 width: 100%;
    }
 @media (min-width: 700px) {
 width: 70%;
    }
  }
}
```

这给了我们

```
@media screen and (max-width: 600px) {
  .main {
    width: 100%;
  }
}
@media screen and (min-width: 700px) {
  .main {
    width: 70%;
  }
}
```

如您所见，创建了两个媒体查询。最后，我们可以使用 Sass 表达式，比如变量、函数和操作符作为特性名和值。

```
$format: screen;
$mobile: 500px;
$tablet: 800px;
$desktop: 1200px;

@media #{$format} {
  .menu {
 @media (max-width: $mobile) {
 width: 100%;
    }
 @media (max-width: $tablet) {
 width: 60%;
    }
 @media (max-width: $desktop) {
 width: 30%;
    }
  }
}
```

正如你所看到的，我们正在使用插值和我们的`$format`变量，并嵌套我们的查询。我们使用不同的设备变量来构建其余的查询。这给了我们:

```
@media screen and (max-width: 500px) {
  .menu {
    width: 100%;
  }
}
@media screen and (max-width: 800px) {
  .menu {
    width: 60%;
  }
}
@media screen and (max-width: 1200px) {
  .menu {
    width: 30%;
  }
}
```

## @扩展

有时，在设计页面样式时，您可能会有一个元素，它应该具有另一个元素的所有样式以及自己的样式。假设我们有两类文本，一类是常规文本，一类是强调文本。

```
.main {
  color:  black;
  font-size: 12px;
}
.emphasis {
  font-weight: bold;
}
```

这已经足够好了，但是为了使用强调文本，我们必须这样做:

```
<div class="main emphasis">
```

记住，我们可以使用 Sass 来减少我们必须做的打字量。@extend 指令允许我们让一个元素继承另一个元素的样式。

```
.master {
 color:  black;
 font-size: 12px;
}
.emphasis {
 @extend .master;
 font-weight: bold;
}
```

我们已经使用@extend 重写了上面的样式，这给了我们:

```
.master, .emphasis {
  color: black;
  font-size: 12px;
}

.emphasis {
  font-weight: bold;
}
```

如你所见，`.emphasis`包含在`.master`中，并且具有仅适用于强调文本的样式。现在要使用这个类，我们只需要包含`.emphasis`类。

使用@extend 指令，我们可以扩展复杂的选择器，拥有多个@extends、chain @extends 等等。我可以写一整篇关于@extends 的文章，更多关于@extends 的信息请查看 [Sass 文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#directives)。

小心使用`@extend`,因为输出的 CSS 可能会变得臃肿，而且由于指令的工作方式，你可能会创建一些非常糟糕的 CSS。Hugo 已经写了一篇关于[为什么他避免使用@extend](https://www.sitepoint.com/avoid-sass-extend/) 的帖子，以及更多关于它的[更深入的帖子](https://www.sitepoint.com/sass-extend-nobody-told-you/)。

## @根

@at-root 指令在文档的根创建规则，而不是嵌套在它们的父元素中。

```
.top {
  .first {
 font-size: 12px;
  }
 @at-root {
    .second {
 font-size: 14px;
    }
    .third {
 font-size: 16px;
    }
  }
  .fourth {
 font-size: 18px;
  }
}
```

它编译成:

```
.top .first {
  font-size: 12px;
}
.second {
  font-size: 14px;
}
.third {
  font-size: 16px;
}
.top .fourth {
  font-size: 18px;
}
```

@at-root 指令适用于单行或一组选择器。如您所见，嵌套在@at-root 中的选择器位于文档的根目录。其他选择器嵌套在`.top`中。

@at-root 指令还允许您通过使用`(without:...)`或`(with:...)`移动到指令之外。比如说。

```
@media print {
  .copy {
 color: black;
 @at-root (without: media) {
 width: 100%;
    }
  }
}
```

这给了我们:

```
@media print {
  .copy {
    color: black;
  }
}
.copy {
  width: 100%;
}
```

@at-root 指令也有一个`without`，它允许我们在没有@media 查询的情况下创建一个单独的样式。

## @调试

@debug 指令将 Sass 表达式的值打印到标准错误输出流中。例如:

```
@debug 10px + 20px;
```

当我保存文件时，输出被写到我的 watch 命令的输出流中。

```
1 DEBUG: 30px
```

## @警告

@warn 指令将 Sass 表达式的值打印到标准错误输出流中。

```
$wrn: 20px;
@warn "#{$wrn}";
```

它将显示表达式的值以及警告的行号。

## @错误

@error 指令将 Sass 表达式的值作为一个错误给出，同样是标准错误流

```
$test: 1px;
@error "#{$test}";
```

这将显示标准错误流中的错误、行号和值。

## 结论

我们强调了一些你可能不知道的@-规则和指令。您可能不会发现它们有多大用处，但是了解您的工具的全部功能总是有好处的。

## 分享这篇文章