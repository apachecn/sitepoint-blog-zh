# 改进选择器和媒体查询的 PostCSS 指南

> 原文：<https://www.sitepoint.com/the-postcss-guide-to-improving-selectors-and-media-queries/>

CSS 规范最近的变化引入了一些有趣的特性。不幸的是，它们中的一些仍然处于草案状态，那些没有的缺乏广泛的浏览器支持。像往常一样，浏览器审查、接受和实施新建议需要一段时间。然而，我们可以省去长时间的等待，尝试一下已经使用 PostCSS 的一些特性。

PostCSS 有各种插件，旨在为最新的 CSS 特性实现 polyfills。由于这些插件的范围很广，很难在一篇文章中涵盖所有的插件。相反，我们将把目光缩小到一系列插件上，这些插件致力于为选择器和媒体查询增加新的功能。它们中的许多将允许我们显著地改进我们的样式表的结构，而其他的可能只是在顶部添加一些甜蜜的语法糖。

在本文中，我们不会深入讨论配置和安装 PostCSS 的细节。这已经在“[postscs 简介](https://www.sitepoint.com/an-introduction-to-postcss/)”和“[用 postscs 提高你的 CSS 质量](https://www.sitepoint.com/improving-the-quality-of-your-css-with-postcss/)”中有所涉及。你也可以查看 GitHub 上的 [PostCSS](https://github.com/postcss/postcss) 页面，获得快速参考。

## 规则嵌套

让我们从最基本的特性开始，也是每个预处理器用户都熟悉的特性——嵌套。 [postcss-nesting](https://github.com/jonathantneal/postcss-nesting) 插件根据 [W3C 嵌套模块提案](http://tabatkins.github.io/specs/css-nesting/)实现样式规则嵌套。

该提案引入了一个新的`&`嵌套选择器，它引用了父选择器。与 Less 或 Sass 相反，根据规范，这个选择器是强制性的，并且必须是选择器链中第一个基本选择器，才能启用嵌套。任何不包含嵌套选择器的选择器都将被忽略。例如:

```
.article {
  color: #333;

  &.popular {
    background: #DDD;
  }

  & .title {
    font-weight: bold;
  }
} 
```

将被翻译成:

```
.article {
  color: #333
}
.article.popular {
  background: #DDD
}
.article .title {
  font-weight: bold
} 
```

注意下面的代码是无效的，因为它没有使用指定的`&`选择器。

```
.article {
  color: #333;

  /* no nested-selector */
  .popular {
    background: #DDD;
  }

  /* the nested selector is not the fist selector in the chain */
  .latest & {
    border: 1px solid red;
  }
} 
```

为了允许将父选择器插入到选择器中的任何位置(而不仅仅是开头)，该提案使用嵌套 at-rule `@nest`定义了一种替代语法。我们可以用下面的方法来修正前面例子中的`.latest &`选择器:

```
.article {
  color: #333;

  @nest .latest & {
    border: 1px solid red;
  }
} 
```

这将编译成:

```
.article {
  color: #333
}
.latest .article {
  border: 1px solid red
} 
```

`@nest`语法也比仅仅`&`更有表现力。

## 自定义选择器

写 CSS 的时候，我们倾向于写很多重复的选择器。这可能是选择所有链接或任何按钮的简单样板代码，或者是需要反复重复的更复杂的选择器。这可能会引入大量的代码重复以及所有与代码维护相关的问题。新的 [CSS 扩展](https://drafts.csswg.org/css-extensions/#custom-selectors)规范引入了一种在变量中存储选择器并从样式表的其他部分引用它们的方法。因此，重复选择器只能定义一次，并且可以在其他地方安全地重用。

PostCSS 有一个 [postcss-custom-selectors](https://github.com/postcss/postcss-custom-selectors) 插件实现了这个特性。下面是一个简单的例子，它为所有标题元素定义了一个选择器:

```
@custom-selector :--heading h1, h2, h3, h4, h5, h6;

:--heading {
  font-weight: bold;
} 
```

它将编译成:

```
h1,
h2,
h3,
h4,
h5,
h6 {
  font-weight: bold;
} 
```

自定义选择器被实现为伪类，因此看起来很奇怪的`:--`语法。

自定义选择器可以有效地与基本选择器一起使用。例如:

```
.article :--heading .author {
  color: blue;
} 
```

被编译成:

```
.article h1 .author,
.article h2 .author,
.article h3 .author,
.article h4 .author,
.article h5 .author,
.article h6 .author {
  color: blue;
} 
```

您甚至可以在单个选择器中组合多个自定义选择器，以实现更复杂的排列。

```
@custom-selector :--links a, a:focus, a:visited, a:hover, a:active;
article :--heading :--links {
  color: #333;
} 
```

将导致:

```
article h1 a,
article h2 a,
article h3 a,
article h4 a,
article h5 a,
article h6 a,
article h1 a:focus,
article h2 a:focus,
article h3 a:focus,
article h4 a:focus,
article h5 a:focus,
article h6 a:focus,
article h1 a:visited,
article h2 a:visited,
article h3 a:visited,
article h4 a:visited,
article h5 a:visited,
article h6 a:visited,
article h1 a:hover,
article h2 a:hover,
article h3 a:hover,
article h4 a:hover,
article h5 a:hover,
article h6 a:hover,
article h1 a:active,
article h2 a:active,
article h3 a:active,
article h4 a:active,
article h5 a:active,
article h6 a:active {
  color: #333;
} 
```

这个例子可能有点夸张，但是它很好地展示了这个特性的强大之处。

## 新伪类

[选择器第 4 级](https://drafts.csswg.org/selectors-4)规范引入了一大堆新的伪类，但是由于大多数伪类的动态特性，只有几个可以作为 PostCSS 插件使用。

### `:matches()`伪类

[postcss-selector-matches](https://github.com/postcss/postcss-selector-matches) 插件实现了新的`:matches()`伪类。它是一个功能类，只过滤与参数中的选择器匹配的元素。如果您传递多个选择器，那么元素必须至少匹配其中一个。简而言之:

```
button:matches(:hover, :focus) {
  color: red;
} 
```

被编译成:

```
button:hover, button:focus {
  color: red;
} 
```

### `:not()`伪类

`:not()`伪类过滤不匹配任何给定参数的元素。它是在 [postcss-selector-not](https://github.com/postcss/postcss-selector-not) 插件中实现的。

```
section:not(:first-child, :last-child) {
  background: white;
} 
```

结果是:

```
section:not(:first-child):not(:last-child) {
  background: white;
} 
```

### `:any-link`伪类

[postcss-pseudo-class-any-link](https://github.com/jonathantneal/postcss-pseudo-class-any-link)插件实现了`:any-link`伪类。引入它是为了帮助解决围绕`:link`伪类的混乱。与后者不同，它匹配所有链接——包括访问过的链接。

```
a:any-link {
  color: blueviolet;
} 
```

被编译成:

```
a:link,a:visited {
  color: blueviolet;
} 
```

## 媒体查询的改进

PostCSS 的武库中有几个插件，旨在使媒体查询更容易使用。它们是[邮政 CSS-定制-媒体](https://github.com/postcss/postcss-custom-media)和[邮政 CSS-媒体-最小最大](https://github.com/postcss/postcss-media-minmax)。

### 自定义媒体

编写媒体查询会产生与常规选择器相同的问题——它们在样式表中频繁重复。可能比简单的选择器更频繁。幸运的是，有一个类似于自定义选择器的解决方案。 [postcss-custom-media](https://github.com/postcss/postcss-custom-media) 插件实现了[定制媒体查询](https://drafts.csswg.org/mediaqueries/#custom-mq)规范，该规范增加了将媒体查询保存到变量中的能力。

语法非常类似于自定义选择器。这里有一个简单的例子:

```
@custom-media --medium-viewport (min-width: 768px) and (max-width: 992px);

@media (--medium-viewport) {
  /* your styles here */
} 
```

被编译成:

```
@media (min-width: 768px) and (max-width: 992px) {
  /* your styles here */
} 
```

当然，您可以同时使用多个自定义介质查询:

```
@custom-media --landscape (orientation: landscape);
@media (--medium-viewport) and (--landscape) {
  /* your styles here */
} 
```

将导致:

```
@media (min-width: 768px) and (max-width: 992px) and (orientation: landscape) {
  /* your styles here */
} 
```

如您所见，现在更改“中等视窗”的定义以及查找任何相关的 CSS 代码变得容易多了。

### 最小和最大语法

虽然媒体查询是一件很棒的事情，但是`min-`和`max-`语法受到了来自社区的很多憎恨。W3C 通过使用比较操作符引入了更直观的语法作为回应。 [postcss-media-minmax](https://github.com/postcss/postcss-media-minmax) 插件增加了对`>`、`>=`、`<`和`<=`运算符的支持。它还允许我们使用中间值语法，即`min-value < property < max-value`。

我们可以使用新的语法简化前面的例子，这将产生相同的结果。

```
@custom-media --medium-viewport (768px <= width <= 992px);

@media (--medium-viewport) {
  /* your styles here */
} 
```

当然，插件也可以不使用自定义媒体。

## 不要停在这里

不幸的是，由于它们的动态特性和对 DOM 的依赖性，没有多少新的 CSS 特性可以作为 PostCSS 插件来实现。我们需要等待大部分的本地支持。但是在你的工作流程中仍然有大量的插件需要探索和测试。这篇文章关注的是与选择器和媒体查询相关的插件，但是我们也希望带给您其他新奇事物的概述。如果你感到好奇和不耐烦，你可以在 [postcss.parts](http://postcss.parts/) 找到更多。继续探索！

## 分享这篇文章