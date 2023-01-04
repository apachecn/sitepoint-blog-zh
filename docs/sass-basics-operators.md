# Sass 基础:运算符

> 原文：<https://www.sitepoint.com/sass-basics-operators/>

[Sass](http://sass-lang.com/) 相当受欢迎，趋向于成为每个前端开发者的必备工具。Sitepoint 已经发表了一些关于这种 CSS 预处理语言的介绍性文章。

在本文中，我们将继续深入研究 Sass 的操作。

注意:这些例子将基于 Ruby Sass。我也建议你看一下[雨果的文章](https://www.sitepoint.com/introducing-sass-compatibility/)，其中讨论了 Sass 兼容性。

## 赋值运算符

Sass 使用冒号(`:`)操作符来定义变量。例如:

`$main-color: lightgray;`

## 算术运算符

算术运算符用于执行标准的算术运算。

以下是 Sass 支持的算术运算符:

| 操作员 | 描述 |
| --- | --- |
| + | 添加 |
| – | 减法 |
| * | 增加 |
| / | 分开 |
| % | 剩余物 |

正如我们将在下一节中看到的，加法运算符`(+)`也可以用来连接字符串。

请注意，上述运算符仅适用于具有兼容单位的数字:

```
h2 {
 font-size: 5px + 2em; // error incompatible units

 font-size: 5px + 2; // 7px
}
```

此外，将同一单位的两个数相乘将产生无效的 CSS:

```
h2 {
 font-size: 5px * 2px; // invalid CSS
}
```

您可能知道，`/`符号是 CSS 速记属性的一部分。例如:

```
font: 16px / 24px Arial sans-serif;

background: url("http://example.com") no-repeat fixed center / cover;
```

但是，正如已经提到的，Sass 可以利用正斜杠(`/`)来执行除法运算。

那么，让我们看看萨斯是如何理解这个符号的:

```
h2 {
 font-size: 16px / 24px // Outputs as CSS

 font-size: (16px / 24px) // Uses parentheses, does division

 font-size: #{$base-size} / #{$line-height}; // Uses interpolation, outputs as CSS

 font-size: $base-size / $line-height // Uses variables, does division

 opacity: random(4) / 5; // Uses a function, does division

 padding-right: 2px / 4px + 3px // Uses an arithmetic expression, does division
}
```

Sass 负责操作的顺序(操作符优先)。例如，以下是一些通用规则:

*   首先计算括号中的表达式。
*   与加法(`+`)和减法运算符(`-`)相比，乘法(`*`)和除法(`/`)运算符具有更高的优先级。

它们的一个例子如下所示:

```
h2 {
 width: 3px * 5 + 5px; // 20px

 width: 3 * (5px + 5px); // 30px

 width: 3px + (6px / 2) * 3; // 12px
 }
```

## 等式运算符

相等运算符在条件语句中用于测试两个值是否相同。基于它们的评估，它们返回一个`Boolean`值。

Sass 支持以下等式运算符:

| 操作员 | 描述 |
| --- | --- |
| == | 等于 |
| ！= | 不等于 |

所有类型都支持这些。

我们来看两个例子。

在我们的第一个例子中，我们使用等号运算符(`==`)来测试`$font`参数的类型，并打印适当的消息。代码如下:

```
@mixin font-fl($font){
 &:after {
 @if(type-of($font) == string) {
 content: 'My font is: #{$font}.';
        } @else {
 content: 'Sorry, the argument #{$font} is a #{type-of($font)}.';
        }
    }
}

h2{
 @include font-fl(sans-serif);
}
```

这将编译为:

```
h2:after {
    content: 'My font is: sans-serif.';
}
```

在我们的第二个例子中，我们定义了一个列表并检查其条目的长度。使用模数(余数)操作符(`%`)，我们计算它们的长度，最后设置`h2`元素的`color`属性。下面是相应的代码:

```
$list: "tomato", "lime", "lightblue";

@mixin fg-color($property) {
 @each $item in $list {
 $color-length: str-length($item);
 @if( $color-length % 2 != 0 ) {
 #{$property}: unquote($item);
        }
    }
}

h2 {
 @include fg-color(color);
}
```

以及生成的 CSS:

```
h2 {
    color: lightblue;
}
```

值得一提的是，Sass 不支持严格的等式操作符(`===`)，而这种操作符在其他计算机语言中可以找到。然而，正如您将在下一个示例中注意到的，Sass 处理等式运算符的方式与其他语言处理严格等式运算符的方式相同。

```
// comparison in Javascript
("5" == 5) // returns true

("5" === 5) // returns false

// comparison in Sass
("5" == 5) // returns false

(20px == 20) // returns true (this does not work in LibSass)
```

## 比较运算符

与相等运算符类似，比较运算符用于比较数字。

Sass 支持以下比较运算符:

| 操作员 | 描述 |
| --- | --- |
| > | 大于 |
| >= | 大于或等于 |
|  | 不到 |
|  | 小于或等于 |

这里有一个简单的例子:

```
$padding: 50px;

h2 {
 @if($padding <= 20px) {
 padding: $padding;
    } @else {
 padding: $padding / 2;
    }
}
```

以及编译后的 CSS:

```
h2 {
    padding: 25px;
}
```

## 逻辑运算符

逻辑运算符允许我们在一个条件表达式中测试多个条件。

Sass 支持以下逻辑运算符:

| 操作员 | 情况 | 描述 |
| --- | --- | --- |
| 和 | x 和 y | `true`如果 x 和 y 都是`true` |
| 或者 | x 或 y | `true`如果 x 或 y 是`true` |
| 不 | x | `true`如果 x 不是`true` |

我们通过一个例子来解释一下。

我们定义了一个列表映射，它包含作为键的按钮状态和作为值的相应颜色。然后，我们创建一个条件来计算它的长度。如果整个条件评估为`true`，我们将`background-color`应用到`h2`元素。下面是我们最初的 Sass 代码:

```
$list-map: (success: lightgreen, alert: tomato, info: lightblue);

@mixin button-state($btn-state) {
 @if (length($list-map) > 2 or length($list-map) < 5) {
 background-color: map-get($list-map, $btn-state);
    }
}

.btn {
 @include button-state(success);
}
```

以及生成的 CSS:

```
.btn {
    background-color: lightgreen;
}
```

如我们所见，我们的条件返回`true`。这是因为地图的长度是 3，因此嵌套条件(`length($list-map) > 2`–`length($list-map) ) return` )是 T3。

请注意，尽管我们修改了表达式:

```
@if (length($list-map) > 2 or not (length($list-map) == 3)) {...} // returns true, applies background-color

@if (length($list-map) > 2 and not (length($list-map) == 3)) {...} // returns false, doesn't apply background-color
```

## 字符串操作

正如我们已经讨论过的，加法运算符(`+`)为我们提供了一种连接字符串的方法。

以下是基本规则:

*   如果我们将一个带引号的字符串(在`+`操作符之前)加到一个不带引号的字符串上，结果就是一个带引号的字符串。
*   如果我们将一个不带引号的字符串(在`+`操作符之前)添加到一个带引号的字符串中，结果就是一个不带引号的字符串。

为了证明这一点，我们来看看下面的例子。我们的 Sass 代码如下所示:

```
@mixin string-concat {
 &:after {
 content: "My favorite language is " + Sass;
 font: Arial + " sans-serif";
    }
}

h2 {
 @include string-concat;
}
```

相应的 CSS:

```
h2:after {
    content: "My favorite language is Sass";
    font: Arial sans-serif;
}
```

此外，我们可以利用插值将动态值放入字符串中(我们已经使用过了！).还要注意的是`null`值不会被打印出来。这里有一个与上一个相似的例子:

```
@mixin string-concat ($language) {
 &:after {
 content: "My favorite language is #{$language}";

        // second way without using interpolation
        //content: "My favorite language is " + $language;
    }
}

h2 {
 @include string-concat(Sass);
}
```

产生的 CSS:

```
h2:after {
    content: "My favorite language is Sass";
}
```

## 颜色操作

在第二节中，我们分析了算术运算符。有趣的是，我们也可以用它们来产生颜色值。

让我们看一看。

这是我们的 Sass 代码:

```
h2 {
 color: #468499 + #204479;
}
```

以及生成的 CSS:

```
h2 {
    color: #66c8ff;
}
```

那么，这些颜色操作是如何工作的呢？Sass 对配对执行相应的计算。这意味着:

`46+20=66 (red color)`、`84+44=c8 (green color)`和`99+79=ff (blue color)`。

请记住，十六进制值是红色、绿色和蓝色值的组合。此外，使用数字 0-9 和字母 A-F 来表示它们。

下面是我们的最后一个例子。与前一个唯一的不同是，我们现在使用 [rgba](http://sass-lang.com/documentation/Sass/Script/Functions.html#rgba-instance_method) 函数来表示颜色。

我们的 Sass:

```
h2 {
 color: rgba(70, 132, 153, 1) + rgba(32, 68, 121, 1);

    // error alpha channels must be equal
 color: rgba(70, 132, 153, .9) + rgba(32, 68, 121, .7);
}
```

请注意，当您使用 [rgba](http://sass-lang.com/documentation/Sass/Script/Functions.html#rgba-instance_method) 或 [hsla](http://sass-lang.com/documentation/Sass/Script/Functions.html#hsla-instance_method) 颜色功能时，颜色应该具有相同的 alpha 值。否则，Sass 将抛出一个错误。

## 结论

在本文中，我解释了 Sass 操作符。如您所见，有些类似于其他编程语言支持的操作符。希望这篇文章能帮助你提高关于这种令人敬畏的语言的技能。最后但同样重要的是，你可以在这个 Sassmeister 页面上找到这篇文章的所有例子[。](http://sassmeister.com/gist/0fb30d192fbeb20b2ddc)

## 分享这篇文章