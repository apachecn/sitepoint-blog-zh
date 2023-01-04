# 为什么应该避免 Sass @extend

> 原文：<https://www.sitepoint.com/avoid-sass-extend/>

大约一年前，我写了 [Mixin 或占位符](https://www.sitepoint.com/sass-mixin-placeholder/)(我在 SitePoint 的第一篇文章)，紧接着是[没人告诉你的 Sass Extend](https://www.sitepoint.com/sass-extend-nobody-told-you/) 。一年后，我在这里再次改变我的想法，写下为什么我认为来自萨斯的`@extend`指令真的远非埃尔多拉多指令。

**TL；博士:延伸是看不见的。延伸并不一定有助于文件重量，相反的说法。扩展不适用于跨媒体查询。延伸不灵活。Mixins 绝对没有缺点。**

但首先，我应该提醒你一下。`@extend`是一个 Sass 特性，旨在为选择器 A 提供一种从选择器 B 扩展样式的方法。这样做时，选择器 A 将被添加到选择器 B 中，因此它们共享相同的声明。例如:

```
.selector-A {
 @extend .selector-B;
 unicorn: true;
}

.selector-B {
 rainbow: true;
}
```

这个 Sass 片段将产生这个 CSS:

```
.selector-A {
    unicorn: true;
}

.selector-B,
.selector-A {
    rainbow: true;
}
```

这就是你需要了解的关于`@extend`的全部内容，这样你才能完全理解这篇文章，所以现在我们已经讨论了基础知识，是时候继续了。我认为`@extend`是一个骗人的特性，有相当多的理由。

不过不用担心，这不是另一篇关于如果你搞乱*【Sass】`@extend`如何输出[超长选择器](http://pastebin.com/Jy9PqFTy)*的文章。让我们试着深入挖掘，好吗？

## 延伸是看不见的

Sass 只是一个工具。它旨在帮助开发人员编写 CSS。然而在这种情况下, *write* 不仅仅意味着写代码。它也意味着*维护*。Sass 是一个工具，可以帮助人们回到样式表并更新代码，而不必花费数小时来弄清楚发生了什么以及#FA7A55 颜色还在哪里。

正因为如此，Sass 和其他任何工具一样，应该是透明的。当编写一行 Sass 时，我们应该能够计算出从中会产生什么样的 CSS。例如，如果我给你这个:

```
// _mixins.scss
@mixin center($max-width) {
 max-width: $max-width;
 width: 100%;
 margin: 0 auto;
}

// main.scss
.container {
 @include center(1170px);
}
```

您可以通过查看 mixin 来告诉我，最终的 CSS 将是:

```
.container {
    max-width: 1170px;
    width: 100%;
    margin: 0 auto;
}
```

很容易理解这一点，因为您知道包含一个 mixin 会获取 mixin 的内容，用给定的参数替换变量，并在包含 mixin 的位置打印出来。这实际上是 mixins 的酷之处，它们是*明显的*。

而这是`@extend`的第一个问题。每当你决定扩展一个选择器，你绝对不知道会发生什么。据你所知，结果可能是什么都不做，甚至导致灾难性的副作用。扩展选择器可以出现一次(最好的情况)，或者在复合选择器中出现多次，这将是非常糟糕的。

现在，显然有些情况下，您**知道**扩展的选择器在整个样式表中只出现一次，在这种情况下，扩展它完全没问题。例如，对于助手来说就是这样。考虑:

```
%clearfix::after {
 content: '';
 display: table;
 clear: both;
}
```

这通常是你的`_helpers.scss`文件的一部分，包含在样式表的顶部。`%clearfix`占位符不会是任何其他选择器的一部分。永远不会。因此，扩展它是相当透明的:扩展选择器将跳到样式表的顶部来连接`%clearfix::after`。

无论如何，这使我认为扩展选择器只有一个合理的理由:它是一个助手，在整个样式表中只存在一次。即使这样，我认为 mixin 还是更好。

## 就性能而言，扩展不一定比混合好

有一个古老的传言说`@extend`在性能方面比 mixins 更好，因为它们对选择器进行分组，而不是一遍又一遍地重复相同的 CSS 声明。人们常说:

```
%toolicorn {
 unicorn: rainbow;
 status: happiness;

 &::grandeur {
 level: infinite;
    }
}

.wanna-be-a-unicorn {
 color: hotpink;
 @extend %toolicorn;
}

.wanna-be-a-unicorn--too {
 color: deepskyblue;
 @extend %toolicorn;
}
```

…在性能方面远远优于:

```
@mixin toolicorn {
 unicorn: rainbow;
 status: happiness;

 &::grandeur {
 level: infinite;
    }
}

.wanna-be-a-unicorn {
 color: hotpink;
 @include toolicorn;
}

.wanna-be-a-unicorn--too {
 color: deepskyblue;
 @include toolicorn;
}
```

理论上，这是千真万确的。第一个 CSS 输出比第二个更短，也可能更优雅。自己看，这是`@extend`版本(285 个字符):

```
.wanna-be-a-unicorn,
.wanna-be-a-unicorn--too {
    unicorn: rainbow;
    status: happiness;
}

.wanna-be-a-unicorn::grandeur,
.wanna-be-a-unicorn--too::grandeur {
    level: infinite;
}

.wanna-be-a-unicorn {
    color: hotpink;
}

.wanna-be-a-unicorn--too {
    color: deepskyblue;
}
```

这是 mixin 驱动的版本，有 304 个字符:

```
.wanna-be-a-unicorn {
    color: hotpink;
    unicorn: rainbow;
    status: happiness;
}

.wanna-be-a-unicorn::grandeur {
    level: infinite;
}

.wanna-be-a-unicorn--too {
    color: deepskyblue;
    unicorn: rainbow;
    status: happiness;
}

.wanna-be-a-unicorn--too::grandeur {
    level: infinite;
}
```

对于这样一个小例子，差别非常小，但是它可以随着 mixin/placeholder 内容的增加而快速增加。因此，从理论上讲，扩展占位符比包含混合更有利于文件大小是有道理的。

然而，文件通常是从服务器压缩的。Gzip 基于 DEFLATE 和 LZ77；简而言之，字符串重复得越多，越有利于压缩。当把这放在上下文中，这意味着一旦 Gzip 滚动了整个事情，差异可能是不存在的。

这与将复制的媒体查询放入选择器时的争论完全相同。[一旦 Gzip 完成了它的任务，就没什么区别了](http://sasscast.tumblr.com/post/38673939456/sass-and-media-queries):

> …我们详细讨论了组合与分散媒体查询是否会对性能产生影响，并得出结论，这种差异虽然难看，但在最坏的情况下是极小的，在最好的情况下基本上不存在。
> [山姆·理查兹](https://twitter.com/snugug)关于[断点](https://github.com/at-import/breakpoint)

因此，因为代码重复而使用`@extend`而不是 mixins 是站不住脚的。继续前进。

## 跨媒体上下文扩展是不可能的

你知道的，对吧？当试图从一个`@media`指令中扩展一个外部选择器时，Sass 哽咽着说:

> 您不能从@media 内部@扩展外部选择器。在同一个指令中只能@extend 选择器。

有人试图用 hack-y 解决方案(包括 SitePoint 上发布的 [mine](https://www.sitepoint.com/cross-media-query-extend-sass/) )来填补这一空白，但这不是一个好主意。在生产中使用这种东西是愚蠢的。

在这一点上，我们不能跨越不同媒体环境的原因完全是技术性的。尽管如此，这仍然是一件非常令人讨厌的事情。为了解决这个问题，*不那么糟糕的*想法是用 mixin 包装占位符，这样你就可以选择`@extend`或者包含，这取决于你是否在`@media`块中。我在这里描述了这种技术[，这仍然是我在工作中使用的。概念的快速验证:](http://hugogiraudel.com/2014/03/31/getting-the-most-out-of-sass-placeholders/)

```
// _helpers.scss
@mixin clearfix($extend: true) {
 @if $extend == true {
 @extend %clearfix;
    } @else {
 &::after {
 content: '';
 display: table;
 clear: both;
        }
    }
}

%clearfix {
 @include clearfix($extend: false);
}

// main.scss
.container {
 @include clearfix;
}

@media (min-width: 42em) {
    .my-other-selector {
 @include clearfix($extend: false);
    }
}
```

据我所知，它工作得很好，并没有涉及太多的复杂性，但在某种程度上，它仍然是另一个抽象层。然而，对于刚开始使用 Sass 的人来说，代码可能看起来有点复杂。

无论如何，你不能跨不同的媒体指令扩展一个选择器，这太糟糕了。出于这个原因，迁移到基于 mixin 的设置可能是值得的，在那里样式可能会重复(参见第*节，就性能而言，扩展不一定比 mixin 好*),但是你可以做任何你想做的事情，无论何时，以你想要的方式。

## 扩展不灵活

根据他们自己的定义，mixins 提供了比`@extend`更强大的功能。事实上，他们:

*   接受[个参数](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixin-arguments)；
*   可以为它们的参数定义默认值；
*   可以定义[变量参数](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variable_arguments)(你也可能想阅读[这篇关于 mixin 参数](https://www.sitepoint.com/sass-multiple-arguments-lists-or-arglist/)的文章)；
*   允许任何内容通过 [`@content`](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixin-content) 。

所有这些原因都使它们成为您选择灵活性的特性。向 mixin 添加额外的参数变得非常容易，因为您需要它做更多的事情，而不会引入任何 API 中断。

另一方面，`@extend`相当严格。如果您需要代码片段接受一个变量，您必须将占位符更改为 mixin，然后更新扩展该占位符的所有规则以包含 mixin。不仅相当乏味，而且肯定不太灵活。

## 最后的想法

所以`@extend`糟透了，是吗？我不确定。也许我只是不够熟练，看不到这个功能的真正潜力，但似乎我不是唯一一个意识到`@extend`真的不是一个杀手级功能的人。事实上，我建议你阅读这些文章来进一步推动事情的发展:

*   [何时使用@ extend 何时使用 mixin](http://csswizardry.com/2014/11/when-to-use-extend-when-to-use-a-mixin/) 作者哈里·罗伯茨；
*   不要在弗莱德·梅尔的《萨斯》中过分夸大自己。

总之。`@extend`可以凌乱。我的建议是尽可能避免使用 mixins，因为在一天结束时，这完全没有区别。

## 分享这篇文章