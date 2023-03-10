# 使用 Sass 构建调色板

> 原文：<https://www.sitepoint.com/using-sass-build-color-palettes/>

前几天，UI 开发人员 Alexandre Broudin 问我如何使用 Sass 来构建各种各样的调色板，这些调色板与给定的调色板相似，只有基色会改变。我喜欢用程序化的方式玩颜色，所以我决定试一试。

假设你有一个由 7 种红色组成的调色板。也许你已经在 Photoshop 中或者用 Sass 函数手工构建了调色板。第四种(中间)颜色是*基色*，有三种较浅的色调和三种较深的色调。

![color palette](img/fe77247c580374dbfaf77fe917c64355.png)

现在，您需要一种从另一种基色快速生成类似调色板的方法。基本上，您希望有另一个颜色集合，其中基色和调色板中每种颜色之间的亮度和饱和度差异是相同的。因此，如果最亮的颜色和基本颜色之间的差异是，比方说，28%的亮度，您希望所有调色板中最亮的颜色比它自己的调色板中的基本颜色亮 28%。

如果我们已经手工制作了第一个调色板，但不知道从基色到调色板的每种颜色所需的颜色处理，我们怎么能让所有的调色板都一样呢？

*注意:这篇文章使用了我在 Sass Way 上发表的另一篇文章中的代码片段。*

## 构建我们的功能

首先，我们需要将默认调色板初始化为一个全局变量。

```
$base-palette: (
  'base': #ff6351,
  'colors': #cfdfe8 #bfb9c3 #cf9192 #ff6351 #bf413c #7f3128 #732c24
) !default;
```

我们的基本调色板是由两个键组成的地图:基本颜色和颜色列表。前三个是浅色调，从最浅的开始。然后你又有了基色，然后是暗色调，以最暗结束。

现在为了理解我们为什么要做我们将要做的事情，我需要解释一下整个想法。如果你已经以 Sass 的方式阅读了我的文章，它会更容易理解，但以防万一你没有机会阅读它，让我试着把这弄清楚。

我们将计算*色差*。色差是应用于颜色 A 以产生颜色 b 的操作图。在我们的例子中，我们需要一个色差列表，调色板中的每种颜色都有一个色差列表。每个颜色差异将是应用于颜色 N 的操作，以便有基本颜色(或相反，这没关系)。

这是色差函数:

```
@function color-diff($a, $b) {
  $sat: saturation($a) - saturation($b);
  $lig: lightness($a) - lightness($b);
  $fn-sat: if($sat > 0, 'desaturate', 'saturate');
  $fn-lig: if($lig > 0, 'darken', 'lighten');

  @return (
    adjust-hue: -(hue($a) - hue($b)),
    #{$fn-sat}: abs($sat),
    #{$fn-lig}: abs($lig)
  );
}
```

`color-diff`函数返回应用于第一种颜色(`$a`)以获得第二种颜色(`$b`)的操作图。太完美了。

接下来，我们需要一个函数，对基本调色板(`$base-palette`)的每种颜色运行`color-diff`，并返回这些差异的列表:

```
@function palette-diff($palette) {
  $base: map-get($palette, 'base');
  $colors: map-get($palette, 'colors');
  $diffs: ();

  @each $color in $colors {
    $diffs: append($diffs, color-diff($base, $color));
  }

  @return $diffs;
}
```

我们现在需要做的就是运行一次，然后将它存储在一个全局变量中。不需要为我们运行的每个调色板计算这些差异，只需要一次就足够了:

```
$base-palette: (
  'base': #FF6351,
  'colors': #CFDFE8 #BFB9C3 #CF9192 #FF6351 #BF413C #7F3128 #732C24
) !default;

$palette-diff: palette-diff($base-palette);
/**
 * Yields a list of 7 maps (diffs)

(
  (adjust-hue: 195.3931deg, desaturate: 64.78873%, lighten: 20.19608%),
  (adjust-hue: 269.7931deg, desaturate: 92.30769%, lighten: 8.62745%),
  (adjust-hue: 352.82536deg, desaturate: 60.75949%, lighten: 3.13725%),
  (adjust-hue: 0deg, saturate: 0%, lighten: 0%),
  (adjust-hue: 0.20532deg, desaturate: 47.80876%, darken: 16.66667%),
  (adjust-hue: 0deg, desaturate: 47.90419%, darken: 33.13725%),
  (adjust-hue: -0.13095deg, desaturate: 47.68212%, darken: 36.27451%)
)

 */
```

到目前为止，一切顺利！只剩下两个功能了。第一个来自旧文章，对一种颜色应用 diff(`color-diff`的返回),返回新的颜色。

```
@function apply-diff($color, $diff) {
  @each $function, $value in $diff {
    $color: call($function, $color, $value);
  }

  @return $color;
}
```

这里我们将 diff 中的每个函数应用于颜色，然后我们得到另一种颜色。现在我们只需要一个从基色创建调色板的函数。姑且称之为`create-palette`:

```
@function create-palette($base-color) {
  $palette: ();

  @each $diff in $palette-diff {
    $palette: append($palette, apply-diff($base-color, $diff));
  }

  @return $palette;
}
```

至此，我们基本完成了。我们用基色调用`create-palette`函数，它返回 7 种颜色的列表(`$base-palette` `colors`键的长度)，由用于基色调色板的相同操作制成。例如:

```
$green-palette: create-palette(lightgreen);
// Returns: #f4f1f3 #d5d5d5 #c2cec0 lightgreen #79b079 #4f864f #497c49
```

很整洁，是吧？

## 让事情变得更好

颜色列表并不是处理颜色的最佳方式。你打算用什么？`nth($green-palette, 3)`？不太实用。如果我们把这个列表变成一个带有明确关键字的地图，比如:`lightest`、`lighter`、`light`、`base`、`dark`、`darker`、`darkest`，会怎么样？然后你可以说`map-get($green-palette, light)`。

对于这一点，没有巫术。我们构建了一个函数，将颜色列表手动转换为地图:

```
@function palette($base-color) {
  $colors: create-palette($base-color);
  $keys: 'lightest' 'lighter' 'light' 'base' 'dark' 'darker' 'darkest';
  $palette: ();

  @for $i from 1 through min(length($colors), length($keys)) {
    $palette: map-merge($palette, (nth($keys, $i): nth($colors, $i)));
  }

  @return $palette;
}
```

我们之前的例子:

```
$green-palette: palette(lightgreen);
/**
 * Yields

(
  lightest: #f4f1f3, 
  lighter: #d5d5d5, 
  light: #c2cec0, 
  base: lightgreen, 
  dark: #79b079, 
  darker: #4f864f, 
  darkest: #497c49
)

 */
```

很酷，对吧？

## 把事情推进一步

这里的东西越来越有用，但是输入`map-get(..., light)`很快就会变得很烦人。为了减轻这个过程(看到我在那里做了什么吗？)，我们可以建立一系列的助手:

```
@function lightest($palette) {
  @if not map-has-key($palette, 'lightest') {
    @warn "`#{inspect($palette)}` doesn't seem to have a key named `lightest`.";
  }

  @return map-get($palette, 'lightest');
}

@function lighter($palette) {
  @if not map-has-key($palette, 'lighter') {
    @warn "`#{inspect($palette)}` doesn't seem to have a key named `lighter`.";
  }

  @return map-get($palette, 'lighter');
}

@function light($palette) {
  @if not map-has-key($palette, 'light') {
    @warn "`#{inspect($palette)}` doesn't seem to have a key named `light`.";
  }

  @return map-get($palette, 'light');
}
```

以此类推，你就明白了。那你只要做:

```
.el {
  color: light($green-palette);
}
```

…这可以被认为是*从`$green-palette`* 给我“光”的颜色。

## 最后的想法

正如你所看到的，我们可以做很多事情来简化色彩处理。在列表、地图和颜色函数之间，构建调色板从未如此简单。

希望你喜欢这个主意。下面有一个演示。您可以通过编辑 CSS 中靠近底部的“调色板”部分来测试它。改变六种颜色中的任何一种都会改变整个调色板。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )用 Sass 构建调色板的笔[。](http://codepen.io/SitePoint/pen/lqLvD/)