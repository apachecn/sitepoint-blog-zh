# 利用 Sass 的 Zip()函数

> 原文：<https://www.sitepoint.com/making-use-sass-zip-function/>

当涉及到我们的样式表时，Sass 减少了编写重复代码的需要，但是您已经知道这一点，否则您不会读到这里。我想向您展示一种很好的、精明的方法来编写速记属性，而不需要记住 CSS 值的顺序，如`animation`和`font`。

**在你开始之前**:因为我正在使用[萨斯地图](//sass-lang.com/documentation/file.SASS_REFERENCE.html#maps)所以熟悉一下它们可能是个好主意。恰好至少有[两篇](//www.sitepoint.com/using-sass-maps) [篇](//blog.grayghostvisuals.com/sass/real-sass-real-maps)篇。

## 永远不要背你能在书上查到的东西

你刚才读的标题是阿尔伯特·爱因斯坦的一句名言——而且是一句很好的名言。我最大的猜测是，我不是世界上唯一一个问自己“现在……在`fill-mode`之后还有`direction`吗？还是反过来？”

下面的设置可能不适合所有人，但我希望您注意这种方法。你可以把它改成你想要的任何方式。记住…天空是无限的。

**Sass**

```
@function sh-setup($config) {
 @return zip(map-values($config)...);
}

$animation_config: (
 name: none,
 duration: 0s,
 timing: ease,
 delay: 0s,
 iteration: 1, // infinite
 direction: normal, // reverse, alternate, alternate-reverse
 fill-mode: none, // forwards, backwards, both
 play-state: running
);

.object {
 animation: sh-setup($animation_config);
}
```

**CSS**

```
.object {
  animation: none 0s ease 0s 1 normal none running;
}
```

## 发生了什么事？

我们的`sh-setup()`函数接受一个名为`config`的参数，该参数将其值传递给`map-values()`，结果最终传递给`zip()`函数。这个函数接受由`map-values()`返回的逗号分隔的列表，并将那个*列表*转换成空格分隔的字符串！哇哦。Daaaaaannnnnggggg！！！

所以…这很酷，但是为什么在`map-values()`的结尾有三个句号(或者省略号)？嗯，省略号告诉`map-values()`“允许传递多个逗号分隔的值。”例如，当我们从等式中去掉`zip()`时，我们将返回下面的字符串。

```
none, 0s, ease, 0s, 1, normal, none, running
```

如果我们不添加`...`，Sass 将会错误地输出我们的简写，因为`zip()`不能接受我们的多个逗号分隔值。它将上面的输出视为一个单独的字符串。`...`帮助告诉 Sass 接受未知数量的参数，以便去掉逗号。我们开发人员[必须告诉 Sass](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variable_arguments) 可以将字符串分成不同的情况，所以我们最后得到:

```
none 0s ease 0s 1 normal none running
```

## 更进一步

现在我们已经用`animation`完成了，让我们用同样的方法尝试一下`font`的简写。`font`属性以非常特定的顺序为它的简写取值，否则**整个语句都失败**。如果你不记得订单了(*哈哈，谁记得*)，那么让我提醒你一下:

*   `font-style`
*   `font-variant`
*   `font-weight`
*   `font-size`
*   `line-height`
*   `font-family`

现在让我们按照我们想要的方式设置它。请注意，我们一点也没有触及我们的`sh-setup()`函数，而是在解决它。

```
@function sh-setup($config) {
 @return zip(map-values($config)...);
}

$fonts: "'Proxima Nova', Arial, sans-serif";

$font_config: (
 font-style: normal,
 font-variant: normal,
 font-weight: normal,
 font-measurements: 100%/1.5,
 font-family: unquote($fonts)
);

body {
 font: sh-setup($font_config);
}
```

**CSS**

```
font: normal normal normal 100%/1.5 'Proxima Nova', Arial, sans-serif;
```

我将`font-size`和`line-height`放入一个变量中，以避免过于复杂，因为我们需要`/`介于`font-size`和`line height`值之间。`unquote()`函数是允许我们为`font-family`属性输出多种字体的魔法。这个函数有助于保留 W3C 推荐的包含空格的字体系列名称的单引号[，以避免转义错误。我们还为多个`font-family`名称保留逗号。](https://www.w3.org/TR/CSS21/fonts.html#propdef-font-family)

我们可以使用前面的方法的另一种方式是定义另一个映射来保存我们的字体系列，并且仍然使用我们一直使用的相同函数。

```
@function sh-setup($config) {
 @return zip(map-values($config)...);
}

$fonts: (
 primary-font: "'Proxima Nova'", // font family names containing whitespace write like this"'Font Name'"
 font-fallback1: Helvetica,
 font-fallback2: Arial,
 font-failure: sans-serif
);

$font_config: (
 font-style: normal,
 font-variant: normal,
 font-weight: normal,
 font-measurements: 100%/1.5,
 font-family: unquote(#{map-values($fonts)})
);

body {
 font: sh-setup($font_config);
}
```

我喜欢这种情况下的可读性，并且我确切地知道在优先级方面要显示什么字体。缺点是为了可读性，我不得不在`unquote()`中插入`map-values()`,这让事情变得有点复杂。

## 离别的思绪

现在我们已经看到了这些使用`animation`和`font`速记的用例，你认为我们还能在哪里使用像`zip()`这样的东西呢？它能用于`background`速记吗？`grid`的语法怎么样？真的是由你来决定方法。我希望我至少启发了您去研究其他的助手函数，比如`zip()`，这些函数可供想要使用 Sass 的前端开发人员使用。当你冒险进入未知领域时，请记住“一个从未犯过错误的人也从未尝试过新事物。”~爱因斯坦

## 分享这篇文章