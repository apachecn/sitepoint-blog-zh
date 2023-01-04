# Sass 基础知识:Sass Mixin 指令

> 原文：<https://www.sitepoint.com/sass-basics-the-mixin-directive/>

![Sass Mixins](img/695341f411bceff7c679a30ac9898a7d.png)

为了深入而简明地探究萨斯，SitePoint 向其所有高级会员提供萨斯超级明星雨果·吉劳德的《跳跃开始萨斯》。或者，你也可以花 19 美元买到一本。

*这篇文章于 2015 年首次出现在 SitePoint 上，经过一些小的更正和修改后，于 2017 年 6 月 14 日重新发布。*

对我来说，发现萨斯是一个启示。很长一段时间以来，我厌倦了编写普通的 CSS。对于一个小网站来说，这很好，但是对于大网站来说，CSS 很快就失控了。调试是一场噩梦，我不禁觉得我可以用我的 CSS 做更多的事情。

我找到了萨斯，一切都变了。使用 Sass，我可以将我的 CSS 分解成模块，使故障诊断变得轻而易举。我也可以在创建 CSS 时使用编程概念，比如函数和变量。最重要的是，萨斯向我介绍了 Mixins。

## 什么是萨斯 Mixin？

mixin 允许你创建可重用的 CSS 块。能够做到这一点将有助于您避免编写重复的代码。例如:

```
a:link { color: white; }
a:visited { color: blue; }
a:hover { color: green; }
a:active { color: red; }
```

一遍又一遍地写这些代码会很快变得过时，尤其是如果你有一个有很多链接的大型网站。您可以使用 Sass mixin 为链接创建样式，如下所示:

```
@mixin linx ($link, $visit, $hover, $active) {
  a {
 color: $link;
 &:visited {
 color: $visit;
    }
 &:hover {
 color: $hover; 
    }
 &:active {
 color: $active;
    }
  }
}
```

## 如何包含一个 Sass Mixin

要使用 mixin，你必须将它包含在你的 Sass 文件中。要从上面调用 linx mixin，您需要添加以下代码行:

```
@include linx(white, blue, green, red);
```

`@include`指令允许您在 Sass 文件中使用 mixins。

## 如何创建 Sass Mixin

要创建一个 mixin，你可以使用`@mixin`指令。例如:

```
@mixin sample {
 font-size: 12px;
}
```

您通过使用`@mixin`后跟 mixin 的名称来定义这个指令。您还可以选择在 mixin 中包含参数，就像您在上面的 linx mixin 中所做的那样。

您还可以在 mixin 中使用变量，这些变量在 Sass 文件中的其他地方定义。假设您想在 mixin 中使用`$font-base`作为变量。只要定义了变量，就可以在 mixin 中使用它:

```
$font-base: 12px;

@mixin sample {
 font-size: $font-base;
}

p {
 @include sample;
}
```

生成的 CSS 是:

```
p {
  font-size: 12px;
}
```

## Sass 混合中的参数

mixin 可以将 Sass 数据值作为参数。这些值是在定义 mixin 时指定的，并在您`@include`mixin 时给定一个特定的值。然后参数作为变量传递给 mixin。参数包含在一个逗号分隔的列表中，该列表放在 mixin 名称后面的括号中:

```
@mixin headline ($color, $size) {
 color: $color;
 font-size: $size;
}

h1 {
 @include headline(green, 12px);
}
```

这将编译成:

```
h1 {
  color: green;   
  font-size: 12px;
}
```

当使用基本参数时，必须按照 mixin 中指定的顺序调用它们。在最后一个例子中，如果在包含 mixin 时改变了参数的顺序，编译后的 CSS 将无效:

```
h1 {
 @include headline(12px, green);
}

h1 {
 color: 12px;
 font-size: green;
}
```

如您所见，mixin 只是按照给定的顺序传递参数，并没有产生预期的 CSS 代码。

还可以将 Sass 变量作为参数传递。例如，假设您想要在上面的示例中设置一个`$base-color`变量:

```
$base-color: pink;

@mixin headline($color, $size) {
 color: $color;
 font-size: $size;
}

h1 { @include headline($base-color, 12px);}
```

这将编译成:

```
h1 {
  color: pink;
  font-size: 12px;
}
```

## Sass 混合中的默认值

当创建 mixin 时，你可以指定默认值作为参数。当您包含默认值时，您可以在调用 mixin 时省略传递该值，因为 Sass 将使用默认值。例如，如果您用默认值更新上面的标题 mixin，如下面的代码所示:

```
@mixin headline($size, $color: red) {
 color: $color;
 font-size: $size;
}

h1 {
 @include headline(12px);
}

h1 {
 @include headline(12px, blue);
}
```

代码编译如下:

```
h1 {
  color: red;
  font-size: 12px;
}
h1 {
  color: blue;
  font-size: 12px;
}
```

在第一个 h1 中，你只指定了一个像素大小，所以 mixin 使用了默认值红色。在第二个例子中，您提供了一个显式的颜色值 blue，从而替换了您的`@include`指令中的默认值 red。请注意，您必须更改参数的顺序，因为 Sass 希望首先指定所需的参数。因为`$color`参数有默认值，所以在@include 中指定颜色是可选的。您也可以指定一个变量作为默认值:

```
$base-color: orange;

@mixin headline($size, $color: $base-color) {
 color: $color;
 font-size: $size;
}
```

## Sass 混合中的关键字参数

您还可以选择用关键字参数包含 mixin。即使使用关键字参数会使您的代码不那么简洁，但它将提高可读性，如果其他人将维护您编写的代码，这一点很重要。您可以以任何顺序包含关键字参数，当然也可以省略默认值:

```
@mixin headline($size, $color: red) {
 color: $color;
 font-size: $size;
}

h1 {
 @include headline($color: blue, $size: 12px);
}
```

即使参数顺序错误，编译也不会出错。

```
h1 {
  color: blue;
  font-size: 12px;
}
```

## Sass 混合中的变量参数

有时候你可能需要你的 mixin 接受一些参数。例如，`padding`属性可以有一到四个参数。在这种情况下，您可以创建一个使用可变参数的 mixin。可变参数允许您将参数打包成一个列表。变量参数看起来像 mixin 的常规参数，只是它们在末尾添加了(…):

```
@mixin pad ($pads...) {
 padding: $pads;
}

.one {
 @include pad(20px);
}
.two {
 @include pad(10px 20px);
}
.three {
 @include pad(10px 20px 40px);
}
.four {
 @include pad(10px 20px 30px 20px);
}
```

上面的代码编译成:

```
.one {
  padding: 20px;
}
.two {
  padding: 10px 20px;
}
.three {
  padding: 10px 20px 40px;
}
.four {
  padding: 10px 20px 30px 20px;
}
```

您还可以在变量参数旁边包含常规参数。假设您想在 pad mixin 中设置一种文本颜色:

```
@mixin pad ($color,$pads...) {
 color: $color;
 padding: $pads;
}
.four { @include pad(orange, 10px 20px 30px 20px); }
```

这产生了

```
.four {
  color: orange;
  padding: 10px 20px 30px 20px;
}
```

常规参数必须放在变量参数之前。如您所见，剩余的参数被打包并用于填充值。当包含您的 mixin 时，您也可以使用变量参数。对于您的参数，您可以从值列表或地图中进行绘制:

```
$box-style1: 5px, solid, red;
$box-style2: (bStyle: dotted, bColor: blue, bWidth: medium);

@mixin boxy($bWidth, $bStyle, $bColor) {
 border-width: $bWidth;
 border-style: $bStyle;
 border-color: $bColor;
}

.first {
 @include boxy($box-style1...);
}

.second {
 @include boxy($box-style2...);
}
```

正如你所看到的，你已经为你的 mixin 设置了一个列表和一个地图。使用列表时，参数的顺序必须正确。使用映射时，顺序并不重要，因为映射值将被视为关键字参数。正如您所看到的，映射值的顺序是错误的，但是您最终仍然得到了正确的 CSS 代码:

```
.first {
  border-width: 5px;
  border-style: solid;
  border-color: red;
}

.second {
  border-width: medium;
  border-style: dotted;
  border-color: blue;
}
```

## @内容

通过`@content`指令，你还可以传递 mixin 中没有定义的样式块。这些额外的样式将出现在您放置`@content`的 mixin 中:

```
@mixin cont {
 background-color: black;
 color: white;
 @content;
}
```

现在，当您调用这个 mixin 时，您将能够添加您想要使用的任何附加属性:

```
div {
 @include cont {
 font-size: 12px;
 font-style: italic;
  }
}

div {
 background-color: black;
 color: white;
 font-size: 12px;
 font-style: italic;
}
```

如您所见，上面添加的`font-size`和`font-style`已经被合并到与 div 元素相关的已编译 CSS 代码中。`@content`指令允许你设置基本样式并根据需要进行定制。

## 结论

如您所见，mixins 在您的 Sass 中非常有用。使用 mixins 可以完成很多事情，从而加快工作流程。关于 Sass mixins 的更多信息，请查看 [*Sass: Mixin 还是 Placeholder？雨果·吉劳德尔的*](https://www.sitepoint.com/sass-mixin-placeholder/) 和[萨斯博士的](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)。

你最喜欢的萨斯混音是什么？请在评论中分享。

## 分享这篇文章