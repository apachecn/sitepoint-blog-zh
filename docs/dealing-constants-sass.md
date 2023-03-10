# 在 Sass 中处理常数

> 原文：<https://www.sitepoint.com/dealing-constants-sass/>

Sass 和 JavaScript 一样，没有实现常量的本地方法。但是在继续之前，让我们给那些不太了解这种编程术语的人一个快速的提示。

> 在计算机程序设计中，常量是一个标识符，它的相关值通常不能被程序在其执行期间改变(尽管在某些情况下这是可以避免的，例如使用自修改代码)。许多编程语言在常量符号和变量符号之间有明确的语法区别。
> –[维基百科](http://en.wikipedia.org/wiki/Constant_(computer_programming)

所以常数基本上是不可变的变量。然而，由于变量本身是可编辑的，我们需要一个新的名字来描述一个不是…你知道，变量的变量。于是，*常数*。

正如我所说，Sass 没有办法定义常量。如果你问我，这可能是最好的。在声明性的、样式相关的语言中，比如 CSS，不需要不可变的值是非常不可能的。但是你可能知道，我是那个和萨斯一起做疯狂事情的人，所以…

我们走吧。

## 这一切是如何开始的？

计算机编程中有一个约定，希望作者将常量名称定义为蛇形大写字符串，就像这样:`LOOK_AT_ME_I_AM_A_CONSTANT`。

所以我首先想到的是依靠命名约定来定义 Sass 中的常量。基本上，所有蛇形大写的变量都应该被认为是常量，因此永远不应该被屏蔽。

例如:

```
$MAX_Z_INDEX: 2147483647;
$PI: 3.1415926535897932384626433832795028841971693993751;
$E: 2.71828182845904523536028747135266249775724709369995;
```

如果你问我，在大多数情况下你应该就此打住。但是如果你喜欢给你的代码添加一些额外的有趣/有用的东西，你可能想继续读下去。

## 保持简单

所以我想(显然[不是唯一一个](https://twitter.com/cahnory/status/522757446067306496))把所有的常数都收集到一个`$CONSTANTS`图中。这样，即使对于没有经验的开发人员来说，也应该很清楚这些值不应该被编辑。

```
/// Constants map
/// @type Map
/// @prop {Number} MAX_Z_INDEX - 2147483647
/// @prop {Number} PI - 3.1415926535897932384626433832795028841971693993751
/// @prop {Number} E - 2.71828182845904523536028747135266249775724709369995

$CONSTANTS: (
  'MAX_Z_INDEX': 2147483647,
  'PI': 3.1415926535897932384626433832795028841971693993751,
  'E': 2.71828182845904523536028747135266249775724709369995
);
```

虽然，由于在这个图中用`map-get()`取值不是很方便，我们还是构建一个小函数来获取它们，叫做`const()`。

```
/// Constant getter
/// @param {String} $name - Name of constant to get
/// @return {*} Constant value
/// @require $CONSTANTS
/// @throw 'Unknown constant `#{$name}`.'

@function const($name) {
 @if not map-has-key($CONSTANTS, $name) {
 @error 'Unknown constant `#{$name}`.';
  }

 @return map-get($CONSTANTS, $name);
}
```

这个函数再简单不过了:它只需要一个常量名称。如果不存在于`$CONSTANTS`地图中，则抛出错误。如果您觉得返回`null`(这是 [`map-get`](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_get-instance_method) 在一个键不存在的情况下所做的)是没问题的，请随意去掉这个语句。就我而言，我觉得如果你试图获取一个未知的常数，应该会失败。

然后，你可以这样使用它:

```
.test {
 z-index: const('MAX_Z_INDEX');
}
```

它看起来不错，不是吗？我喜欢从函数的名字就可以看出 z-index 的值是一个常数，而不是一个神奇的数字。

## 把事情推进一步

您可能已经注意到，我们当前的版本只处理读取常量，而不是创建它们。如果你想添加一个新的常量，你必须手动将它写到`$CONSTANTS`图中。

那么，如果我们有一种动态设置常数的方法会怎么样呢？这也允许我们在试图覆盖一个现有的常量时抛出一个错误。这就是我们在最后一节要做的事情。

这个想法也很简单，不用担心:

*   构建一个`const` mixin 来设置一个常量；
*   构建一个`const`函数来获得一个常量。

我们将保留我们的`$CONSTANTS`地图想法，但是我们将它初始化为一个空地图。然后`const` mixin 会在其中放置新的常量。

```
$CONSTANTS: () !global;
```

让我们开始我们的`const` mixin。这应该很容易理解:如果我们试图创造的常数已经存在，我们就会爆炸。这才是重点。如果没有，我们会将其添加到地图中。

```
/// Constant setter
/// @param   {String} $name  - Name of constant to get
/// @param   {*}      $value - Constant value
/// @return  {*}             - Constant value
/// @require $CONSTANTS
/// @throw   'Constant `#{$name}` already defined.'
/// @output  Nothing

@mixin const($name, $value) {
 @if map-has-key($CONSTANTS, $name) {
 @error 'Constant `#{$name}` already defined.';
  }

 $CONSTANTS: map-merge($CONSTANTS, ($name: $value)) !global;
}
```

我们的`const`函数在长度上应该非常相似:

```
/// Constant getter
/// @param   {String} $name - Name of constant to get
/// @return  {*}            - Constant value
/// @require $CONSTANTS
/// @throw   'Unknown constant `#{$name}`.'

@function const($name) {
 @if not map-has-key($CONSTANTS, $name) {
 @error 'Unknown constant `#{$name}`.';
  }

 @return map-get($CONSTANTS, $name);
}
```

因此，我们有“getter”和“setter”。我们现在准备使用我们的小系统:

```
// Initializing new constants

@include const('MAX_Z_INDEX', 2147483647);
@include const('PI', 3.1415926535897932384626433832795028841971693993751);
@include const('E', 2.71828182845904523536028747135266249775724709369995);

// Using a constant;
// same API as before

.test {
 z-index: const('MAX_Z_INDEX');
}
```

## 最后的想法

所以你有三个解决方案:

1.  以特定的方式命名常量，以区别于变量。
2.  使用一个常量映射来存储它们，并构建一个小函数来获取它们。
3.  构建“getters”和“setters”来拥有一个简单而强大的系统。

就我而言，我可能会选择第一个选项。蛇形大写变量。但是，只要能让你开心就好，对吗？您可以随意使用 SassMeister 上的代码。

在 SassMeister 上玩[简单版。然后继续前进，并检查了](http://sassmeister.com/gist/69daef4a1d5426c80812)[完整版](http://sassmeister.com/gist/324e5d32934df4cef69d)，也在萨斯梅斯特。

## 分享这篇文章