# 带有 Sass 和 calc()的创造性网格系统

> 原文：<https://www.sitepoint.com/creative-grid-system-sass-calc/>

在我们开始讨论 Sass 和这个项目的代码之前，让我提醒你这只是一个快速的实验，绝不应该在真实的环境中使用。所以接下来的只是一个概念证明，我们可以用 CSS 中的几个 Sass 变量和`calc()`函数做些什么。

可能仍然有一些缺陷和边缘情况没有在这个例子中处理。如果你对此有什么改进的建议，一定要分享。否则请记住这只是一个实验。

## 我们要做什么？

在过去的几天里，我一直在使用 CSS 中的 calc()函数。对于跨单位计算来说，这可能是一件好事，如果有几个 Sass 变量可以使一切都易于定制，那就更好了。

然后 Twitter 上有人问我，如果必须的话，我会如何构建一个网格系统。首先，我不会。已经有太多的 CSS 网格系统和框架，构建另一个将是重新发明轮子。事实上，我曾经写过一篇名为[我们不需要你的 CSS 网格](http://hugogiraudel.com/2013/03/04/css-grids/)的文章，即使我现在的观点比我第一次写文章的时候稍微平和了一些。长话短说:一些聪明人已经建立了比我能想到的任何网格系统都更强大的网格系统(比如[断点](https://github.com/Team-Sass/breakpoint)和 [Susy](http://susy.oddbird.net/) )。

无论如何，我不想做一个像其他人一样的网格系统，所以我想*“嘿，为什么不和`calc`*一起玩呢？我想让事情尽可能简单——三个变量，一个断点，一个混合。仅此而已。三个可定制的变量是:

*   网格中的列数(默认为`$grid-columns: 12`)
*   列间装订线的宽度(默认`$grid-gutter: 10px`)
*   屏幕宽度，在此宽度下我们移动到单列(默认`$grid-breakpoint: 48em`)

我想要的另一个特性(实际上越来越不特别)是避免在 DOM 中使用类名。更重要的是，我想在样式表中做任何事情。这也意味着我必须使用像`:nth-of-type`这样的高级 CSS 选择器。

## 混合芯

我总是试图让我的函数和 mixin 签名尽可能简洁。对于这一个，我最终只需要一个参数:

```
@mixin grid($cols...) {
  // Sass magic
}
```

…它实际上是可变数量的参数(也称为 [`argList`](http://sass-lang.com/documentation/Sass/Script/ArgList.html) )，如`$cols`变量后的省略号所示。主要思想是借助于`:nth-of-type` CSS 选择器，遍历这些参数并基于这些参数处理列。例如，在基于 12 列的网格上调用`grid(6, 6)`将创建由`10px`栏分隔的 2 列。

但是在循环之前，让我们添加几个声明来构建布局:

```
@mixin grid($cols...) {
  overflow: hidden;

    > * {
      float: left;
      margin-right: $gutter;
    }

  // Loop
}
```

为了定位调用 mixin 的容器中的所有子容器，我们使用带有子组合子`>`的`*`选择器。我打赌你们中的一些人已经气喘吁吁了。嗯……是的。现在是 2014 年，这意味着 CSS 性能[不再是问题](http://calendar.perfplanet.com/2011/css-selector-performance-has-changed-for-the-better/)。此外，由于我们使用的是`calc()`，我们将不支持任何低于 Internet Explorer 9 的版本，所以我们使用的是`> *`，好吧！

在我们的声明块中，我们浮动所有直接的子元素，并添加一个右边距。包装器有`overflow: hidden`来清除内部浮动。如果你更喜欢 [micro-clearfix](http://nicolasgallagher.com/micro-clearfix-hack/) 的人，一定要改变它以适应你的需要。如果是一个列表，不要忘记添加`list-style: none`和`padding-left: 0`，如果你的 CSS 重置还没有这样做的话。

现在，循环！

```
@for $i from 1 through length($cols) {
  > :nth-of-type(#{$i}n) {
        $multiplier: $i / $grid-columns;
        width: calc(100% * #{$multiplier} - #{$grid-gutter} * (1 - #{$multiplier}));
  }
}
```

哎哟，这看起来太复杂了！让我们一次处理一行。对于整个解释，让我们假设我们正在调用`grid(3, 7, 2)`，这将是一个非常标准的布局，一个中心容器用 2 个侧栏圈起来。别忘了我们有一个 12 列的布局，有 10px 的间距，就像我们之前在变量中定义的那样。

首先，选择器。暂时不要注意`n`，我们将在下一节看到它为什么会在那里。现在你所要理解的是，我们一个接一个地选择孩子，给他们应用宽度。顺便说一下，`:nth-of-type`前的空格是一个隐式的`*`。

现在是`calc()`功能。计算看起来很激烈，不是吗？实际上，如果你想象一下，这并不难理解。让我们来处理我们的第一个专栏(`3`)。如果我们一步一步地研究我们的方程，我们会得到:

1.  100% * 2012 年 3 月–10px *(2012 年 3 月 1 日)
2.  100% * 0.25–10px * 0.75
3.  25%–7.5 像素

我们的列将扩展到总宽度的 25%减去 7.5 个像素(希望目标浏览器将处理子像素着色)。还不清楚？让我们看看主容器(`7`)的另一个例子:

1.  100% * 2012 年 7 月至 10px *(2012 年 7 月 1 日)
2.  100% * 0.58333333333333334–10px * 0。56333.86333333637
3.  58.333333333334%–4.3 像素。366368636

最后但同样重要的是，我们的右边栏(`2`):

1.  100% * 2012 年 2 月–10px *(2012 年 1 月–2 月)
2.  100% * 0.16666666666666666–10px * 0。1666666666
3.  16.666666666666666%–8.403633666

现在，如果我们将三个结果相加以确保一切正确:

1.  (25%–7.5%)+(58.333333333333334%–4.166666666666663 px)+(16.666666666666666%–8.33333333333334 px)
2.  (25%+58.3333333333333334%+16.66666666666666666%)–(4.166666666666663 px+8.3333333333334 px+7.5 px)
3.  100%–20 像素

这是有道理的，因为我们有 2 个 10px 的水槽。这些都是计算的内容。没那么难吧。

最后一件重要的事情:我们用另一个高级 CSS 选择器从循环外的最后一个子元素中移除右边距:

```
> :nth-of-type(#{length($cols)}n) {
  margin-right: 0;
}
```

如果您想知道，对所有子对象应用边距，然后从最后一个子对象中移除边距，这比只对最后一个子对象以外的所有子对象应用边距要好。两种我都试过。

*注意:当在`calc()`函数中使用 Sass 变量时，如果你想让它工作的话，不要忘记用[插值它们](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#interpolation_)。记住`calc()`不是由 Sass 编译的，而是由浏览器自己编译的，所以它需要将所有值正确地写入函数中。*

## 未知数量的项目

我想不用说，网格系统对嵌套网格处理得很好。我想要的一件事是能够嵌套网格，并且子网格的数量未知。这有很多原因，无论是 Ajax 加载、lazyload 还是其他。

因为我们不知道孩子的数量，所以我们不能在 mixin 包含中包含所有孩子的“比率”。所以我想出了一个只需要单行的模式的解决方案(例如`grid(3, 3, 3, 3)`)。然后，如果有 4 个以上的孩子，他们应该仍然表现得像一个 4 列网格(新行等等)。

此外，您可能已经注意到，我们没有为每一行使用任何子包装器，因为我们没有对 DOM 做任何更改。因此，我们需要确保容器的最后一个子容器和每行的最后一个子容器都没有边距。

因此有了我们之前看到的`:nth-of-type()`选择器。这意味着，例如，子节点`4`、`8`、`12`等不会有右边距。

## 处理小屏幕

现在我们已经拥有了一切，我们应该确保网格在小屏幕上优雅地降级。我尽量保持简单:在给定的断点下，所有东西都作为一列堆叠。

```
@mixin grid($cols...) {
    // Mixin core

    @media (max-width: $breakpoint) {
      float: none;
      width: 100%;
      margin-right: 0;
    }
}
```

低于此屏幕宽度，元素的行为就像没有网格系统一样。也就是说，块级元素会拉伸以适合其父元素的宽度，并在源顺序中下移一个位置。一种简单而有效的方法。

## 使用占位符改进 CSS 输出

到目前为止，它做得很好。一切都很好，我们很开心，不是吗？然而，如果我们碰巧在同一个页面上有多个网格，有许多重复的 CSS 规则可以合并，以使输出更轻松。

我们可以让 mixin 扩展占位符，而不是直接转储 CSS 规则。首先，让我们创建占位符。

```
%grid-parent {
    overflow: hidden;
}

%grid-child {
    float: left;
    margin-right: $grid-gutter;
}

%grid-last-child {
    margin-right: 0;
}

@for $i from 1 through $grid-columns {
    %grid-col-#{$i} {
          $multiplier: $i / $grid-columns;
          width: calc(100% * #{$multiplier} - #{$grid-gutter} * (1 - #{$multiplier}));
    }
}

@media (max-width: $grid-breakpoint) {
    %grid-fallback {
      float: none;
      width: 100%;
      margin-right: 0;
    }
}
```

前三个占位符不言自明。对于第四个占位符，为了避免直接在 mixin 中计算宽度，我们用一个`@for`循环为网格创建了所需数量的占位符(例如，12 列 12 个)。

关于`%grid-fallback`占位符，我们必须在媒体查询中实例化它，以便能够从样式表中其他地方的等效媒体查询中扩展它。事实上，Sass 对跨媒体@extend 有一些限制(即它不起作用)。

下面是 mixin 现在的样子——只不过是扩展了占位符:

```
@mixin grid($cols...) { 
    @extend %grid-parent;

    > * { 
      @extend %grid-child;

      @for $i from 1 through length($cols) {
        &:nth-of-type(#{$i}n) {
              @extend %grid-col-#{nth($cols, $i)};
        }
      }

      &:nth-of-type(#{length($cols)}n) {
        @extend %grid-last-child;
      }
    }

    @media (max-width: $grid-breakpoint) {
      @extend %grid-fallback;
    }
}
```

## 最后的想法

嘿，最后还是挺激烈的，不是吗？老实说，起初我认为这很容易，但我想到我必须做一些高级的 Sass 来保持 CSS 输出的整洁，就像是手写的一样。一个很好的经验法则是，Sass 文件的输出应该与您自己编写的输出非常相似。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [euKgi](http://codepen.io/SitePoint/pen/euKgi/) 。