# 使用 Sass 管理颜色值

> 原文：<https://www.sitepoint.com/managing-color-values-with-sass/>

有很多关于用 Sass 管理颜色值的建议。你可以[通过程序生成调色板](https://www.sitepoint.com/using-sass-build-color-palettes/)，依靠 Sass 颜色函数、[命名变量](http://davidwalsh.name/sass-color-variables-dont-suck)，甚至[让 math 为你挑选所有的颜色](http://chrisasher.com/sassycolours/)。

但是，如果您只想选择一组基色，并对每种基色使用相同的暗/亮/透明变化，该怎么办呢？我们可以通过几个 Sass 映射和一个函数很容易地做到这一点。

## 彩色地图

首先，我们将开始在一个 [map](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#maps) 中存储我们所有的主色(也称为 Sass 数组)。

```
$colors: (
red: #ff4136,
blue: #0074d9,
yellow: #ffdc00,
orange: #ff851b
);
```

从 Sass 3.3 开始，SassScript 为我们提供了几个函数来[访问地图中的数据](http://sass-lang.com/documentation/Sass/Script/Functions.html#map-functions)。我们稍后将使用的函数是`map-has-keys()`和`map-get()`。

## 变化图

现在，我们将创建一个地图来存储我们需要从每种主要颜色中做出的变化。地图将保存这些信息。我们将使用在地图中存储地图的能力来很好地组织这一点。让我们看看如何组织这些信息。

首先，每种颜色变化的名称都是我们用来描述这种颜色变化的简写，稍后在我们的函数中调用它。该名称是具有两个值的映射的关键:Sass 脚本颜色函数的名称和该函数需要的参数。`function`的值必须与 [RGB 函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#rgb_functions)、 [HSL 函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#hsl_functions)或[不透明度函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#opacity_functions)列表中的函数相匹配。

`parameters`的值应该是函数除了原始颜色之外需要的任何参数。如果你看一下示例`$variations`地图，你会发现`lighten`需要两个参数:一个原始颜色(我们将从`$colors`获得)和一个我们将存储在`parameters`的变化量。另一方面，`grayscale`只需要一个起始颜色，所以我们根本不需要为那个嵌套的贴图包含`parameters`。由“shade”调用的`mix`函数最多接受三个参数:起始颜色、混合颜色以及第一种颜色占“混合”的百分比。我们只是在一个空格分隔的列表中包含了这两个附加参数。

```
$variations: (
light: (
function: lighten,
parameters: 15%
),
dark: (
function: darken,
parameters: 10%
),
fade: (
function: rgba,
parameters: .7
),
gray: (
function: grayscale
),
shade: (
function: mix,
parameters: white 80%
)
);
```

## 生成颜色的函数

现在，有趣的部分来了:一个函数获得一种颜色，进行变化，然后返回正确的值供您使用。首先，让我们看看整个函数。然后，我们将一步一步地介绍它。

```
@function color-variation($color, $variation: false) {

@if map-has-key($colors, $color) {
$color: map-get($colors, $color);
} @else {
@if type-of($color) != color {
@error "Invalid color name: `#{$color}`.";
}
}

@if $variation {
@if not map-has-key($variations, $variation) {
@error "Invalid $variation: `#{$variation}`.";
} @else {
$this-variation: map-get($variations, $variation);
$args: join(map-get($this-variation, function), $color);
@if map-get($this-variation, parameters) {
$args: join($args, map-get($this-variation, parameters));
}
@return call($args...);
}
}
@return $color;
}
```

首先，您应该注意到该函数有两个参数。一种颜色的名称(可能是来自`$colors`的一个键)是必需的(*是的，我说的是“可能”——稍后会有更多的介绍*)，一个变体的名称(必须与来自`$variations`的一个键匹配)是可选的。

### 验证颜色值

```
@if map-has-key($colors, $color) {
$color: map-get($colors, $color);
} @else {
@if type-of($color) != color {
@error "Invalid color name: `#{$color}`.";
}
}
```

第一个`@if`语句确保我们有一个有效的颜色值来处理。如果作为颜色传递的字符串在`$colors`中，将使用来自映射的适当值。但是如果你通过了不在`$colors`的东西呢？这个操作的`@else`部分处理这种情况。

首先，它测试`$color`参数是否是有效的 CSS 颜色。如果`$color`不是一个有效的颜色(我们已经知道它不在`$colors`中)，我们将抛出一个编译器`@error`，让你知道你需要使用一个有效的颜色。另一方面，如果您的`$color`本身是一个有效的颜色值(HTML 颜色名称、RGB(A)或 HSL)，我们将继续使用该值。这使得我们的`color-variation()`功能范围更广。例如，您可以将它用于一次性颜色值(当然，有节制地使用——如果您正在重复一个颜色值，它可能应该在地图中)。

*注意:如果你想强迫自己或其他开发者坚持使用`$colors`图中的值，只需删除两行:`@if type-of($color) != color {`和它的结束`}`低两行。将`@error`语句留在它所在的`@else`块中。*

现在我们有一个有效的颜色，让我们应用正确的变化。

### 验证变体名称

```
@if $variation {
@if not map-has-key($variations, $variation) {
@error "Invalid $variation: `#{$variation}`.";
} @else {
//...
}
}
```

第一个`@if`语句检查我们是否传递了一个变体名称。毕竟，这个函数需要返回一个不变的颜色值，对吗？没错。在您的 Sass 中，您将使用`color: color-variation(orange);`来获取 CSS 属性值中已定义的颜色之一。

接下来，我们使用[`map-has-key()`函数](https://www.sitepoint.com/using-sass-maps/)来确保我们传递的变体名称确实在`$variations`图中。如果没有，我们将显式地向编译器抛出一个`@error`。这将提供我们犯了一个错误的即时反馈，并迫使我们在继续前进之前修复它。

*注意:如果你想现在允许犯错误，以后再改正，用`@warn`代替`@error`。`@warn`会给你的编译器发送一个“更柔和”的错误信息，这个函数会简单地返回颜色值不变。*

### 改变颜色

```
$this-variation: map-get($variations, $variation);
$args: join(map-get($this-variation, function), $color);

@if map-get($this-variation, parameters) {
$args: join($args, map-get($this-variation, parameters));
}
```

这里，我们所做的是获取分配给当前变量的数据，并将其分配给一个新变量。记住，`$variations`映射包含其他几个映射，所以这一步使得处理当前变体的嵌套键/值对变得更加容易。

我们现在也要添加`$args`。我们最终需要将一个值列表(函数名、颜色、变量参数)传递给`call()`函数，`$args`将存储该列表。我们首先使用 [`join()`](http://sass-lang.com/documentation/Sass/Script/Functions.html#join-instance_method) 将当前变量的函数名(`map-get($this-variation, function)`)与上面验证过的`$color`值结合起来(我们可以使用`append()`来代替 join)。

接下来，我们的函数检查对于`$this-variation`是否有一个`parameters`键/值对。如果没有，则进入`call()`语句。但是如果有参数，我们通过将当前`parameters`中的值/列表加入到列表中来添加到列表中。现在我们已经得到了我们需要的列表:函数名、开始颜色和函数的附加参数。

```
@return call($args...);
```

这最后一行返回将我们的`$args`列表传递给`call()`函数的结果。`call()`动态生成并运行一个函数。它采用的第一个参数是函数名，任何其他参数都作为参数传递给该函数。

## 结论

要在您自己的项目中使用这个函数，创建您自己的颜色名称和值的映射，颜色函数和参数的映射，并使用`color-variation()`函数在您的 Sass partials 中获得您需要的颜色值。

额外收获:如果你不喜欢反复输入“color-variation()”，可以使用别名`cv()`作为速记功能:

```
@function cv($color, $variation:false) {
@return color-variation($color, $variation);
}
```

如果你想看这个的所有代码，那么看看这个 Sassmeister 要点的源代码。

## 分享这篇文章