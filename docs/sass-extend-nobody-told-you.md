# 没人告诉你萨斯的“延伸”

> 原文：<https://www.sitepoint.com/sass-extend-nobody-told-you/>

Sass 提供了许多强大的特性来编写一致且健壮的 CSS。其中最强大也最棘手的一个必须是`@extend`。虽然大多数 Sass 用户都了解`@extend`的基本知识，但我觉得它还有一些不为人知的模糊部分。

但首先，让我们从基础开始。

## `@extend`的基础知识

Sass `@extend`指令提供了一种简单的方法，允许选择器继承另一个选择器的样式。这在基于组件的 CSS 架构中特别有用，允许我们通过扩展组件来轻松地对其进行修改。

许多人可能已经知道如何扩展选择器，但是对于那些不知道的人，这里是 Sass 中选择器扩展的最基本的情况:

```
.message {
  padding: .5em;
}

.message-error {
  @extend .message;
}
```

产生以下 CSS:

```
.message, .message-error { 
  padding: .5em;
}
```

很简单，对吧？当然你不局限于扩展类；您可以扩展标签选择器(例如`a`)、id(例如`#id`)——基本上是任何有效的 CSS 选择器。并且你可以扩展 Sass 占位符(例如`%placeholder`)，这些占位符都是为此而专门制作的。

*注:上一篇文章， [Sass: Mixin 还是占位符？](https://www.sitepoint.com/sass-mixin-placeholder/)，我更详细地讨论了占位符的话题。你也可以在官方 Sass 文档上查看[占位符。](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#placeholders)*

## 扩展更深的选择器

在大多数情况下，您将扩展非常简单的选择器(主要是类)，但是没有什么可以阻止您扩展更复杂的选择器，比如`.class element:pseudo`或者甚至:

```
.message + .message {
  margin-bottom: .5em;
}

.message-error {
  @extend .message;
}
```

它将输出:

```
.message + .message, 
.message-error + .message-error, 
.message + .message-error, 
.message-error + .message { 
  margin-bottom: .5em;
}
```

结果现在稍微复杂一点，但是如果你理解`@extend`，输出应该不会让你太惊讶。

## 多重延伸

您可能知道您可以在同一个规则中扩展多个选择器，但是您知道您可以用一个`@extend`指令来这样做吗？

```
.message {
  padding: .5em;
}

.important {
  font-weight: bold;
}

.message-error {
  @extend .message, .important;
}
```

输出:

```
.message, .message-error {
  padding: .5em;
}

.important, .message-error {
  font-weight: bold;
}
```

如果你问我，我不喜欢这种方法。虽然它使代码变得更短，但我发现它更难阅读。为每个扩展的选择器使用不同的指令，可以使眼睛更容易发现选择器以及有多少选择器被扩展。

您可以像这样解决语法问题:

```
.message-error {
  @extend
    .message,
    .important;
}
```

但是这又有多少可读性呢？

我将继续每行使用一个`@extend`指令，但是结果是一样的，所以请随意选择您最喜欢的语法。

### 链接扩展

正如刚才讨论的，一个选择器可以`@extend`来自多个源。但是您也可以链接您的`@extend`指令:

```
.message {
  padding: .5em;
}

.message-important {
  @extend .message;
  font-weight: bold;
}

.message-error {
  @extend .message-important;
}
```

输出是:

```
.message, .message-important, .message-error {
  padding: .5em;
}

.message-important, message-error {
  font-weight: bold;
}
```

虽然上面的例子是正确的，但我建议不要这样做，因为它可能会产生不良影响。让我们现在就讨论这些。

## 大规模扩展

人们反对 CSS 预处理程序的一个主要理由可能是:*“看看输出，太可怕了！”*在某种程度上，他们是对的。Sass `@extend`指令非常强大，它可以导致[异常大的扩展](http://pastebin.com/Jy9PqFTy)。

这就是为什么在扩展一个选择器时你应该总是小心的原因，因为它将扩展选择器的所有出现的*——这会很快变得混乱。考虑下面的例子:*

```
.important {
  font-weight: bold;
}

.sidebar .important {
  color: red;
}

.message {
  @extend .important;
}
```

这将输出:

```
.important, .message {
  font-weight: bold;
}

.sidebar .important, .sidebar .message {
  color: red;
}
```

正如你所看到的，不仅`.message`继承了`.important`，而且`.message`也继承了在后代选择器中使用`.important`的实例。虽然这可能是您所期望的，但并不总是这样，所以您应该小心使用`@extend`指令。要么确保您要扩展的选择器只存在于 CSS 中的一个地方，要么扩展一个占位符——这就是它们的用途。

所以请记住， [Sass 不会产生糟糕的代码，糟糕的程序员会产生](http://thesassway.com/editorial/sass-doesnt-create-bad-code-bad-coders-do)。

## 保持源顺序

Sass 中`@extend`的一个鲜为人知的特性是它处理源代码顺序的方式。让我们来看看下面的代码片段:

```
.half-red {
  color: rgba(red, .5);
}

.message-error {
  color: red;
  @extend .half-red;
}
```

抛开抽象的类名不谈，这是非常好的代码，对吗？当它编译时，您可能会看到如下内容:

```
/* This will not be the correct output! */
.message-error {
  color: red;
  color: rgba(255, 0, 0, 0.5);
}
```

这看起来像是为不支持`rgba()`颜色值的旧浏览器提供优雅降级的一种简单方法(我们在这里不会详述使用`@extend`的原因；没关系)。但是这不是 CSS 输出的样子。相反，它看起来像这样:

```
.half-red, .message-error {
  color: rgba(255, 0, 0, 0.5);
}

.message-error {
  color: red;
}
```

*但是为什么呢？！*你可能会不高兴地说。这是因为`@extend`指令的工作方式与您预期的相反。来自 [Sass 文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#extend):

> @extend 的工作方式是在样式表中出现扩展选择器[…]的任何地方插入扩展选择器[…]。

在我们的例子中，“扩展选择器”是`.message-error`而“扩展选择器”是`.half-red`。

## 可选延伸

根据您正在开发的应用程序的类型，您可能使用也可能不使用第三方框架或基于 CSS 的小部件。如果其中一些是用 Sass 编写的，那么您可以从这些文件中扩展选择器。

不幸的是，如果扩展选择器碰巧丢失，Sass 将抛出一个错误，编译将失败。

> ".消息-错误“无法@extend”。重要”。
> 选择者”。找不到重要的。
> 使用“@extend。重要！可选"如果扩展应该能够失败。

如您所见，您可以向导入传递一个`!optional`标志，以防止它们在没有找到扩展选择器时失败。这在扩展和扩展选择器冲突的情况下也很有用:

```
a.important {
  font-weight: bold;
}

p.message-error {
  @extend .important;
}
```

实际上，这抛出了一个错误，因为两个选择器都被指定了，使得它们不可能统一。因此，您将得到以下错误:

> 没有匹配的选择器。“重要”可以与“p.message-error”统一。

给你的`@extend`添加`!optional`标志可以解决这个问题。

## 扩展和媒体查询

`@extend`最大的问题之一是它缺乏对从`@media`指令内部扩展的支持。不幸的是，Sass 不允许跨媒体扩展:

```
.important {
  font-weight: bold;
}

@media (max-width: 767px) {
  .message-error {
    @extend .important;
  }
}
```

这将导致以下错误:

> 您不能从@media 内部@扩展外部选择器。在同一个指令中只能@extend 选择器。

这是因为`@extend`基本上是关于移动选择器，而不是 CSS 规则，正如我们在保持源代码顺序一节中看到的。如果 Sass 允许这样做，那么扩展另一个媒体查询中的选择器将会产生如下结果:

```
.rule, @media(max-width: 767px) { .another-rule }, .another-another-rule {
  /* ... */
}
```

…这显然不是有效的 CSS。

话虽如此，Sass 的开发者们很清楚这个问题(从他们的回购协议中提到这个问题的惊人数量可以看出: [#501](https://github.com/nex3/sass/issues/501) 、 [#640](https://github.com/nex3/sass/issues/640) 、 [#915](https://github.com/nex3/sass/issues/915) 、 [#1050](https://github.com/nex3/sass/issues/1050) 、 [#1083](https://github.com/nex3/sass/issues/1083) )。

由于这是高级 Sass 架构的一个主要缺点，他们很可能很快就会实现这个问题的解决方案。根据来自 Nex3 (Sass 首席开发者)的[这个评论](https://github.com/nex3/sass/issues/640#issuecomment-16537355)，将会是 *mixin 插值*。

## `@extend`的最佳实践

总而言之，当在 Sass 中使用`@extend`指令时，这里有一些我称之为*的最佳实践*:

*   确保扩展选择器在样式表中只出现一次。
*   避免从嵌套选择器扩展。
*   避免链接`@extend`指令。
*   不要尝试从媒体查询中扩展；它不起作用。

关于`@extend`的讨论到此结束。如果你有什么要补充的，请在评论中告诉我！

这篇文章由 Pierre Choffé为《T2 瀑布》翻译成法语

## 分享这篇文章