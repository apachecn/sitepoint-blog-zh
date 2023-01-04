# Sass 基础:控制指令和表达式

> 原文：<https://www.sitepoint.com/sass-basics-control-directives-expressions/>

如果您使用 Sass 已经有一段时间了，或者查看了像 Foundation 这样的框架的代码，您会注意到 mixins 中使用了许多高级特性。这些 mixins 使用@if 和@for 这样的控制指令来完成为网格系统设置类这样的事情。

继续 Sass 的基础知识，我们将讨论这些控制指令，并看看如何在您的项目中使用它们。您可能从未发现有必要使用这些控制指令，但是知道您可以使用哪些工具总是好的。

## 如果()

这有点不同，因为它不是一个控制指令，而是 Sass 中的一个内置函数。与@if 指令不同，if()允许您测试条件并返回两个可能值中的一个。我们测试的条件不是真就是假。例如:

```
@mixin test($condition) {
 $color: if($condition, blue, red);
 color:$color
}

.firstClass {
 @include test(true);
}

.secondClass {
 @include test(false);
}
```

这将编译为:

```
.firstClass {
    color: blue;
}

.secondClass {
    color: red;
}
```

if()函数参数是要测试的条件，true result 和 false result 除 false 或 null 之外的任何值都被视为 true。在上面的例子中，我们可以用一个数字代替 true，得到同样的结果。

```
.firstClass {
 @include test(1);
}
```

## @如果

@if 指令接受一个表达式，如果结果不是 false 或 null，则返回样式。例如:

```
@mixin txt($weight) {
 color: white;
 @if $weight == bold { font-weight: bold;}
}

.txt1 {
 @include txt(none);
}

.txt2 {
 @include txt(bold);
}
```

这给了我们:

```
.txt1 {
    color: white;
}

.txt2 {
    color: white;
    font-weight: bold;
}
```

我们可以用多个@else if 语句和一个 final @else 来扩展@if 指令。这样我们可以测试多种情况。我们可以这样扩展上一个例子。

```
@mixin txt($weight) {
 color: white;
 @if $weight == bold { font-weight: bold;}
 @else if $weight == light { font-weight: 100;}
 @else if $weight == heavy { font-weight: 900;}
 @else { font-weight: normal;}
    }

.txt1 {
 @include txt(bold);
}

.txt2 {
 @include txt(light);
}

.txt3 {
 @include txt(heavy);
}

.txt4 {
 @include txt(none);
}

.txt5 {
 @include txt(normal)
}
```

这反过来给了我们

```
.txt1 {
    color: white;
    font-weight: bold;
}

.txt2 {
    color: white;
    font-weight: 100;
}

.txt3 {
    color: white;
    font-weight: 900;
}

.txt4 {
    color: white;
    font-weight: normal;
}

.txt5 {
    color: white;
    font-weight: normal;
}
```

我已经包含了最后两个类来演示添加@else 如何改变@if 指令的工作方式。在第一个例子中，没有使用粗体作为参数，它不会给我们一个字体粗细。当添加@else 时，任何与另一个@if 或@else if 不匹配的参数都将获得@else 语句的样式。这就是为什么`.txt4`和`.txt5`有相同的字体粗细。

## @for

@for 指令允许您在循环中输出样式。该指令既可以用作首尾相连，也可以用作首尾相连。不同之处在于，从开始到结束包括结束编号，而从开始到结束不包括结束编号。@for 语句使用一个变量来跟踪针对这些范围的循环。如果我们想递减计数而不是递增计数，我们可以让起始数大于结束数。让我们看看如何设置@for 语句。

```
@for $i from 1 through 12 {
    .col-#{$i} { width: 100/12 * $i;}
}
```

这里有一个@for 语句，我们可以用它来为网格系统设置列。@for 后面是变量，在本例中是$i。然后我们在它后面加上 from 和我们的起始(1)到结束(12)号。在循环的每一次重复中，我们生成一个样式。注意我们如何使用插值来使用变量作为我们生成的类的一部分。

```
.col-1 {
    width: 8.33333;
}

.col-2 {
    width: 16.66667;
}

.col-3 {
    width: 25;
}

.col-4 {
    width: 33.33333;
}
```

我没有包括@for 生成的所有样式，因为它们一直到 12。正如您所看到的，在每次循环中，都会创建一个样式，并将变量值添加到类名中。我们还根据变量进行了计算，以生成合适的宽度。

## @每个

@each 指令使用列表或映射，而不是起始值和结束值。在每次循环中，变量都会从列表或映射中获得一个值。

```
@each $usr in bob, john, bill, mike {
    .#{$usr}-avatar {
 background-image: url('/img/#{$usr}.png');
     }
}
```

@each 指令后面是我们的变量`$usr`。之后，我们有一个列表，它将被赋给变量`$usr`。我们也可以用地图作为我们的论据。之后，我们使用插值来构建我们的类，并显示正确的图片。

```
.bob-avatar {
    background-image: url("/img/bob.png");
}

.john-avatar {
    background-image: url("/img/john.png");
}

.bill-avatar {
    background-image: url("/img/bill.png");
}

.mike-avatar {
    background-image: url("/img/mike.png");
}
```

如果我们要使用 map，我们必须修改@each 语句并使用多重赋值。我们可以像这样在@each 语句中使用映射。

```
$ppl: ( usr1:bob, usr2:john, usr3:bill, usr4:mike );

@each $key, $usr in $ppl  {
    .#{$usr}-avatar {
 background-image: url('/img/#{$usr}.png');
    }
}
```

我们必须添加第二个变量来保存每个值的键。如果我们没有这样做，我们的键和值将在我们生成的样式中，而不是得到与上一个例子相同的结果。我们也可以在列表中使用多重赋值。

```
$alt: alert, yellow, red;

$sub: submit, white, green;

$bck: back, blue, transparent;

@each $type, $txt, $back in $alt,$sub,$bck {
    .#{$type}-button {
 color: $txt;
 background-color: $back;
    }
}
```

这里我们采用多个列表，并用它们来构建我们的类。当我们遍历每个列表时，列表中的项目被分配给@each 语句中的变量。这给了我们:

```
.alert-button {
    color: yellow;
    background-color: red;
}

.submit-button {
    color: white;
    background-color: green;
}

.back-button {
    color: blue;
    background-color: transparent;
}
```

## @当

@while 指令输出样式，直到语句为 false。类似于@for 指令，@while 指令允许我们在循环中有更大的灵活性。我可以将上面的@for 指令重写为@while 循环。

```
$x:1;

@while $x < 13 {
    .col-#{$x} { width: 100/12 * $x;}
 $x: $x + 1;
};
```

我们没有像@for 指令那样在语句中设置范围，而是在变量中设置一个值。然后，我们使用该值作为对@while 语句的测试。在这种情况下，我们说，只要$x 小于 13，输出样式。一旦达到 13，该语句将为假，循环将停止。要记住的关键是增加你的变量，否则你的循环将永远不会结束。如你所见，这个例子是将变量加 1，直到它达到 13。

## 结论

Sass 确实包含了很多强大的特性，使得我们的前端开发更加容易。尽管控制指令很强大，但是除非你正在构建一个大的框架，否则你可能不会发现它们有很多用处。就像我之前说的，重要的是了解你可以使用的工具。也许有一天你会发现这些控制指令的用途。

## 分享这篇文章