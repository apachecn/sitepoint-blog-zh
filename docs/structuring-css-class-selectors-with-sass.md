# 用 Sass 构造 CSS 类选择器

> 原文：<https://www.sitepoint.com/structuring-css-class-selectors-with-sass/>

CSS 命名约定是军团。你可能已经知道 [BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/) 和 [SMACSS](https://smacss.com/) (后者也不仅仅是命名约定)。还有 [OOCSS](http://oocss.org/) ，这更像是一个完整的方法论。它们都非常依赖 CSS 类选择器，因为它们是高度可重用的。

使用 Sass 可以*帮助*以更加模块化的方式编写这些选择器。通过选择器嵌套和混合，我们可以想出~~花哨~~疯狂的解决方案来构建想要的 API。在本文中，我将(重新)介绍其中的一些方法，列出我认为每种方法的优缺点。

## 本机选择器嵌套

在 Sass 中，有一种方法可以嵌套选择器，以便不必重复原始的块名，这是一个长期以来一直被询问的特性。在 Sass 的 3.3 版本中，终于引入了这个特性。首先在测试版中用了一个非常奇怪的语法，后来当稳定版上线时，用一个更好的语法替换了 T2 的语法。Natalie Weizenbaum 在[的这篇文章](http://nex3.roughdraft.io/8050187-)中解释了这一变化背后的原因。

基本上，引用选择器(`&`)可以用作子类名称的一部分，以便从文档根级别的第一个创建另一个类名(意味着这里不需要`@at-root`)。

```
.foo {
  // Styles for `.foo`

  &-bar {
    // Styles for `.foo-bar`
  }
}
```

这个特性很快被过度利用来编写 BEM 选择器，例如[非常流行的媒体对象](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/):

```
.media {
  // Styles for `.media` block

  &__img {
    // Styles for `.media__image` element

    &--full {
      // Styles for `.media__image--full` modified element
    }
  }

  &--new {
    // Styles for `.media--new` midifier
  }
}
```

前面的代码将被编译为:

```
.media {}
.media__img {}
.media__img--full {}
.media--new {}
```

### 赞成的意见

*   它依赖于一个本地特性，不需要额外的帮助，比如变量或混合。
*   一旦你知道引用选择器(`&`)是如何工作的，总体来说很容易掌握。

### 骗局

*   它公开了`&`语法，对于不熟悉 Sass 的开发人员来说，这可能有点令人困惑，甚至有点吓人。
*   嵌套通常不在根打印，除非使用了`@at-root`，这可能会令人不安。

## BEM 混合

因为在 Sass 3.3 ( `@at-root #{&}__element`)的测试版中，类生成的语法是如此丑陋，所以我们很快发现，到处都出现了一些 mixins 来隐藏痛苦并提供一个更友好的 API。

```
@mixin element($element) {
  &__#{$element} {
 @content;
  }
}

@mixin modifier($modifier) {
  &--#{$modifier} {
 @content;
  }
}
```

你可以这样使用它们:

```
.media {
  // Styles for `.media` block

 @include element("image") {
    // Styles for `.media__image` element

 @include modifier("full") {
      // Styles for `.media__image--full` modified element
    }
  }

 @include modifier("new") {
    // Styles for `.media--new` midifier
  }
}
```

我们也可以用同样的方式创建一个`block` mixin，但是这并没有多大帮助，因为块只是一个类名。让我们保持简单。虽然，对于一些人来说`modifier`和`element`似乎太长了，所以我们看到了一些`e`和`m`盛开。

```
.media {
  // Styles for `.media` block

 @include e("image") {
    // Styles for `.media__image` element

 @include m("full") {
      // Styles for `.media__image--full` modified element
    }
  }

 @include m("new") {
    // Styles for `.media--new` midifier
  }
}
```

### 赞成的意见

*   这个版本提供了一个友好的 API，一旦你知道 BEM 是什么以及它是如何工作的，就很容易理解。

### 骗局

*   逻辑隐藏在 mixins 后面，除非您明确知道发生了什么，否则新的选择器和类的生成并不明显。
*   单字母 mixin 可能不是一个好主意，因为它们很难理解 mixin 的目的。`b`和`m`可能意味着很多事情。

## 人源化 BEM 混合蛋白

最近，我读到了安德斯·施密特·汉森的一种新的类似边界元法的方法。这个想法是将块元素修饰语的行话隐藏在一个普通的词汇表后面，大声读出来就有意义。

```
@mixin new($block) {
 @at-root .#{$block} {
 @content;
  }
}

@mixin has($element) {
  &__#{$element} {
 @content;
  }
}

@mixin when($modifier) {
  &--#{$modifier} {
 @content;
  }
}
```

在这种情况下，重点是将代码隐藏在精心命名的 mixin 后面，这样看起来代码在讲述一个故事，所以`new` mixin 实际上是有用的。

```
@include new("media") {
  // Styles for `.media` block

 @include has("image") {
    // Styles for `.media__image` element

 @include when("full") {
      // Styles for `.media__image--full` modified element
    }
  }

 @include when("new") {
    // Styles for `.media--new` midifier
  }
}
```

安德斯在`is(..)`和`holds(..)`混合上更进一步。整个想法让我想起了我的 [`when-inside(..)` mixin](https://css-tricks.com/snippets/sass/simplifying-contexts-events/) ，在基于上层上下文设计元素时，将`&`隐藏在对人友好的 mixin 后面。

```
@mixin when-inside($selector) {
  #{$selector} & {
 @content;
  }
}

img {
 display: block;

 @include when-inside(".media-inline") {
 display: inline;
  }
}
```

### 赞成的意见

*   这种方法有助于提高代码的可读性，就像我们开始用前导`is-`命名有状态类一样(由 SMACSS 普及)。
*   仍然坚持特定的方法(在本例中是 BEM)，但是对开发人员来说更加友好。

### 骗局

*   更多的混合，更多的助手，更多的东西来学习，以获得更稳定的学习曲线。每个人都不喜欢处理大量的 mixins 来编写简单的东西，比如 CSS 选择器。
*   对某些人来说，这可能太抽象了；不是每个人都喜欢像阅读英语一样阅读代码。看情况。

## 最后的想法

请记住，使用这些技术中的任何一种都会阻止选择器代码库的可搜索性，因为选择器实际上并不存在，直到它们被 Sass 生成。在选择器之前添加注释可以解决这个问题，但是为什么不首先直接编写选择器呢？

> 不要使用新的 Sass 特性来混淆所有的选择器(。b { &__e { } })如果您关心拥有一个可搜索的代码库。
> 
> —凯丽格(@凯丽格)[2014 年 3 月 12 日](https://twitter.com/kaelig/status/443758841331998721)