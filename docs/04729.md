# Sass 多个参数、列表或 Arglist

> 原文：<https://www.sitepoint.com/sass-multiple-arguments-lists-or-arglist/>

我做了很多 Sass 代码审查。同样，我喜欢阅读框架的代码。不用说，我见过很多事情，好的坏的都有，说到 Sass。

如果说有什么东西总是让我在看别人的 Sass 时咬牙切齿，那就是看到 mixins 和函数用列表代替变量参数，或者用列表代替多个命名参数。

在这篇文章中，我想看看这个问题，并希望能更清楚地说明何时使用 what。

## 术语

在继续之前，让我们快速浏览一下术语。函数和 mixins 都能够接受从 0 到无穷大的参数。这意味着你可以创建一个无参数的函数/mixin 或者一个有多个参数的函数。现在让我们更深入。

在 Sass 中，`list`相当于其他语言中的数组。碰巧将`list`作为类型的值(用 [`type-of`](http://sass-lang.com/documentation/Sass/Script/Functions.html#type_of-instance_method) 函数检查)是几个具有特定类型的值的集合(`number`、`string`、`bool`、`color`、`list`、`map`或`null`)。为了更深入地理解 Sass 列表，我建议你阅读我的关于这个的文章。

最后但同样重要的是，还有一个[变量参数](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variable_arguments)。您可能不知道`arglist`是一个真正的 Sass 数据类型(文档中没有提到它)。事实是，你不能轻易地(故意地)创建一个`arglist`，因为它通常是 Sass 的一个内部机制，当你在 mixin/function 签名中使用变量参数符号时就会发生这种情况。

例如:

```
@mixin shadows($shadows...) {
 box-shadow: $shadows;
}
```

在这个例子中，`$shadows`是一个`arglist`。通过在签名中使用`...`，我们可以传递任意数量的参数，从无到有。它与常规列表具有完全相同的特征，除了它总是用逗号分隔，因为几个参数都用逗号分隔。

既然我们已经对术语有所了解，那么是时候决定什么时候使用什么了。让我们从简单的开始。

## 可变参数

如果您不知道有多少个参数将被传递给函数/mixin，因为可能没有或有宇宙中的星星那么多，这意味着您将处理未知数量的参数，也称为`arglist`。

例如，让我们考虑线性梯度混音:

```
@mixin linear-gradient($direction, $gradients...) {
 background-color: nth($gradients, 1);
 background-image: linear-gradient($direction, $gradients...);
}

.selector {
 @include linear-gradient(to right, magenta, red, orange, yellow, green, blue, purple);
}
```

这是可变参数的最佳情况，因为可以有任意数量的色标传递给 mixin。不要只使用一个列表来循环和创建一个逗号分隔的列表…这没有意义。

## 多个参数

现在让我们继续讨论多重论点。在处理不相关的参数时，经常使用多个不同的命名参数。坚持我们的渐变混合，我们不会在渐变列表中打包方向。它服务于一个完全不同的目的，因此应该站在自己的论点。

在大多数情况下，您将使用多个参数，而不是一个`arglist`的一个`list`。它是常见的用例，一个函数/mixin 需要`a`、`b`和`c`才能工作，所以你让它接受三个参数:`$a`、`$b`和`$c`。如果这些参数中的一些有默认值，您可以在 mixin 签名中添加这个默认值，例如`$a`、`$b`、`$c: "default"`。

很少有人知道的一件事是，在一个函数/mixin 签名中设置多个参数并不一定意味着必须一个接一个地传递它们。让我们把梯度混合放在一边，考虑一分钟虚拟函数:

```
@function dummy($a, $b, $c: "default") {
  // do stuff
}
```

现在，假设你有一个名为`$parameters`的列表，其中有 3 个参数。

```
$parameters: (true, 42, 'kittens');
```

有两种方法可以根据参数列表调用函数。要么像这样，这是不雅的:

```
$value: dummy(nth($parameters, 1), nth($parameters, 2), nth($parameters, 3));
```

或者像这样，指示 Sass 将`$parameters`作为变量参数传递:

```
$value: dummy($parameters...);
```

虽然第二个版本非常方便，但它需要正确排序`$parameters`。为了解决这个问题，自 Sass 3.3 以来，还有第三种方法，即使用地图:

```
$parameters: (
  'c': 'kittens',
  'a': true,
  'b': 42
);

$value: dummy($parameters...);
```

通过使映射键与参数名匹配，您可以通过向它追加`...`来动态地将映射转换为变量参数。Sass 将按照正确的顺序正确地获取值。

回到我们最初的话题。使用多个不同的参数比要求用户传递一个列表要强大得多，因为您给了他们选择的余地:要么他们可以一个接一个地传递参数，要么一次传递一个列表或一个映射。

所以下次当你想用一个带有函数/mixin 签名的列表替换几个参数时，请三思。

## 名单

据我所知，mixin 或函数需要一个列表是很不寻常的。要么使用多个参数，因为它们都是不相关的，要么传递一个`arglist`，因为函数/mixin 需要接受任意数量的相关参数。

列表通常用作内部工具，在一个唯一变量中存储多个值。然而，一些函数/mixin 可能需要一个列表，因为它们实际上在这个列表上做一些操作。例如，测试一个值是否包含在一个列表中的函数显然需要一个列表才能工作。没有命名的参数，因为我们不知道数目。不是可变参数，因为它是单个实体。

```
@function contains(/* List */ $haystack, /* Any */ $needle) {
 @return not not index($haystack, $needle);
}
```

请允许我稍微漂移一下。当前版本的波旁威士忌为[提供了一种调酒来帮助调整成分](http://bourbon.io/docs/#size)。这个想法很简单:你给它传递一个值，它把它分配给`width`和`height`你给它传递两个值，它给`width`第一个值，给`height`第二个值。

现在，这很容易做到，就像这样:

```
/// Helper mixin to size elements
/// @param {Number} $width - Width
/// @param {Number} $height ($width) - Height
@mixin size($width, $height: $width) {
 width: $width;
 height: $height;
}
```

如果`$height`没有被定义，它应该回到它的缺省值，这个缺省值恰好是`$width`的值。既优雅又简洁。现在，我不太清楚为什么波旁决定反其道而行之，要求一份名单。

```
/// Helper mixin to size elements
/// @param {List} $size - Width and possibly height (but not mandatory)
@mixin size($size) {
 $height: nth($size, 1);
 $width: $height;

 @if length($size) > 1 {
 $height: nth($size, 2);
  }

 width: $width;
 height: $height;
}
```

我是说，为什么？在这种情况下，使用`list`是完全没有意义的。这两个值是不相关的，所以它们不应该打包在同一个变量中。此外，Sass 提供了一种内置的方式来直接从签名中设置默认值。

这个版本的唯一好处是在调用 mixin:

```
// Standard mixin
.selector {
 @include size(13px, 37px);
}

// Bourbon mixin
.selector {
 @include size(13px 37px);
}
```

如果你问我，我觉得这绝对不值得。

## TL；博士；医生

如果你期望无限数量的参数，不要使用`list`，使用`arglist`。这种数据类型就是为此目的而设计的。

如果你期望有限数量的参数，不要使用`list`，使用多个命名参数。这将允许您使用列表或地图一个接一个地或一次全部传递它们。

如果你希望 mixin/function 有一个列表，使用一个`list`。你可以使用一个`arglist`，但是它没有多大意义。

## 分享这篇文章