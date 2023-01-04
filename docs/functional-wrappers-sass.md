# Sass 中的函数包装器

> 原文：<https://www.sitepoint.com/functional-wrappers-sass/>

我不太喜欢到处都有全局变量，不管是哪种语言。在 Sass 中，和在 CSS 中一样，我们只有一个全局名称空间(尽管最新的 Sass 版本逐渐提供了更多的作用域特性)。这就像一个只有一个房间的房子:无论你多么努力地整理你的家具，一天下来，房子里仍然只有一个房间。

这有什么问题？说到 Sass，问题是变量可能会重叠。如果您有一个名为`$baseline`的变量，并且您使用的 Sass 网格框架也有一个`$baseline`变量，那该怎么办？或者更糟的是，如果你包含的 Sass 库使用一个`$_`变量来保存它工作所需的私有内容，而你碰巧用自己的东西覆盖了它，那该怎么办？简单:坏了。

回到我最初的观点:我不太喜欢用变量来配置 Sass 系统。我应该更精确地说“只有**个**变量”，因为，当然，变量是存储数据的关键。同时，我想到*功能包装器*很有帮助。

## 功能性什么…？

您可以将*函数包装器*视为 getters 和 setters，在某种程度上，它们是包装变量声明或检索以在其上添加一些额外功能的函数:验证、错误处理、默认值、警告等等。

做这个难道不痛苦吗？你说。看情况。变量中有一种美丽的东西:它的简单。变量是显而易见的:它是一个映射到存储键的值。还有比这更简单的吗？然而，我认为原始变量不是最适合处理库配置之类的事情。

实际上，选择权在你:要么你有一个从一开始就处理所有事情(错误、参数验证、默认值……)的函数包装器，要么你在你得到的每个函数和 mixins 中做所有这些事情。我要说清楚:我宁愿选择前者。

## 它是如何工作的？

让我们考虑一个简单的用例。我们构建了一个小的网格系统，它支持从左到右或者从右到左的书写模式。从这个配置中，我们的系统定义了一个`$main-direction`(或者`left`或者`right`)和一个`$opposite-direction`(与`$main-direction`相反)。这是我们用默认变量写的:

```
// Define the content direction for the whole grid system
$writing-mode: 'LTR' !default;

// ...

// DON'T TOUCH
$main-direction:     if($writing-mode != 'LTR', right, left);
$opposite-direction: if($writing-mode != 'LTR', left, right);
```

什么问题会导致此代码？让我们举几个例子:

*   `$writing-mode`应该是`LTR`或`RTL`，但是用户可以将其定义为任何值，没有问题。
*   如果`$writing-mode`设置为`ltr`，网格系统将从右向左运行，因为`LTR != ltr`。
*   如果`$writing-mode`无效，将不会发出错误或警告。无声失败。
*   当出于任何原因在另一个上下文中重新定义`$writing-mode`时，应该添加`!global`标志，但可能会被忘记。

我确信我们可以找到一些其他的缺点，但是这些是我们将在本文中讨论的主要问题。

## 构建包装器

所以这个想法是用一个函数(或者更好，一个 mixin)来设置内容方向。它将确保该值有效，可能会发出警告/错误，并最终创建全局变量。

```
/// Define the content direction for the whole grid system
/// @access public
/// @param {String} $value ("LTR") - Content direction, either `RTL` or `LTR`
/// @throw Throws an error if the given value is neither `RTL` nor `LTR`.
/// @output Nothing.
@mixin content-direction($value: "LTR") {
 $value: to-upper-case($value);

 @if index("RTL" "LTR", $value) == null {
 @error "Content direction can be either `LTR` or `RTL` (case-insensitive). "
 + "Given: `#{$value}`. Aborting.";
  }

 $__direction__: $value !global;
}
```

这是怎么回事？mixin:

1.  在根据有效字符串检查给定值之前，确保它是大写的；
2.  对照`RTL`和`LTR`检查大写给定值，如果两者都不是，则发出错误；
3.  动态创建一个名为`$__direction__`的全局变量，包含大写给定值。

不仅变量名不太可能与应用程序中的另一个变量冲突，而且也不会被手动获取，所以谁会关心这个名称呢？事实上，我们会有一个小包装。

```
/// Return the content direction
/// @access private
/// @throw Throws an error if `content-direction` has not been included yet.
/// @return {String} - Either `RTL` or `LTR`
@function get-content-direction() {
 @if global-variable-exists("__direction__") == false {
 @error "Content direction has not been initialized yet. "
 + "Please include `content-direction($value)` before anything else.";
  }

 @return $__direction__;
}
```

如果已经包含了`content-direction` mixin，这个函数返回`$__direction__`，或者抛出一个错误，提示用户在包含其他内容之前包含`content-direction`。您将看到这个函数的目的只是为了避免在我们的两个传入访问器中为主布局方向和相反方向重复错误。

## 布局方向

当我们想要访问主布局方向或相反的布局方向时，我们使用以下任何功能:

```
/// Return the layout direction
/// @access public
/// @require {function} content-direction
/// @return {Position} - Either `left` or `right`
@function get-layout-direction() {
 @return if(get-content-direction() == "LTR", left, right);
}

/// Return the layout opposite direction
/// @access public
/// @require {function} content-direction
/// @return {Position} - Either `left` or `right`
@function get-layout-opposite-direction() {
 @return if(get-content-direction() == "RTL", left, right);
}
```

在这一点上，它们的工作方式应该非常明显:它们获取内容方向，并返回`left`或`right`。

同样，我们可以直接检查`$__direction__`而不是构建一个`get-content-direction()`函数，但是如果用户试图在用`content-direction` mixin 定义内容方向之前获得一个布局方向，我们将无法提供一个有用的错误消息。相反，用户会看到:

> 未定义的变量:“$ __ 方向 _ _”。

不是很有帮助，是吗？尤其是因为用户完全不知道`$__direction__`是什么…

## 使用它

```
// Define the content direction for the grid
@include content-direction("RTL");

// Framework stuff
%grid-whatever-tool {
 margin-#{get-layout-direction()}: 1em;
}
```

## 最后的想法

我承认用 3 个函数和 1 个 mixin 来初始化 3 个变量似乎有点大材小用，在大多数情况下，我完全同意这种说法。

然而，当向最终用户提供 API 时，我发现让他们将`content-direction()`包含在他们想要的值中，捕捉任何异常并提供有用的错误消息，而不是让 Sass 在某个时候抛出随机错误，因为他们犯了一个诚实的错误，这样更优雅。

此外，当一个系统有几个选项时，很容易将它们都包装在一个映射中，并在默认的 mixin 中验证这个映射中的任何值，从而得到一个非常简洁的 API:

```
@include set-options((
    'baseline': 42px,
    'debug': false,
    'color': hotpink
));
```

顺便说一下，这是 [Compass](https://github.com/Compass/compass/blob/stable/core/stylesheets/compass/_configuration.scss) 、 [Susy](https://github.com/ericam/susy/blob/master/sass/susy/language/susy/_settings.scss) 和其他一些高级 Sass 框架的发展方式，所以你可以说这毕竟不是一个坏主意。；)

你[可以在 SassMeister](http://sassmeister.com/gist/1abece652c3d03f8109e) 上玩代码。

## 分享这篇文章