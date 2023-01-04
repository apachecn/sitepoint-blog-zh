# Sass 基础:函数指令

> 原文：<https://www.sitepoint.com/sass-basics-function-directive/>

我最近写了一篇关于 Sass 基础知识的文章，特别是关于 [@mixin 指令](https://www.sitepoint.com/sass-basics-the-mixin-directive/)。为了与强调 Sass 基础的主题保持一致，这次我将讨论@function 指令。

## 一个函数做什么

```
@function remy($pxsize) {
 @return ($pxsize/16)+rem;
}

h1 { font-size: remy(32);}
```

函数看起来很像 mixin，它们都接受相同类型的参数。虽然它们看起来很相似，但 Sass 函数的行为是不同的。虽然 mixin 通过减少重复代码的输入使我们的生活变得更容易，但函数允许您从代码中剥离可重复的逻辑。例如，在上面的代码中，我们使用一个函数来计算给定像素大小的 rem 值。结果代码将是:

```
h1 { font-size: 2rem; }
```

正如您所看到的，函数所做的只是返回一个在样式表中使用的值，而不是像 mixin 那样对元素应用样式。

## 函数或混合

理解什么时候使用函数而不是 mixin 的关键是知道你想要什么。mixin 用于创建样式，这将是一个不断编写的苦差事。使用 mixin，您可以用一行代码轻松地编写这些样式。当编写 mixins 时，您可能会尝试包含计算。我们可以把 remy 函数写成 mixin:

```
@mixin remy ($pxsize) {
 font-size: ($pxsize/16)+rem;
}

h1 { @include remy(32);}
```

这可能行得通，但会适得其反。首先，mixin 被锁定在仅仅计算字体大小的 rem。如果我们想将 mixin 用于其他属性呢？我们可以重新编写 mixin 来接受不同的参数，但那样会有些矫枉过正。

另一方面，函数被设计为返回值。当你需要生成样式时，使用 mixin。当您需要封装一些逻辑时，特别是如果您打算在项目的不同元素中使用它，那么就使用函数。我们可以在我们选择的任何元素上使用 remy 函数。

```
h1 { font-size: remy(32);}

div { width: remy(800);}
```

## 如何创建函数

首先，使用@function 指令创建一个 Sass 函数。后面是函数名和括号中的所有参数。

```
$col-count: 12;

@function col-pct($columns) {
 @return unquote((100/$col-count)*$columns+"%");
}
```

记住函数只返回一个值，所以我们必须调用@return 来设置函数返回的值。还要注意，该函数可以使用任何全局定义的变量。上面的 col-pct 函数计算指定列数的百分比大小。我还使用了一个内置的 Sass 函数，从返回值中去掉引号。Sass 附带了许多内置函数，你可以在这里查看。

## 如何使用函数

通过提供函数名和任何参数来调用函数。例如，要使用 col-pct 函数计算六列的大小:

```
.col-6 { width: col-pct(6);}
```

生成的代码是:

```
.col-6 { width: 50%; }
```

## 争论

我们已经看到函数可以使用全局变量。我们也可以使用与 mixins 相同类型的参数。当然，我们必须按照正确的顺序提供参数。假设我们修改了 remy mixin 以接受 rembase 大小的参数。

```
@function remy($pxsize,$rembase) {
 @return ($pxsize/$rembase)+rem;
}
```

如果我们以错误的顺序提供参数，我们将得到一个不正确的值。当然，除非我们使用关键字参数

## 关键词

如果使用关键字，我们可以用任意顺序的参数调用新的 remy 函数。

```
h3 { font-size: remy2($rembase:8,$pxsize:32 );
```

这将正确工作，因为我们使用了关键字参数。

## 默认值

我们也可以在函数中使用默认值。进一步改进 rem 函数的一种方法是包含一个默认值。这样，指定 rem 基值将是可选的。

```
@function remy($pxsize,$rembase:16) {
 @return ($pxsize/$rembase)+rem;
}
```

然后我们可以用两种方法之一调用这个函数。

```
h3 { font-size: remy(32);}
p { font-size: remy(16,8);}
```

这两个例子都可以工作，尽管它们工作的 rem 基础会有所不同。

## 可变参数

我们也可以在函数中使用可变参数。我的 [@mixin 文章](https://www.sitepoint.com/sass-basics-the-mixin-directive/)里的 padding mixin 可以写成函数。

```
@function pad($pads...) {
@return $pads;
}
```

要使用该功能:

```
.five {padding: pad(25px,35px);}

.six {padding: pad(25px,35px,45px);}

.seven {padding: pad(25px,35px,45px,55px);}
```

尽管函数的能力有限，但我相信您能够找到一些方法将它们合并到您的项目中。试着在函数中去掉任何跨项目使用的逻辑。如果你想出一些有用的东西，它可能会在未来的项目中派上用场。

## 分享这篇文章