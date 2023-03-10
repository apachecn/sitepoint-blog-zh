# Javascript 中的行尾

> 原文：<https://www.sitepoint.com/line-endings-in-javascript/>

我今天花了很多时间与 Javascript 中的行尾进行斗争，最终找到了一些非常值得分享的结果——如果只是为了避免其他开发人员陷入相同的调试黑洞。

您可能知道，根据操作系统的不同，简单的换行符实际上有三种形式。在 Unix 机器上，一个换行符' n '就可以完成这项工作。在 MAC 上，使用回车符“r”。DOS 和 Windows 都用:‘rn’。如果你不知道要注意什么，这是一个相对微妙的问题，会让你很难受。

今天，我被分配了一个简单的任务:构建一个 Javascript 函数，将文本区域内的单换行转换为双换行。我的第一次尝试是这样的:

 `var doublenewlinesRE = /([^n])n([^n])/g;
function doublenewlines(obj) {
obj.value = obj.value.replace(doublenewlinesRE, "$1nn$2");
}`

`[Double newlines](#)`

上面的代码使用了一个简单的正则表达式，该表达式查找非换行符的所有实例，后跟一个换行符，再跟一个非换行符的实例。然后，这个模式的实例被中间有两个换行符而不是一个换行符的相同模式替换。

这在 Windows、Linux 和 Mac 上的 Firefox 中运行良好，因为 Firefox 无论在什么平台上都将换行符视为“n”。它在 IE for Windows 和 IE for Macintosh 上崩溃了，因为这些浏览器分别使用‘rn’和‘r’。

很公平。这个问题的通常解决方案是在运行转换之前对行尾进行规范化，将三种组合中的每一种都替换为您喜欢的单个结尾(在我的例子中是“n”)。下面是我对该函数的第二次尝试:

 `function doublenewlines(obj) {
obj.value = obj.value.replace(/(rn|r|n)/g, 'n');
obj.value = text.replace(doublenewlinesRE, "$1nn$2");
}`

那也没用。在绞尽脑汁、调试和使用警告框之后，我终于发现了 Internet Explorer 的一个没有文档记录的、几乎令人麻木的模糊“特性”:当你将一个字符串赋给一个输入对象的 value 属性时，IE 会悄悄地将你漂亮的“n”行尾转换成平台偏好。微软的文档[没有注意到这个](http://msdn.microsoft.com/workshop/author/dhtml/reference/properties/value_0.asp)，但是我已经确认这在 Windows 和 Mac 版本的 Internet Explorer 上都发生了。

奇怪的是，如果你给一个隐藏的表单域对象赋值，转换不会发生；仅当分配给文本区域时，行尾才会改变。

下面的代码虽然看起来与刚才列出的代码功能相同，但却完全符合我的要求:

 `function doublenewlines(obj) {
var text = obj.value;
text = text.replace(/(rn|r|n)/g, 'n');
obj.value = text.replace(doublenewlinesRE, "$1nn$2");
}`

这样做很好，因为规范化的版本被赋给一个变量，而不是直接赋给 textarea 对象的 value 属性——因此 IE 的自动行尾转换被延迟到脚本的末尾，并且无法破坏我的第二个正则表达式。

最后，谈谈风格。如果我考虑的是代码重用，而不是快速解决问题，我可能会得出这样的结论:

 `function doublenewlines(text) {
text = text.replace(/(rn|r|n)/g, 'n');
return text.replace(doublenewlinesRE, "$1nn$2");
}`

`[Double newlines](#)`

虽然它需要 onclick 处理程序中多一点代码，但是只抽象出字符串操作，我就可以完全避免怪异的行尾转换问题。不过，至少我已经理解了 IE 的另一个小怪癖。

## 分享这篇文章