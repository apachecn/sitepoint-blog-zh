# Sass 3.4 出来了！

> 原文：<https://www.sitepoint.com/sass-3-4-is-out/>

仅仅在 Sass 3.3 之后的几个月，[3.4 版本已经以“选择性史蒂夫”的代号发布了](http://blog.sass-lang.com/posts/221239-sass-34-is-released)。很奇怪的名字，但是我们会看到这个名字很准确，因为大多数新特性都是关于选择器的。

## SassScript 中的父选择器`&`

*“在 SassScrwhat？”*你问。你可以把 [SassScript](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#sassscript) 看作是 Sass 的语言。Sass 是生态系统，是预处理器，而 SassScript 是在引擎盖下制造 Sass 的东西。或者正如[娜塔莉·韦森鲍姆所说的](http://blog.sass-lang.com/posts/221239-sass-34-is-released):

> “SassScript”是我们称之为 Sass 用于变量、属性值等的迷你语言。它大部分只是 CSS 值，但是它也支持自定义函数、算术等等。

在 Sass 3.4 之前，你不能用*父选择器*做太多事情(虽然我讨厌这个名字，因为它不是父选择器，而是对当前选择器的引用，这完全不同，但无论如何)，除了像这样的事情:

```
.selector {
  .no-svg & {
    // Stuff
  }

 &:hover {
    // (H)o(v|th)er stuff (see what I did there?)
  }
}
```

现在我们能做的更多了。起初，这项新功能应该会在 Sass 3.3 中出现，但由于技术问题，它被推迟了。

基本上，您现在可以像操作任何其他变量一样操作`&`。正如您所期望的那样，它总是包含一个列表列表。

例如，如果您有一个类似于`.foo .bar, .baz`的选择器，那么`&`的上部列表将包含两个项目:`.foo .bar`和`.baz`，它们都是列表。那么，第一个也包含 2 项(`.foo`和`.bar`)，而第二个只包含一项(`.baz`)。

注意，由单个*部分*组成的选择器的 SassScript 表示仍然是一个 2 级深度列表。例如，`.item`将会是`((item,),)`。所以如果你想从`&`那里抢`.item`，你就得跑`nth(nth(&, 1), 1)`(`&`第一项的第一项)。

好的，所以可能不太清楚为什么我们需要这样的东西。诚然，虽然我们已经有一段时间没有这样做了，但有一件具体的事情——实际上经常发生——我们直到现在才能做到:

```
// This doesn't work in 3.3.
// This doesn't work in 3.4.

.selector {
 color: blue;

  a& {
 color: red;
  }
}
```

典型的场景是:*我想动态地限定一个选择器*。在我们的例子中，我们有`.selector`，如果它是一个锚(`a`)，我们希望对它应用自定义样式。

事情仍然没有那么简单，但至少我们现在可以做到:

```
// This doesn't work in 3.3.
// This does work in 3.4.

.selector {
 color: blue;

 @at-root #{a + &} {
 color: red;
  }
}
```

首先，多亏了`@at-root`，我们需要在根级别输出它，否则我们最终会得到类似`.selector a.selector`的东西，这不是我们想要的。然后，我们只需要对整个事情进行插值。

现在，我们可以通过为此创建一个函数来使事情更加模块化。像这样的事情怎么样:

```
// This doesn't work in 3.3.
// This does work in 3.4.

@mixin qualify($selector) {
 @at-root #{$selector + &} {
 @content;
  }
}
```

然后:

```
.selector {
 color: blue;

 @include qualify(a) {
 color: red;
  }
}
```

很酷，不是吗？据我所知，这是 SassScript 中唯一的简单用例之一。可能还有很多其他场景，但是它们通常涉及更复杂的上下文(Sass 框架等)。).

## 选择器功能

为了配合这款名为 Sass scripted~~parent~~reference selector 的全新玩具，并更多地坚持使用 *Selective Steve* 的名字，你会很高兴地知道 Sass 3.4 带来了一些新功能，让你可以玩选择器。

*   [选择器-追加](http://sass-lang.com/documentation/Sass/Script/Functions.html#selector_append-instance_method)
*   [选择器巢](http://sass-lang.com/documentation/Sass/Script/Functions.html#selector_nest-instance_method)
*   [选择器-扩展](http://sass-lang.com/documentation/Sass/Script/Functions.html#selector_extend-instance_method)
*   [选择器-更换](http://sass-lang.com/documentation/Sass/Script/Functions.html#selector_replace-instance_method)
*   [选择器-统一](http://sass-lang.com/documentation/Sass/Script/Functions.html#selector_unify-instance_method)
*   [选择器-解析](http://sass-lang.com/documentation/Sass/Script/Functions.html#selector_parse-instance_method)
*   [是-超级选择符](http://sass-lang.com/documentation/Sass/Script/Functions.html#is_superselector-instance_method)
*   [简单选择器](http://sass-lang.com/documentation/Sass/Script/Functions.html#simple_selectors-instance_method)

我不会深入研究这些函数，因为它们对大多数开发人员(包括我)来说都是无用的。事实上，这个功能是专门针对框架开发人员的(Natalie 在 Twitter 上向我证实了这一点，尽管我无法找到该推文，因为 Twitter 的搜索非常蹩脚)。

基本上，你有一个功能可以做任何你想做的事情；无论是将一个选择器附加到另一个选择器，将一个选择器嵌套在另一个选择器下，替换另一个选择器中的一个选择器，统一选择器，等等。

然而，我想谈谈其中的两个功能:`selector-extend`和`selector-replace`。

首先，值得指出的是`selector-extend`的工作方式与`@extend`指令完全相同。事实上，我怀疑这就是引擎盖下的指令所使用的。

其次，`selector-replace`函数实际上比人们最初认为的要有效得多。警告！它是**而不是**一个字符串替换函数。它实际上利用了`@extend`指令的力量来替换选择器，而不会破坏放在第一位的逻辑。

也就是说——我无耻地复制粘贴了娜塔莉的例子——`selector-replace(".foo.bar.baz", ".foo.baz", ".qux")`(在`.foo.bar.baz`中用`.qux`替换`.foo.baz`)返回`.bar.qux`。常规的字符串替换函数不会返回任何内容，因为`.foo.bar`在`.foor.bar.baz`中不作为字符串存在。这就是 Sass 选择器函数发挥作用的地方。

反正要我说，那些功能大部分在任何合理的项目里都接近于无用。有些人已经找到了用例，例如([马克·明特尔在这里](https://medium.com/@marcmintel/pushing-bem-to-the-next-level-with-sass-3-4-5239d2371321)和[米卡·戈德博尔特在这里](http://www.phase2technology.com/blog/everything-you-need-to-know-about-sass-3-4/))，但就我而言，我认为这些并不能解决太多问题，当他们找到时，会增加太多的代码复杂性。[保持简单的斗嘴](https://www.sitepoint.com/keep-sass-simple/)！

## 使用`@error`进行错误处理

好了，选择器已经够了！如果说 Sass 3.4 中有什么是我一直在等待的，那就是`@error`指令。其实作为一个框架开发者，我等这样一个特性已经快两年了！

朋友们，`@warn "..."; @return null;`的日子已经过去了。`@error`万岁！直到现在，Sass 还没有一个干净的方法来处理错误。如果你是我文章的长期读者，你可能熟悉如下模式:

```
@function rainbow($unicorn) {
 @if type-of($unicorn) != "unicorn" {
 @warn "What?! `#{$unicorn}` is not a unicorn? Are you serious?!";
 @return null;
  }

  // Proceed with the code.
}
```

所以基本上，我们必须检查我们想检查的任何东西，通过`@warn`警告用户，返回`null`(或`false`或其他什么)，然后用我们能有的所有逻辑来处理这个问题。

让我们用 Sass 3.4 的魔力重写我们的代码:

```
@function rainbow($unicorn) {
 @if type-of($unicorn) != "unicorn" {
 @error "What?! `#{$unicorn}` is not a unicorn? Are you serious?!";
  }

  // Proceed with the code.
}
```

嘣。如果解析器到达了`@error`指令，它就会崩溃并在控制台中打印错误消息，就好像您遇到了语法错误或其他问题一样。代码就停止了。这太棒了！

## 一些关于颜色的改进

首先，添加了新的`rebeccapurple`颜色(#663399)，现在在 Sass 中被视为任何其他颜色。[这是](http://meyerweb.com/eric/thoughts/2014/06/19/rebeccapurple/)，献给(CSS 传奇人物)Eric A. Meyer 的女儿，她最喜欢的颜色是紫色，最近刚刚去世，年仅 6 岁。

此外，Sass 处理颜色的方式也有一些微小但不错的改进。例如，Sass 将尽可能保持颜色的创作方式，除了在压缩模式下，它将试图找到最紧凑的方式来表示颜色。

此外，`rgb()`、`rgba()`、`hsl()`和`hsla()`功能现在将它们的值箝位到最小/最大授权值，而不是抛出错误。这一改变是为了遵守来自 [CSS 颜色级别 4](http://dev.w3.org/csswg/css-color/) 的官方 CSS 规范。

最后，因为对此有很多抱怨，Sass 现在会在插值中使用颜色时显示警告(`#{}`)。基本上，一些开发人员表达了他们对代码结果的不满，如下所示:

```
@each $color in darkolivegreen, firebrick, blanchedalmond {
  .item-#{$color} {
 color: $color;
  }
}
```

虽然这在除压缩模式之外的任何模式下都会产生预期的结果，但是在为生产编译 Sass 时，产生的 CSS 是:

```
.selector-#556b2f{color:#556b2f}.selector-#b22222{color:#b22222}.selector-#ffebcd{color:#ffebcd}
```

正如您所看到的，颜色名称已经被它们的十六进制等价物所取代，因为它们更短，这可以节省一些字节。解决方案是引用列表中的颜色(`"darkolivegreen", "firebrick", "blanchedalmond"`)，但是为了防止更多的混淆，Sass 将直接在控制台中发出警告。

## 你不开心吗？！

![Are you entertained?](img/8e0b0a0b8f60da43a247e186d85c737f.png)

如果所有这些对你来说还不够，那么不用担心，我的朋友，因为还有更多关于选择性史蒂夫(即 Sass 3.4)的发现。

让我试着用几个我认为有趣的额外特性来娱乐你一下。很快的:

*   您现在可以使用`map-remove`功能一次移除多个按键。这现在与`map-merge`一致，它也接受几对。
*   `@extend`指令现在可以正确处理伪类，这在 3.4 版之前是不可能的。
*   Sourcemaps 已经有了很大的改进。

最后，请务必阅读[官方变更日志](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html)以获取以上所有内容的更多信息。

## 分享这篇文章