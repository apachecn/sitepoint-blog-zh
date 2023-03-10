# jQuery 未确定的字符串文字错误

> 原文：<https://www.sitepoint.com/jquery-undetermined-string-literal-error/>

你有没有遇到过这个恼人的错误信息:“未确定的字符串文字”。

![undetermined-string-literal](img/e4f28b2ebb2915bec173114c410b8e2e.png "undetermined-string-literal")

好的，我有三个你可能会遇到这种错误的场合，以及如何针对每个特定的情况修复它。

1.  多行字符串
2.  错误的字符串格式(错误的字符)
3.  尖括号

## 多行字符串

如果你试图[将一个包含多行的字符串](http://www.jquery4u.com/snippets/js-howto-declare-string-multiple-lines/)赋给一个变量，你可能会看到“未确定的字符串文字”错误。要解决这个问题，您必须在每一行之后使用 JavaScript 转义字符反斜杠("")来告诉解释器该行在哪里结束，并将字符串连接在一起。

另一种方法是简单地把你的字符串分割成比特，然后把它们加在一起。

## 错误的字符串格式(错误的字符)

如果你试图将 HTML 赋给一个变量，你可能会看到“未确定的字符串文字”错误。可能是您通过 ajax 从其他地方加载了 HTML，现在正试图使用/检查它。在尝试分配字符串之前，使用以下代码清除字符串中的错误字符。

## 尖括号

如果您试图使用尖括号(" > " & "前 10 个位置像" ABC " '；
//注意:abc 替换实际查询
[/js]

结果是 securequery 尖括号造成了错误，我不得不在编码和通过 AJAX 发送请求之前添加尖括号("")来解决这个问题(见下文)。

```
$.ajax({
  type: 'POST',
  url: '/ajax/abc',
  data: 'content=' + encodeURIComponent(''),
  dataType: 'html',
  success: function(data){
	console.log(data);
	//display results
	$('#results').html(data);
  }
});
```

这甚至可能是 jQuery 中的一个 bug，但不确定。当你在 firebug 中测试时，它仍然有效！所以也许不是…

```
var fine = '<securequery consumer="abc" engine="abc" template="abc">first 10 location like "abc"</securequery>';
console.log(fine);
```

## 分享这篇文章