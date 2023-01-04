# js 如何在多行中声明一个字符串

> 原文：<https://www.sitepoint.com/js-howto-declare-string-multiple-lines/>

前几天有人问我如何在 jQuery 中多行声明一个字符串。它实际上是普通的 JavaScript，只需在每一行后添加转义字符反斜杠" "。

正如你所看到的，我们只是在每一行的末尾加了一个反斜杠，告诉解释器它是同一个长字符串的一部分。

```
var textFromChris = "me:  we lunchin?
 Sent at 11:34 AM on Friday
 me:  sure
 Sent at 11:58 AM on Friday
 chris:  T=12.30
 Sent at 12:07 PM on Friday";
var timeRegex = /T=([0-9.]+)/gm;
timeRegex.compile(timeRegex);
console.dir(timeRegex.exec(textFromChris));
```

**常见错误信息:**语法错误:[未终止的字符串文字](http://www.jquery4u.com/testing/jquery-undetermined-string-literal-error/)
**兼容性:**测试显示它可以在所有主流浏览器中工作，包括 IE 6。

显然，我们还有其他方法可以达到同样的效果。我们可以像这样简单地把字符串分开，然后它们在不同的行上声明也没关系。

```
var textFromChris = "me:  we lunchin?" +
 "Sent at 11:34 AM on Friday" +
 "me:  sure" +
 "Sent at 11:58 AM on Friday" +
 "chris:  T=12.30" +
 "Sent at 12:07 PM on Friday";
var timeRegex = /T=([0-9.]+)/gm;
timeRegex.compile(timeRegex);
console.dir(timeRegex.exec(textFromChris));
```

## 分享这篇文章