# 了解 Sass 单位

> 原文：<https://www.sitepoint.com/understanding-sass-units/>

我过去在自己的博客上写过关于 Sass 中的[单元的文章，不久前还写过一篇](http://hugogiraudel.com/2013/09/03/use-lengths-not-strings/) [CSS-Tricks Sass 片段](http://css-tricks.com/snippets/sass/correctly-adding-unit-number/)，但是考虑到我经常注意到对我们心爱的预处理器中单元工作方式的混淆和误解，我认为一篇完整的文章毕竟不是一个坏主意。

经过两年每天编写和阅读 Sass 代码，我必须说误用 Sass 单元的片段、文章和库代码不计其数。在过去，我自己也曾为此感到内疚，我不止一次目睹 Sass 核心设计人员试图教开发人员正确使用 Sass 单元。

这场战斗显然还没有结束，所以让我们希望这篇文章在某种程度上会是最后一击。

## 现实生活中的单位

在考虑代码之前，我认为理解数字和单位在现实生活中的行为是很重要的。你可能还没有注意到这一点，但是我们在日常生活中使用的数字很少有没有单位的。当我们说“限速 50”时，隐含的意思是“每小时 50 公里”；在这种情况下，单位是*公里/小时*。当我们说“看，鸡蛋！我要一打那些”，单位是*鸡蛋*:一打鸡蛋。当然也有例外，但是在我们的日常生活中，大多数数字的用法通常涉及到一个单位。

同样，对带单位的数字进行数学运算也遵循一些规则。例如，为什么 5 米的面积比 5 米的面积大 25 平方米(`m²`)是有原因的。1 米边的立方体也是如此:它的体积是 1 米 3 T2，因为 1 米比 1 米不是 3 米，而是 1 立方米。

我喜欢克里斯·埃普斯坦的说法:

> 2 英尺^ 2 是 4 平方英尺(面积)，而不是 4 英尺(长度)。在这个计算中，正确的做法是让单位和数字相乘。思考如何正确使用单位进行数学运算的最简单的方法是，把一个单位想象成代数中的一个未知数。
> (2 * x) ^ 2 是 4 * x <sup>2。</sup>如果计算结束时的单位没有抵消成你期望的尺寸，这是一个数学错误的好迹象。

我们还必须明白，有些单位实际上是不兼容的。例如，2 个香蕉+ 3 个苹果的结果会是什么？事实证明，我们只能计算 2 个香蕉+ 3 个苹果，因为苹果和香蕉不是相容的单位。另一方面，1 米+ 10 厘米的结果给出 1.1 米或 110 厘米。这是因为米和厘米都是公制的一部分，因此是兼容的。

当然，单位不一定要成为公制的一部分才能相互兼容。例如，光年和英寸是兼容的单位，因为它们都服务于相同的目的:测量距离。当然，一个等式不可能同时包含光年和英寸，因为这两个单位位于距离单位光谱的不同边缘。尽管如此，他们是兼容的。

## 萨斯中的单位

既然我们已经完全掌握了单位在现实生活中的工作方式，是时候在 Sass 中处理单位了。当我说 Sass 中的单元与现实生活中的单元完全一样时，我希望不会让您感到惊讶。有些单位兼容，有些不兼容。两个单位相同的数相乘会产生平方单位，依此类推。

让我们一次处理一件事，从比较单位开始。Sass 有一个兼容单元的[表，可以计算出两个单元是否具有可比性和兼容性。比如`turn`(圈数)和`deg`(度数)兼容；`s`(秒)`pt`(分)都不是。](https://github.com/sass/sass/blob/31c6c531fabc1538d37db334b2cf8b976e289c3b/lib/sass/script/value/number.rb#L455-L483)

这意味着`12px + 1in`是一个将产生`108px`的完全有效的操作。*为什么是 108px？*你问。因为 1 英寸等于 96 个像素，并且运算结果用第一个成员的单位表示，在本例中是像素。如果我们写了`1in + 12px`，结果应该是`1.125in`。

现在，平方单位。虽然两个数字乘以相同的单位会产生平方单位，但实际上 Sass 不允许平方单位，因为它们在 CSS 中没有意义。当这种情况发生时，Sass 简单地抛出如下错误:

> 4px*px 不是有效的 CSS 值。

我花了一些时间才明白，这条消息并不意味着我们试图执行`4px * px`。在前面提到的错误消息中，`px*px`表示`px²`，因此该消息也可以是:

> 4px 不是有效的 CSS 值。

这很有意义:任何使用`px²`作为单位的值，或者基本上任何平方单位，都不是有效的 CSS 值。CSS 不支持正方形单位，因为拥有它们没有任何意义。现在，为了记录，如果您试图运行`4px * px`，Sass 将抛出一个不同的错误消息:

> 未定义的操作:“4px 乘以 px”。

## 发生了什么事?

好了，这已经很久了，也许你还是不知道这一切的意义。这是我成功的时刻，别担心。

当您有一个无单位的数字，并希望将其转换为特定的单位时，不要将单位作为字符串追加。拜托，我求你别再胡闹了。一个单位应该总是和一个数值联系在一起，因为它本身没有任何意义。`cm`本身不是一个单位，它是一个字符串；`1cm`是一个单位，如果这有意义的话。

在将逻辑算术原理应用于 Sass 时，将我们的值乘以所需单位的 1 项就足够了。

```
$value: 42;

.foo {
 font-size: ($value * 1px); // {number} 42px
}
```

现在，你可能会问自己*简单地将单位作为字符串追加有什么问题，因为它的工作方式与*相同，并且你可能在大多数情况下都是正确的。现在考虑一下这个:

```
$value: 13.37;

.foo {
 font-size: round($value + em); // {string} 13.37em
}
```

你能猜到如果我们尝试编译这段代码会发生什么吗？如果你的答案是`13em`，我很抱歉地告诉你，你错了。正确答案是:

> $ value:“13.37 em”不是“round”的数字

之所以会发生这种情况，是因为简单地在数字右侧添加单位会隐式地将值转换为字符串，从而导致`round`函数的参数不正确(该函数需要一个数字)。

当然，如果你不打算对新值进行额外的数学运算，你永远不会注意到差异，CSS 也不会。然而，当你知道 Sass 有多聪明的时候，像这样和单元一起工作似乎很糟糕。为了一致性、优雅和有意义的代码，我强烈建议您正确地转换您的无单位数字。

其实把问题倒过来，想去掉一个数的单位，都是有道理的。如果您立即想到的解决方案是这样的:

```
$value: 42px;
$unitless-value: str-slice($value + ', 1, 2); // {string} 42
```

…那么恐怕你这样做是错误的。移除该单元就像除以该单元的一个成员一样简单。还是那句话，就像在现实生活中。如果你想从 25m 得到 25，你必须用 25m 除以 1m。

```
$value: 42px;
$unitless-value: (42px / 1px); // {number} 42
```

那么我们有一个数字，而不是一个字符串。这是完全合乎逻辑的，优雅的，并防止任何可能令人惊讶的怪异行为。

## 0 的大小写

在离开之前，我想推断一件事。在本文的前面，您可能已经了解到两个不同单位的数字之间的加减结果是用第一个成员的单位表示的。

当我们出于某种原因想要将一个单位转换成另一个(兼容的)单位时，我们可以利用这一点。例如，假设您想要将秒转换为毫秒。我想你可以把这个值乘以 1000 来得到毫秒。另一方面，您可以通过将该值添加到 0 毫秒来将它留给 Sass，如下所示:

```
$value: 42s;
transition-duration: (0ms + $value); // {number} 42000ms
```

对于这种情况，我同意我们也可以自己计算价值。但是当涉及到将度数转换成弧度，或者将每厘米点数(`dpcm`)转换成每像素点数(`dppx`)时，最好还是让 Sass 来处理，而不是进行一些相当复杂的计算。

```
$resolution: 0dppx + 42dpcm; // {string} 1587.40157dppx
$angle: 0rad + 42deg;        // {string} 0.73304rad
```

如果您担心这个小技巧看起来太“粗糙”而不能按原样使用，您可以创建一个简短的函数来使 API 更友好一些。我不能肯定这是否值得麻烦，但我会让你来判断。以下是我对这个函数的理解:

```
/// Convert one unit into another
/// @author Hugo Giraudel
/// @param {Number} $value - Initial value
/// @param {String} $unit - Desired unit
/// @return {Number}
/// @throw Error if `$unit` does not exist or if units are incompatible.
@function convert-unit($value, $unit) {
 $units: (
    'px': 0px,
    'cm': 0cm,
    'mm': 0mm,
    '%': 0%,
    'ch': 0ch,
    'in': 0in,
    'em': 0em,
    'rem': 0rem,
    'pt': 0pt,
    'pc': 0pc,
    'ex': 0ex,
    'vw': 0vw,
    'vh': 0vh,
    'vmin': 0vmin,
    'vmax': 0vmax,
    'deg': 0deg,
    'turn': 0turn,
    'rad': 0rad,
    'grad': 0grad,
    's': 0s,
    'ms': 0ms,
    'Hz': 0Hz,
    'kHz': 0kHz,
    'dppx': 0dppx,
    'dpcm': 0dpcm,
    'dpi': 0dpi,
  );

 @if map-has-key($units, $unit) {
 @return map-get($units, $unit) + $value;
  }

 @error "Unknown unit `#{$unit}`.";
}
```

重写我们之前的例子:

```
$resolution: convert-unit(42dpcm, 'dppx'); // {string} 1587.40157dppx
$angle: convert-unit(42deg, 'rad');        // {string} 0.73304rad
```

## 最后的想法

因此，如果我们总结一下我们在这篇文章中看到的内容:

*   Sass 中的单位和现实生活中的单位完全一样；
*   要给一个数一个单位，就要把所需单位的每个成员的值相乘(如`42 * 1px`)；
*   从一个数中去掉一个单位，除以相关单位的一个成员(如`42px / 1px`)；
*   当两个兼容单位不同的数相加或相减时，结果用第一个成员的单位表示；
*   要将一个值从一个单位转换到另一个单位(兼容)，将该值添加到最终单位的 0 成员(例如`0px + 42in`)。

就是这样。我希望从现在开始，我们将会看到越来越少的单元偏差，得到更优雅的单元处理。正如克里斯所说:

> 我觉得萨斯的数学真的很好，一旦灯泡亮了！

## 分享这篇文章