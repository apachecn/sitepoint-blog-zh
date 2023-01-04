# 帮助你提高谈吐的技巧

> 原文：<https://www.sitepoint.com/tips-help-level-up-sass/>

写代码很难。有许多事情需要注意，许多陷阱需要避免，并且总有改进的空间。使用 Sass 使得编写 CSS 不那么困难。除非你做错了，在这种情况下，它会使 CSS 变得更糟。

为了避免这种情况，我想分享一些技巧来帮助你写更好的 Sass。

## 使用`index()`而不是多个相等的赋值

我喜欢回顾 Sass 代码。我可以花几个小时在 GitHub 仓库上挖掘 Sass 代码。我经常看到的，尤其是在 Sass 框架中，是多重相等赋值的使用。

假设您想要检查一个值是`initial`、`inherit`还是`auto`。通常的写法是这样的:

```
@if $value == "initial" or $value == "inherit" or $value == "auto" {
  // Then do something
}
```

虽然这样做很好，但不仅难看，而且写起来也很长。如果我们简单地使用`index()`函数会怎么样？这个功能…

> 返回一个值在列表中的位置。如果找不到该值，则返回`null`。
> [萨斯参考](http://sass-lang.com/documentation/Sass/Script/Functions.html#index-instance_method)

让我们用这个闪亮的工具来更新我们的示例代码:

```
@if index("initial" "inherit" "auto", $value) {
  // Then do something
}
```

有些人会认为`index()`返回一个数字，而不是一个[布尔值](https://www.sitepoint.com/boolean-data-type/)，因此不应该这样使用。这真的是一个选择的问题。我认为这完全没问题，因为我们只想确保它不返回`null`(这将是 falsy)，但如果你真的想处理一个布尔值，你仍然可以这样做:

```
@if not not index("initial" "inherit" "auto", $value) {
  // Then do something
}
```

您可能知道如何在 JavaScript: `!!value`中将值转换为布尔值。这是完全一样的东西，但是用了`not`。在表达式前添加`not`会将其转换为布尔值，但会反转其值。当添加另一个`not`时，它仍然是一个布尔值，但再次反转该值，因此它保持原来的含义。

在我们的例子中，如果它是 3 个值中的任何一个，就没有数字，如果不是，就没有`null`，而是`true`或`false`。同样，在这个上下文中，它没有给原始语句添加任何东西。

如果你担心这一切会降低最初表达的意义，你可以为此建立一个简短的函数:

```
@function is($value, $values) {
  @return not not index($values, $value);
}
```

然后:

```
@if is($value, "initial" "inherit" "auto") {
  // Then do something
}
```

反正我的观点是:可以用`index()`函数代替的时候不要用多重相等赋值。

## 使用`@warn`

如果说有一个特性经常被 Sass 开发者忽略，那就是`@warn`指令。那太糟糕了，因为这是事情开始变得有趣的时候。`@warn`使您能够直接从 Sass 在控制台中打印出消息:

> @warn 指令将 SassScript 表达式的值打印到标准错误输出流中。对于需要警告用户弃用或从 mixin 使用错误中恢复的库来说，这很有用。
> [萨斯参考](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_6)

这是一个很棒的工具，可以警告开发者:

*   潜在的错误
*   贬值
*   Sass 未经开发商同意而采取的行动
*   发生什么事了

列表中的第一个是最有用的，因为 Sass 还没有`@error`指令*和*。所以无论什么时候做 mixin/function，都要检查给定的参数，确保没问题；如果不是，警告开发人员(并返回一个 falsy 值)。例如:

```
@function color($color) {
  @if not map-has-key($colors, $color) {
    @warn "No color found for `#{$color}` in `$colors` map.";
  }

  @return map-get($colors, $color);
}
```

*注意:关于 Sass 中错误处理的更多信息，我已经为 Tuts+写了一篇关于这个主题的深入文章。*

关于`@warn`的其他用例，一些库用它来反对一些正在发展的 mixins[像波旁威士忌](https://github.com/thoughtbot/bourbon/blob/55b18a209826064c8ee3dfd5adf1888c715a2596/dist/_bourbon-deprecated-upcoming.scss#L7)。

```
@mixin inline-block {
  display: inline-block;
  @warn "inline-block mixin is deprecated and will be removed in the next major version release";
}
```

Guardian [的 Sass-MQ 使用它](https://github.com/guardian/sass-mq/blob/ab8c99b531ae6485d568f379a520c4a96000d595/_mq.scss#L29)来警告开发者何时试图将无单位值转换为`em`。考虑到这一点，明智之举可能是模棱两可的。

```
@function mq-px2em($px, $base-font-size: 16px) {
  @if (unitless($px)) {
    @warn "Assuming #{$px} to be in pixels, attempting to convert it into pixels for you";
    @return mq-px2em($px + 0px); // That may fail.
  } @else if (unit($px) == em) {
      @return $px;
    }
  @return ($px / $base-font-size) * 1em;
}
```

我还可以想到这样一种情况，它在实例化时打印出一个重要 mixin 的配置，这是一种提醒，表明应用程序正在加载，并且具有给定的配置。

## 使用`argList`表示未知数量的参数

每当您构建一个接受任意长度的值列表的函数或 mixin 时，您可能应该使用一个`argList`而不是一个简单的`list`。一个`argList`是我们称之为*变量参数*的技术数据类型。

> 有时 mixin 或函数接受未知数量的参数是有意义的。例如，用于创建框阴影的 mixin 可以将任意数量的阴影作为参数。在这些情况下，Sass 支持“变量参数”，即 mixin 或函数声明末尾的参数，它将所有剩余的参数打包成一个列表。这些参数看起来就像普通的参数，但是后面跟有`...`。
> [萨斯参考](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variable_arguments)

为什么说`argList`更好？从技术角度来看，它不是。不管它是一个`list`还是一个`argList`，你都可以在两者上执行相同的操作，所以为什么要为此烦恼呢？

简而言之，因为它意味着什么。当使用`argList`时，你明确地说*它可以保存任意数量的值，没有限制，并且只要这个`argList`存在*，就不检查长度。同时，使用常规的`list`只是将几个值打包到一个变量中的一种方式。也许你希望这几个值都是 2。或者 3。或者 4。任何特定的数字。像这样:

```
// Function doing something with a key/value pair from a map
// ---
// @param [list] $pair: key/value pair
// ---
@function map-key-value-pair($pair) {
  @if not length($pair) == 2 {
    @warn "`map-key-value-pair` function is expecting a list of 2 items, #{length($pair)} given.";
    @return false;
  }

  // Then do something
}
```

另一方面，在这种情况下，您可能想要一个`argList`而不是一个`list`:

```
// Returns the highest value
// ---
// @param [argList] $values: numbers
// ---
@function max($values...) {
  $max: nth($values, 1);

  @for $i from 2 through length($values) {
    $value: nth($values, $i);
    @if $value > $max {
      $max: $value;
    }
  }

  @return $max;
}
```

在这种情况下，拥有一个`list`没有太大的意义，但是拥有一个`argList`给函数赋予了意义，甚至是来自签名！因为这两种数据类型之间几乎没有区别，所以这在这里是一个很大的语义问题。

## 使用别名

这是为那些用 Sass 构建框架、网格系统、Compass 扩展和其他东西的人准备的。当你构建一个 API 的时候，要尽可能的清晰。如果您的函数/mixin 名称有点长也没关系，只要它们有意义。

但是如果你不想一遍又一遍的输入同一个长函数，就做别名吧。然后，开发人员有责任决定他们是愿意使用模糊的别名还是明确的名称。

例如:

```
@function get-configuration($option) {
  @return map-get($global-configuration, $option);
}
```

真的，`get-configuration()`打字很长。所以做一个别名，返回`get-configuration()`的结果:

```
@function conf($opt) {
  @return get-configuration($opt);
}
```

给你。由于原始函数，该 API 既清晰又易于使用别名。不要为了简洁而修改你的代码，这不值得。

## 最后的想法

正如您所看到的，正是这些小事将普通的代码变成了吸引人的东西。每当你有时间，关心一个项目的时候，一定要尽可能的打磨。知道你已经尽你所能清理和优化了你的代码是一种很棒的感觉！

## 分享这篇文章