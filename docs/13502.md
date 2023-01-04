# 第 2 集答案(现实生活中的正则表达式)

> 原文：<https://www.sitepoint.com/answers-to-episode-2-real-life-regular-expressions/>

是啊，我把这些答案贴出来有点晚了。不好意思！

如果你错过了，上周的挑战是关于[破译正则表达式](https://www.sitepoint.com/episode-2-real-world-regular-expressions/)并找到其中微妙的错误。

与上周一样，在得到实际答案之前，请允许我发表一点意见:

很明显，正则表达式是一把双刃剑。当然，破译它们是一个有趣的测验，但是想象一下在代码中遇到这些怪物并试图弄清楚它们做什么…不好玩。

幸运的是，几乎每个 regex 实现都有一个“verbose”模式，允许您在正则表达式中嵌入注释(在大多数语言中，这是`x`标志)。为了那些必须阅读你的代码的人，请使用详细模式！

好了，继续回答:

#### 1.`[A-PR-Y0-9]{3}-[A-PR-Y0-9]{3}-[A-PR-Y0-9]{4}`

这是一个[美国电话号码](http://en.wikipedia.org/wiki/Phone_number)，包括使用字母的(即`831-555-CODE`)。在详细模式下重写，它更有意义:

```
  [A-PR-Y0-9]{3}  # Area code prefix
  -
  [A-PR-Y0-9]{3}  # 3-digit exchange
  -
  [A-PR-Y0-9]{4}  # 4-digit suffix

```

<cite>birman</cite> 很好地总结了这个模式的问题[:](https://www.sitepoint.com/episode-2-real-world-regular-expressions/#comment-101984)

> 如果区号在括号中，如果数字组由点或空格而不是破折号分隔，或者手机上有 Q 和 Z，那么[它]不能解释前面的 1。它也不能确保该组是孤立的，而不是像 1234888-234-123456123 这样的东西的一部分。

最后一点——隔离错误——是编写正则表达式时非常常见的错误。

#### 2.`&(?!(w+|#d+);)`

这并不像大多数人认为的那样，是匹配 HTML 实体的错误尝试。它实际上是一种匹配 HTML 中的&符号的模式，这些符号是实体的*而不是*部分(它取自 Django 的 fix_ampersands 模板过滤器)。

以下是详细模式:

```
  &     # Match an ampersand...
  (?!       # ... that is *not* followed by...
    (
      w+   # ... word characters...
      |     # ... or...
      #d+ # ... numeric entity symbols...
    )
    ;       # ... and a semi-colon.
  )

```

这种模式的“问题”相当微妙:它匹配的是由 still invalid(例如`&#ggxy;`)表示的格式良好的 HTML 实体。因此，作为一种寻找未编码的&符号的方法，这很好，但是如果您想将它用作 HTML 验证器的一部分，这将是不可接受的。

#### 3.`(-?(?:0|[1-9]d*))(.d+)?([eE][-+]?d+)?`

大多数读者都收到了这封信。它是一个 [IEEE 浮点数](http://en.wikipedia.org/wiki/Scientific_notation)，有可选的指数。在详细模式下:

```
  (             # The non-fractional part of the base
    -?            # could be a leading negative sign 
    (?:           # Non-matching group...
      0|[1-9]d*  # 0, or multiple digits
    )  
  )
  (.d+)?      # Decimal point and fractional part of the base
  (             # Exponent
    [eE]          # 
    [-+]?         #  > "e", plus or minus, exponent.
    d+           # /
  )?

```

一些读者认为基础部分的`d`是一个 bug 实际上，它不是——该表达式匹配`0`，或者以 1-9 开头然后包含任何数字的数字。

实际的 bug 是这个模式匹配的是非规格化的数(即`123.45e3`，应该更恰当地写成`1.2345e5`)。

#### 4.`([da-f]{2}:){5}([da-f]{2})`

几乎每个人都收到了这个:这是一个 MAC 地址:

```
  ([da-f]{2}:){5}  # Two hex digits followed by a colon, x5
  ([da-f]{2})      # Two hex digits to end.

```

正如<cite>伯尔曼</cite>T4 指出的，这种模式不符合 MAC 地址允许的其他一些形式；它们可以用连字符(`12-34-56-78-9A-BC`)书写，也可以用点划线(`1234.5678.9ABC`)书写。

#### 5.`<[^>]*?>`

对于大多数读者来说，这个似乎也很简单；它匹配任何 [SGML](http://en.wikipedia.org/wiki/SGML) 标签。在详细语法中:

```
  <        # Atart the tag
  [^>]*?   # Any non-gt character
  >        # End the tag

```

这一个中的“bug”更抽象一些:畸形的 SGML/HTML 会严重破坏它。不过，我将把寻找这样的代码留给读者去做。

#### 下次

明天继续收听下一期的测验。本周的问题将是“每个 web 开发人员都应该知道的事情”测验；我认为这很有趣。

明天见！

## 分享这篇文章