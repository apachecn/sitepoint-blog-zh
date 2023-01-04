# 用 Sass 创建彩虹边框

> 原文：<https://www.sitepoint.com/rainbow-border-with-sass/>

许多现代设计都很好地利用了多色边框效果(我们也可以称之为“彩虹”边框或“条纹”边框)。例如，看看 [Treehouse 的博客](http://blog.teamtreehouse.com/)——它的导航下有一个多色的边框，看起来真的很不错。

![Treehouse's colored border](img/72ce268cdaccd4838c3203dc514691e7.png)

但是，在我看来，Treehouse 是编码错了。因为这与美学密切相关，所以它应该由 CSS 处理——而且只能由 CSS 处理——而不需要在 HTML 中做任何额外的工作。Treehouse 使用了 50 个`span`元素，每个元素都有特定的背景颜色和大小，来创建它的导航边框。

什么事？

将如此大量的元素转储到 DOM 中，仅仅是为了创建一个条纹边框，这种做法有些过头了。为什么不用 CSS 来做这个呢？我是说*只有* CSS。渐变对于这种类型的任务来说太棒了！是的，你可以用 CSS 渐变以这种方式创建多种颜色，而且很容易做到。

唯一的警告是，梯度是一种痛苦的写作。他们有一个冗长的语法，很可能导致打字错误和错误。此外，为了获得多种颜色而不得不重复颜色停止是令人讨厌的。

## 顶嘴救援！

这是我去年在 CSS-Tricks 上写的东西——但是现在回头看我的代码，我认为重新审视整个事情来看看我们是否可以做得更好是个好主意，特别是因为我们现在可以访问 [Sass 3.3 的特性](http://blog.sass-lang.com/posts/184094-sass-33-is-released)。

在进入代码之前，让我们看看我们想要创建什么。我可以看到产生这种效果的三种不同方式:

1.  基于颜色列表的等宽颜色部分。
2.  基于二维颜色列表+色标的自定义大小的颜色部分。
3.  基于带有随机生成的色标的颜色列表的随机大小的颜色部分。

我想要的是一个独特的功能，根据我们遇到的情况，遵从“私人”功能。这使得 API 对于用户来说很简单，对于开发人员(也就是您)来说代码组织得很好。

## 核心功能

```
@function stripes($colors, $direction: 90deg, $random: false) {
  // If lonely color
  @if length($colors) == 1 { @return $colors; }

  // Else
  $type: 'equal';

  @if $random {
    $type: 'random';
  }

  @else if length(nth($colors, 1)) > 1 {
    $type: 'custom';
  }

  @return linear-gradient($direction, call('_stripes-#{$type}-stops', $colors));
}
```

事情是这样的:首先我们检查`$colors`变量是否是一个单独的项，在这种情况下我们返回它。否则，我们定义我们面临的情况:`random`、`custom`或`equal`。如果你和我一样喜欢三元运算符，你可以这样做来得到相同的结果:

```
$type: if($random, 'random', if(length(nth($colors, 1)) > 1, 'custom', 'equal'));
```

最后，我们很好地利用了 Sass 3.3 中的`call`函数。这使得我们可以使用函数名作为字符串来调用函数，这意味着我们可以基于定义的`$type`动态调用延迟的函数。

注意，我们直接返回一个`linear-gradient()`，这样调用函数就像`background: stripes($colors)`一样简单。

*注意:如果你正在运行 Sass 3.2 并且不能使用`call`函数，那么把`@return`指令移到条件语句中。*

## 等色光圈

`linear-gradient()`语法的惊人之处在于它基于逗号分隔的列表。这意味着一个格式良好的 [Sass 列表](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#lists)可以直接注入到梯度函数中。我们所要做的就是建立一个逗号分隔的列表，每个项目是一个由颜色和色标组成的两个项目的列表。比如:`red 10%, blue 10%`。

现在让我们回到我们的函数。最简单的情况肯定是所有颜色的宽度都相同。要定义这个宽度，我们要做的就是将`100%`除以列表中的颜色数量。然后我们遍历列表，为梯度函数建立一个干净的列表。

```
@function _stripes-equal-stops($colors) {
  $gradients: ();                                                        // 1
  $stops: 100% / length($colors);                                        // 2

  @for $i from 1 to length($colors) {                                    // 3
    $gradients: append($gradients, nth($colors, $i) $i * $stops, comma); // 4
    @if $i < length($colors) {                                        // 5
      $gradients: append($gradients, nth($colors, $i + 1) $i * $stops);  // 6
    }
  }

  @return $gradients;                                                    // 7
}
```

这看起来很复杂，但实际上非常简单。下面是它的作用:

1.  为我们的颜色和颜色停止实例化一个空列表。
2.  以百分比定义颜色的宽度。
3.  在颜色中循环。
4.  将颜色(`nth($colors, $i)`)及其色标(`$i * $stops`)添加到`$gradients`列表中。
5.  如果列表中还有一种颜色，就像我们刚才做的那样添加它。
6.  最后，返回列表。

例如，将一个`$colors`列表视为`red, yellow, green`。该函数返回的结果将是`red 33.33%, yellow 33.33%, yellow 66.66%, green 66.66%`,在我们的线上产生 3 个大小相等的颜色部分。

## 自定义色标

我们也可以接受颜色和色标的二维列表。类似`red 10%, blue 20%, green 60%`的东西。这意味着红色应该占总宽度的 10%，蓝色应该从 10%到 20%，绿色从 20%到最后。事实上，最后一个色标没有被解析。

```
@function _stripes-custom-stops($colors) {
  $gradients: ();

  @for $i from 1 to length($colors) {
    @if length(nth($colors, $i)) > 1 {
      $color: nth(nth($colors, $i), 1);
      $stop:  nth(nth($colors, $i), 2);

      $gradients: append($gradients, $color $stop, comma);
      @if $i < length($colors) {
        $gradients: append($gradients, nth(nth($colors, $i + 1), 1) $stop);
      }
    }

    @else {
      @warn '`#{nth($colors, $i)}` skipped because it is only one item long while it should be 2: (color, color-stop).';
    }
  }

  @return $gradients;
}
```

代码与前面的函数几乎相同，只是我们没有计算颜色停止，而是从当前循环索引的第二项读取它。如果没有为某个颜色定义颜色停止，我们会警告用户该颜色将被跳过。

## 随机颜色停止

最后，我们可以随机生成我们的颜色停止。你可能已经注意到，在 Treehouse 的例子中，颜色的宽度不一样；有些短一些，有些长一些。如果我们想达到类似的效果，我们可以用一个随机函数来计算色阶。

起初，我让命运来决定颜色停止，然而我经常发现自己面对一个巨大的颜色占据了宽度的 90%，而所有其他颜色都在努力适应剩余的空间。糟透了。我想要的是几乎一样大小的颜色，长度略有不同。

```
@function _stripes-random-stops($colors) {
  @if length(nth($colors, 1)) > 1 {
    @return _stripes-custom-stops($colors);
  }

  $nb: length($colors);
  $gradients: ();
  $variation: 10;
  $median: 100 / $n;

  @for $i from 1 to $nb {
    $stop: $median * $i; 
    $random: rand($stop - $variation, $stop + $variation) * 1%;
    $gradients: append($gradients, nth($colors, $i) $random, comma);
    @if $i < length($colors) {
      $gradients: append($gradients, nth(nth($colors, $i + 1), 1) $random);
    } 
  }

  @return $gradients;
}
```

几乎和以前一样的代码。但是请注意我们如何在`n - 10%`和`n + 10%`之间生成一个随机数，`n`是平均颜色宽度。这确保了所有颜色部分的宽度大致相同，而不是严格相等，从而产生一个整洁的随机大小的颜色效果。

Sass 3.3 中的[随机函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#random-instance_method)和 CodePen 中的[自定义 Ruby one 都只接受一个参数:在 1 和给定值之间滚动的最大值。因此，如果我们希望一个函数生成随机的`min`和`max`值，我们可以像这样非常容易地做到这一点:](http://blog.codepen.io/2013/08/26/random-function-in-sass/)

```
@function rand($min, $max) {
  @return random($max - $min) + $min;
}
```

## 结束语+演示

这样，您就有了一个简洁的函数，可以从颜色列表中生成多色边框。如您所见，代码并不难理解，而且最终组织得很好。这个 API 也很容易使用，所以我认为我们做到了！

你可以在 CodePen 上玩代码:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [DKhkf](http://codepen.io/SitePoint/pen/DKhkf/) 。