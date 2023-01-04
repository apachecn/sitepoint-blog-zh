# CSS 中的伪注释(或者，浏览器如何解析样式)

> 原文：<https://www.sitepoint.com/pseudo-comments-in-css-or-how-browsers-parse-styles/>

CSS 规范没有提到它，但是您可以在 CSS 文件中模仿 C 风格和/或 Unix 风格的行注释(有一些注意事项)。其他人以前也写过关于它们的文章(特别参见 SitePoint 的 Web Foundations 帖子，内容涉及 [CSS 评论](https://www.sitepoint.com/web-foundations/css-comments/))。本文将对它们进行更详细的研究。

## CSS 注释

CSS 解析器，[按照规范](https://www.w3.org/TR/CSS2/syndata.html)，正式支持一种风格的注释，即来自 C 风格语言的多行注释，它使用一个开始标记`/*`和一个结束标记`*/`，如下所示:

```
/*
  characters between, and including, the start and
  end tokens are ignored by the parser,
*/
```

因此注释中的规则声明将被忽略:

```
body {
  background: red;
  /*
  background: white;
  */
}
```

注释中的块声明将被忽略:

```
/*
body {
  background: red;
}
*/
```

在每个例子中，我们都有意使用注释语法*来指示解析器忽略内容。*

但是，我们可能会意外地这样做，就像格式错误的声明一样，例如

```
body {
  background: red    /* missing semi-colon */
  background: blue;      
}
```

在本例中，*和*后台声明都没有应用，因为缺少分号。解析器扫描*的下一个*分号，确定整个两行语句的格式不正确，因此忽略整个 lexed 内容。如果我们完全忽略财产价值，也会发生同样的事情:

```
body {
  background:
  background: blue; /* this declaration is not applied */
}
```

并且*表明*表明我们可以使用格式错误的声明作为…

## 伪评论

我们将这些称为“伪注释”,因为准确地说，这些不是以行尾字符结束的注释。相反，它们通过对跟随它们的输入进行变形来工作，甚至在随后的行中。这是由于[规则集、声明块和选择器](https://www.w3.org/TR/CSS2/syndata.html#rule-sets)的错误处理过程:

> 如果选择器中的任何地方有错误，应该忽略整个语句，即使选择器的其余部分在 CSS 2.1 中看起来是合理的

在以下取自规范的示例中，由于选择器中存在无效的“&”字符，第二个规则集被忽略:

```
h1, h2 {color: green }
h3, h4 & h5 {color: red } /* <= ignored */
h6 {color: black }
```

同样，在下面的代码中，由于背景属性名中存在额外的字符，第二个和第三个声明将被忽略:

```
body {
  background: red;
  xbackground: white;    /* property name is not recognized */
  y background: blue;    /* property name is not well-formed */
}
```

快速浏览一下英语键盘，会发现以下特殊字符将作为单行声明注释:

```
selector {
  ~ property-name: ignored;
  ` property-name: ignored;
  ! property-name: ignored;
  @ property-name: ignored;
  # property-name: ignored;
  $ property-name: ignored;
  % property-name: ignored;
  ^ property-name: ignored;
  & property-name: ignored;
  * property-name: ignored;
  _ property-name: ignored;
  - property-name: ignored;
  + property-name: ignored;
  = property-name: ignored;
  | property-name: ignored;
  \ property-name: ignored;
  : property-name: ignored;
  < property-name: ignored;
  . property-name: ignored;
  > property-name: ignored;
  , property-name: ignored;
  ? property-name: ignored;
  / property-name: ignored;
}
```

不过，不要只使用任何字符，坚持 C 和 Unix 惯例，使用`#`或`//`:

```
// background: ignored;
  # background: ignored;
```

## 分号

分号是规则声明的结束标记。因此，他们不能“评论”后面的文本。具体来说，解析器将悬空分号视为格式错误的声明(缺少名称、冒号或值的声明)。

如前所述，当常规的多行注释格式错误时，也就是说，当规则集或声明的开始和结束标记不平衡时，解析器会忽略后面的声明或规则集。由于解析器将为受影响的声明搜索下一个声明结束标记(分号),下面的内容实际上将“注释”掉两个后台声明:

```
body {
  background:
  background: blue;      /* both lines ignored */
}
```

这可以通过在注释之后、下一个声明之前添加一个分号来解决(因此将应用背景蓝色声明):

```
body {
  background: ;          /* ignored */
  background: blue;      /* processed */
}
```

对于缺少分号的行上的伪注释，效果是一样的:

```
body {
  background: # red   /* ignored */
  background: blue;   /* also ignored */
}
```

并通过恢复分号进行更正:

```
body {
  background: # red;  /* ignored */
  background: blue;   /* processed */
}
```

## 内联与下一行放置

这就是“伪”进入术语“伪评论”的地方这可能是根本不称这些为“注释”的充分理由，因为它们打破了 C 或 Unix 风格的行注释的行尾约定。

放在自己一行的伪注释会抑制下一行的声明。在下面，背景将是蓝色的:

```
body { 
  //
  background: white !important;  /* ignored */
  background: blue;
}
```

在同一行的有效声明的之后放置一个伪注释*将会抑制下一个*行的*声明。在下面，背景将是白色而不是蓝色:*

```
body {
  background: white; // next line is ignored... 
  background: blue !important;
}
```

即使是带有内嵌伪注释的 CSS 选择器的“缩小”版本也会表现为单声明注释。在下面的代码中，由于注释标记`#`的存在，第一个背景声明被忽略，解析器将其识别为在下一个分号处终止，第二个背景声明被识别为格式良好，因此被应用(在这种情况下，蓝色将被应用于正文背景):

```
body { // background: red !important; background: blue; }
```

## 选择器

同样的基于规则的行为也适用于选择器。

当选择器前面有伪注释(无论是行内注释)时，整个选择器规则集都会被忽略

```
// body {
  background: white !important;
}
```

或者下一行:

```
//
body {
  background: white !important;
}
```

## 作为目标畸形的伪评论

伪注释利用规范的[规则来处理解析错误](https://www.w3.org/TR/CSS2/syndata.html#parsing-errors)。实际上，他们利用自己的畸形来工作。

### 未知值

> "用户代理必须忽略带有未知属性的声明."

包含无法识别的属性名的声明将不会被计算，例如，下面的`body`规则集中的`comment`属性:

```
body {
  comment: 'could be text or a value';
}
```

### 非法值

> "用户代理必须忽略带有非法值的声明."

下面定义的`color`属性被忽略，因为该值是一个*字符串*，而不是一个值或颜色关键字:

```
body {
  color: "red";
}
```

### 格式错误的声明和陈述

> 用户代理必须处理在解析声明[或语句]时遇到的意外标记，方法是一直读取到声明[或语句]的末尾，同时遵守匹配()、[]、{}、“”和”对的规则，并正确处理转义。"

```
body {
  -color: red;
}
```

由不匹配的`()`、`[]`、`{}`、`""`和`''`对组成的畸形声明比其他声明更容易被忽略(因此也更危险)。引用字符`""`和`''`的处理不同于分组字符`()`、`[]`、`{}`。

### 引用字符

下面第二个声明中的*不成对的*撇号将阻止规则集中随后的*声明被处理(因此，背景将是红色的):*

```
body {
  background: red;
  'background: white;  /* ignored */
  background: blue;    /* also ignored */
}
```

然而，撇号*后面的第三个声明将被处理*(因此背景将是金色):

```
body {
  background: red;
  'background: white;  /* ignored */
  background: blue;    /* also ignored */
  background: gold;    /* processed */
}
```

总之，你不能在它自己的一行结束一个引用字符。

### 分组字符

一般来说，分组字符`()`、`[]`、`{}`应该*避免*成为伪注释，因为它们对解析器的块识别规则的干扰更大，因此会“注释掉”不止一个声明。为了完整起见，我们将检查其中的一些。

例如，*不匹配的*起始组字符的出现抑制了*到*样式表*末尾的所有*后续声明(不仅仅是规则集)。逗号、括号和大括号都是如此。

在下面，只处理`background: red;`声明；整个样式表中*之后的所有声明和选择器都将被忽略:*

```
body {
  background: red;

  {  /* *every* declaration that follows will be ignored, 
        including all subsequent selectors, to the 
        end of the stylesheet. */

  background: white;
  color: aqua;
  margin: 5px;

  ...
}
```

当分组字符*匹配*时，*规则集*中的分组及后续未分组声明将被取消。在下面，背景将是红色，而不是金色:

```
body {
  background: red;

  (
  background: white;
  background: blue;
  background: fuchsia;
  )

  background: gold;
}
```

一个*结束的*逗号或括号将只隐藏下一个出现的声明。在下面，背景将是金色的:

```
body {
  background: red;

  ]
  background: white;  
  background: blue;
}
```

然而，右大括号*，`}`将隐藏规则集末尾的所有声明。在下面，背景将是红色的:*

```
body {
  background: red;

  }
  background: white;
  background: blue;
}
```

## At 规则

At 规则有两种形式:

*   用大括号表示的主体声明，`{ ... }`(如`@media`)，
*   以分号`;`结束的规则声明(如`@charset`)。

body-block at-rules 上的伪注释与选择器的行为相同(即，忽略整个 at-rule)。

## 应用于带有体块的 at 规则的伪注释

对于包含体块的 at-rules，如`@keyframes`、`@media`、`@page`。和`@font-face`，当 at-rule 前面有一个伪注释时，不管是行内的，整个 at-rule 规则集都会被忽略

```
// @media (min-width: 0) {
  body {
    background: white !important;
  }
}
```

或者下一行:

```
//
@media (min-width: 0) {
  body {
    background: white !important;
  }
}
```

## 应用于不带主体块的 at 规则的伪注释

没有块的 At-rules，比如`@charset`和`@import`，为内联伪注释行为提供了一个有趣的例外。

关键字后带有伪注释*的 at 规则将被忽略:*

```
/* the pseudo-comment before url()
   suppresses the entire @import */
@import // url('libs/normalize.css');
```

但是一个伪注释，即*在*之前，at 规则抑制了导入*和导入后的第一个规则或选择器*。这是因为解析器将伪注释的`@import`视为格式错误的语句，并寻找下一个匹配的大括号以完成下一个规则集。

因此，在一系列`@import`规则中的一个`@import`之前的伪注释将抑制*所有的*随后的`@import`规则*和*最后一次导入之后的第一个声明或选择器:

```
// @import url('libs/normalize.css');

/* NONE of these loads because previous statement is
   processed as a malformed statement, and the parser
  looks for the next matching braces. */
@import url('libs/normalize.css');
@import url('libs/example.css');
@import url('libs/other.css');
@import url('libs/more.css');
@import url('libs/another.css');
@import url('libs/yetmore.css');
```

对此的修复出奇的简单:只需在注释`@import`后添加一个空的 body 块

```
// @import url('libs/normalize.css');

{}  /* now, the next import will load */

@import url('libs/normalize.css');
```

这对于调试来说很有趣，但是这种行为非常特殊，您应该避免使用没有主体块的 at-rules 的伪注释方法，而是使用多行语法。

### At 规则和未知的 at 关键字

> "用户代理必须忽略无效的 at-keyword 及其后面的所有内容，直到包含无效 at-keyword 的块的末尾，或者直到并包括下一个分号(；)，或者直到并包括下一个块({…})，以先出现的为准"

我们可以通过使用一个未知的 at 关键字`@comment`来说明这一切，作为多行语法的定制 at 规则替代。例如，以下 at-rule 被解析为右大括号`}`，被确定为格式错误，然后被忽略:

```
@comment { 
  I'm not processed in any way.
}
```

起初，这看起来是无害和可读的，但是由于在`I'm`中撇号的存在，我们再次引入了引用字符问题(即，您不能在它自己的行上终止单引号字符)。这意味着，如果我们的自定义`@comment`的主体在它自己的行上是封闭的，那么随后的 at-rule 或选择器也将被忽略，因为规则的*声明*由于在`I'm`中出现撇号而格式不正确:

```
@comment { 
  I'm not processed in any way. }

body { background: blue; }   /* this whole block will not be processed either! */
```

可以用外面的引号来挽救，或者在大括号里面

```
@comment { 
  "I'm not processed in any way."  }  /* fixed */

body { background: blue; }   /* this block will work */
```

或者去掉大括号，用分号结束伪注释，或者行内:

```
@comment "I'm not processed in any way.";

body { background: blue; }   /* this works */
```

或者下一行

```
@comment 
"I'm not processed in any way.";

body { background: blue; }   /* this works */
```

## 预处理器

各种 CSS 预处理程序支持类似的多行和单行注释。

### 厚颜无耻

> Sass 支持使用/* */的标准多行 CSS 注释，以及使用//的单行注释。多行注释尽可能保留在 CSS 输出中，而单行注释被删除。

[来源](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#comments)

压缩模式通常会去掉所有注释，除非注释前面有`/*!`。

但是，您可以使用一个*单字符*伪注释，比如`#`，输出将包含注释行。

```
body {
   # background: red; 
}
```

### 较少的

> 块样式和行内注释都可以使用。

[来源](http://lesscss.org/features/#features-overview-feature-comments)

不清楚(至少对我来说)Less 会隐藏这些注释还是将它们打印到输出中。从 StackOverflow 的帖子来看，似乎很少会在块级别聚合行注释。

### 唱针

手写笔也支持多行`/* */`和单行注释`//`，但是如果启用了`compress`指令，就会在输出中隐藏这些注释。如果你总是希望输出多行注释，使用*多行缓冲*注释。

> 未隐藏的多行注释以/*开头！。这告诉 Stylus 不管压缩与否都输出注释。

```
/*!
 * This will appear in the output.
 */
```

[来源](https://learnboost.github.io/stylus/docs/comments.html)

## 最佳实践

> "可读性很重要。"
> <cite>【https://www.python.org/dev/peps/pep-0020/】T2’>蟒蛇之禅</cite>

注释可以使晦涩的代码更具可读性，但是可读性取决于不止一个约定。CSS 中的伪注释与其说是可读性，不如说是违背了惯例(也就是解析器)。

如果你发现你需要使用伪注释:

*   坚持 C 和 Unix 惯例，使用`//`或`#`作为伪注释分隔符。
*   将伪注释放在同一行中要忽略的项目之前。
*   使用空格将伪注释分隔符与预期的规则分开~例如`# background: ignored;`。

使用伪注释:

*   使用伪注释进行**调试**，特别是在使用交互式 CSS 编辑面板时，比如 Chris Pederick 的[Web Developer extension](http://chrispederick.com/work/web-developer/)(chrome，firefox，opera)。
*   使用伪注释来防止处理带有主体的**单个**声明、选择器或 at-rules。

避免伪评论:

*   避免将伪注释用于文本描述和没有正文的 at-rules(例如@import) ~改用*多行* `/* ... */`注释。
*   避免引用字符`'', ""` ~它们很难被人眼扫描到，并且不能在自己的行上终止。
*   避免分组字符`(), [], {}` ~它们引入了更复杂的扫描(并且不能在自己的行上终止)。
*   避免在**产品**代码中使用伪注释~虽然不是“有害的”,但它们只是额外的字节。

## 分享这篇文章*