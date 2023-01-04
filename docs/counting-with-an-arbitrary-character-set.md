# 用任意字符集计数

> 原文：<https://www.sitepoint.com/counting-with-an-arbitrary-character-set/>

本周，我们来看一个简单而灵活的技术，用任意字符集进行计数。这不是你经常需要的东西；但是当您这样做时，您会发现 JavaScript 的内置函数都不是为处理它而设计的。

JavaScript 确实有内置的函数来解析和转换不同基数之间的数字。例如，`parseInt`法可以处理从`2`到`36`的任意<dfn>基数</dfn>(数字基数)，常用于非十进制数的数字转换和计数。`Number.toString`方法可以往复，将十进制数转换回非十进制数串:

```
var character = "2F";
alert(parseInt(character, 16));    //alerts 47

var number = 47;
alert(number.toString(16));        //alerts "2F";
```

但是如果你想用克林贡数字来计数呢？或者更有可能，使用希腊或西里尔字母，象形文字，或某种符文？我要展示的技术可以做到这一点，在任何数字基础上；为了充分说明这一点，我将向您展示一些使用十六进制<dfn>(基数为 16)中的**大写希腊字母的例子。**</dfn>

## 这些都在词典里

所以我们需要做的第一件事就是定义一个<dfn>词典</dfn>，它是我们将要使用的字符的词典，定义为一个单一的 unicode 转义序列字符串。在这种情况下，我们有 16 个大写希腊字母，从 Alpha 到 Pi，每个数字由一个字母表示，整个字符串的长度决定了数字基数:

```
var lexicon = "u0391u0392u0393u0394u0395u0396u0397u0398u0399u039au039bu039cu039du039eu039fu03a0";
```

转义序列是一个字符

值得注意的是，尽管定义一个 unicode 转义序列需要六个键入的字符，但它仍然只在字符串中显示为一个字符，因此词典有 16 个字符长。

一旦我们有了词典，我们就可以使用`String.charAt`通过数字索引来引用一个字符，反之，使用`String.indexOf`获得一个字符的数字索引:

```
var number = lexicon.indexOf("u0398");    //the decimal equivalent of "Θ" 

var character = lexicon.charAt(7);         //the character equivalent of 7
```

所以我们做的任何计算都将基于这两种方法。例如，让我们定义一个运行`"Κ"`次迭代的`for`-循环，并列出其间的每个字符:

```
var str = "";
for(var i=0; i<lexicon.indexOf("u039a"); i++)
{
    str += lexicon.charAt(i) + "n";
}
alert(str);
```

但是更大的数字呢，比如说，显示相当于`23`的字符？我们只需提取单个数字，然后获取对应的字符，在本例中是`2`和`3`:

```
var target = 23;

var conversion = lexicon.charAt(Math.floor(target / 10))
               + lexicon.charAt(target % 10);

alert(conversion);
```

更有趣的是，如果我们要转换的数字既包含字母又包含数字，比如十六进制的<abbr title="hexadecimal">数字</abbr>数字`"2F"`呢？在这种情况下，我们必须单独转换每个数字，因为我们不能通过十六进制索引( <abbr title="that is">ie)来引用一个字符。</abbr> `lexicon.charAt("F")`就得变成`lexicon.charAt(15)`):

```
var target = "2F";

var conversion = lexicon.charAt(parseInt(target.charAt(0), 16))
               + lexicon.charAt(parseInt(target.charAt(1), 16));

alert(conversion);
```

当然，最后两个例子相当简单，因为位数是已知的；但是修改这个过程来遍历这个数字包含的所有数字并不困难。您需要的所有组件都在这里，只需根据您的具体需求对它们进行调整。

## 数据才是最重要的！

碰巧的是，如果需要的话，您可以使用完全相同的方法来使用普通的拉丁数字和字母进行计数。词典的可扩展特性意味着您可以使用它将 JavaScript 的原生能力扩展到大于`36`的半径，以及当时合适的任何符号。

或者只是开发一些时髦的时钟！

**note:**Want more?

如果你想阅读更多詹姆斯的作品，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章