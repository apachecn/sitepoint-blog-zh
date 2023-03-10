# 保持简洁

> 原文：<https://www.sitepoint.com/keep-sass-simple/>

再过几个月，我开始使用 Sass 就两年了。几乎每天都有。在工作。在家。兼职项目。以至于人们在 Sass 的时候会寻求我的帮助，相信我，我非常乐意尽我所能提供帮助。

有些要求是公平的。有些非常简单，但是我们都从某个地方开始。有些人简直疯了。听着，我不做评判；在某种程度上，每个使用 Sass(或任何其他预处理器)的人都想过度设计一些东西来使事情变得简单。包括我，而且不止一次。

## 让我们优化！

有一天，有人问我如何做到这一点:

```
.class {
 width: 20px;
}

.other-class {
 width: em(.class:width);
}
```

基本上就是把`.class`宽度转换成`em`然后作为`.other-class`的宽度。想一想。不仅仅是因为 Sass 中缺乏上下文,`px`到`em`很难实现，现在还需要将一个选择器的属性值作为另一个选择器的属性值进行引用。没有。

![Nope bear](img/08dd3556883c9cfa3d47d6809f9c4bd7.png)

就连以相当先进而闻名的[手写笔](http://learnboost.github.io/stylus/)也做不到这一点。充其量可以参考*同*代码块中属性的值(称为[属性查找](http://learnboost.github.io/stylus/docs/variables.html#property-lookup))。显然，更保守的萨斯也做不到这一点。

但问题不在于有没有能力做这样的事情。**问题是想做这样的事情**。这不应该发生。永远不会。如果你想出一个你需要这个的案例，你显然是做错了。

注意:如果你曾经想做这样的事情，不要感到羞愧或被冒犯。这只是众多中 [的一个](http://codepen.io/magicspon/pen/rtKJh)[例子。](http://hugogiraudel.com/2013/03/18/ultimate-rem-mixin/)

## 因为我们可以

好吧，让我们一致认为，上述要求确实是一种错误的使用 Sass 的方式。你想要一个更有争议的例子吗？ [Sass 3.4 变更日志](https://github.com/sass/sass/blob/master/doc-src/SASS_CHANGELOG.md#340-unreleased)最近到处都有特色，促进了即将到来的选择器功能。这些特性意味着能够操作类似列表的选择器，以及类似`selector-nest()`、`selector-replace()`等功能。

无论我多么努力，我都没有找到选择器函数的一个合法用例。Twitter 上的一些人试图用这样的例子来说服我:

```
@mixin context($old-context, $new-context) {
 @at-root #{selector-replace(&, $old-context, $new-context)} {
 @content;
  }
}

li {
 float: left;

  ul {
 display: none;

 @include context('li', 'li:hover') {
 display: block;
    }
  }
}
```

虽然我同意它以一种聪明的方式完成工作，但我不认为它更简单。我觉得这让事情变得更复杂了。我认为它增加了不应该有的代码复杂性。

为什么不这样写呢？

```
li {
 float: left;

  ul {
 display: none;
  }

 &:hover ul {
 display: block
  }
}
```

现在**这个**很简单。这是可以理解的。我觉得有时候我们使用东西只是因为它们存在，而不是因为我们应该使用它们。也被称为*因为——我们能*综合症。

## 我们是怎么到这里的？

在某种程度上，我对此感到有点内疚。我用 Sass 做了一些疯狂的事情(例如，这里的和这里的)并展示了它们，可能没有足够的警告这些技术大多是实验性的。

我觉得这已经够明显了。当您必须编写几十行 Sass 来输出几行 CSS 时，您应该感到怀疑。兴奋和感兴趣，是的，但仍然怀疑在生产中使用它。

似乎如果你给人们太多的权力，最终他们会滥用它。更糟糕的是，他们可能想要更多的权力。这就是我们使用 CSS 预处理程序的方式。变量是不够的，所以我们得到了混合。和功能。和数组。越来越多。我们没有停下来想一想我们在做什么，为什么要这么做。

我也没有停止，直到我开始与那些对 CSS 几乎没有经验的开发人员分享代码库。过度设计和疯狂的东西不再是一个选项。我很高兴。

## 我们应该完全放弃顶嘴吗？

这不是本文的重点，尤其是因为 Sass 没有任何问题。你知道那句谚语:

> 预处理器不会输出坏代码。糟糕的开发者会。

Sass 是一个很棒的工具，当你知道如何使用和如何不使用它的时候。与一些人的想法相反，mixins 或函数绝对没有问题——即使是复杂的——只要它们不会变得太复杂。复杂不复杂。

筑巢也没什么不好，只要你控制住。就我而言，我倾向于避免嵌套，因为这会使代码更难阅读。

虽然我喜欢伪类和伪元素，但我认为当选择器相互嵌套时会很快变得混乱，就像下面的例子，摘自这篇文章中的[:](http://www.phase2technology.com/blog/everything-you-need-to-know-about-sass-3-4/)

```
.tabs {
  .tab {
 background: red;
 &:hover {
 background: white;
      .tab-link {
 color: red;
      }
    }
    .tab-link {
 color: white;
    }
  }
}
```

没有。对我来说太多了。我宁愿写:

```
.tabs .tab {
 background: red;

 &:hover {
 background: white;
  }
}

.tab-link {
 color: white;

  .tabs .tab:hover & {
 color: red;
  }
}
```

你知道吗？第一个例子有 176 个字符，而第二个例子只有 152 个字符。所以深度嵌套并不一定能节省你的角色。

## 但是很好玩！

是的，它是。我知道它是。我是用 Sass 编写了一个 JSON 解析器[的人，并且只使用 SCSS](http://hugogiraudel.com/2014/01/20/json-in-sass/) 实现了[位操作符。当然好玩！](http://hugogiraudel.com/2014/06/22/bitwise-operators-in-sass/)

过度工程不仅好玩，而且有用！在做意想不到的事情时，我在萨斯( [#1090](https://github.com/sass/sass/issues/1090) ， [#1265](https://github.com/sass/sass/issues/1265) )发现了一些小 bug。通过做一些意想不到的事情，我也变得更会顶嘴了。通过为每个项目定义 3 个变量，你不会变得更好。挑战极限会让你变得更好。

但是你需要知道在哪里停下来。你需要知道什么时候事情做得太过分了，你不再完全控制你的代码了。我花了将近两年的时间，主要是一个大规模的项目，才意识到这一点。不是所有的东西都可以在任何地方使用。实验不属于生产环境。这不仅是错误的，而且实际上非常危险。

考虑一下[我想出的这个解决关于跨媒体查询`@extend`指令限制的方法](https://www.sitepoint.com/cross-media-query-extend-sass/)。它已经在 [DoCSSa](http://docssa.info/#responsive) 被用作——我引用——一种*全程干燥*的方法。的确如此。**除了它打破了级联**。因为占位符是动态生成和扩展的，所以你的 CSS 会到处移动，这可能会导致意想不到的行为。

![Me, when trying to figure out what's going on](img/f8299a30892e491d400203aa0afb4713.png)

这项技术是一项实验。它不应该被用在一个庞大的 Sass 框架中，这个框架应该解决每个人的问题。在没有完全意识到后果的情况下，这是不应该用于生产的。然而，它正被这样使用。

## 最后的想法

继续实验！不要停止黑萨斯，因为它太棒了。只是要小心你在真实项目中所做的事情。最重要的是，保持事情简单。少即是多。

## 分享这篇文章