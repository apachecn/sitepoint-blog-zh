# jquery 删除 html 中的不良字符

> 原文：<https://www.sitepoint.com/jquery-removing-bad-charaters-html/>

我以前写过使用 jQuery 从 Div 中去除所有 HTML 标签。现在，如果您想从 HTML 字符串中删除所有不良字符(可能由$。getScript()调用等)。

这是你如何轻松清理你的 html 并删除不良字符的方法，当你从某个地方得到你想要的 html 时，它会很有用。字符串的 match()，但。match()因不良字符而引发错误。我们可以使用正则表达式这样做，并且仍然保留我们的 HTML 标签，如下所示:

```
//clean up string/HTML (remove bad chars but keep html tags)
rawData =  rawData.replace(/[^a-zA-Z 0-9]+/g,'');
```

如果我们想要更加具体，我们也可以删除其他不需要的通用字符:

```
///clean up HTML ready to be used with match() statement
rawData =  rawData.replace(/[^/\"_+-=a-zA-Z 0-9]+/g,'');
```

## cleanHTML()函数

我写了这个小函数来帮助清理 HMTL，为在其上使用正则表达式做好准备。

```
/* clean up HTML for use with .match() statement or regex */
var JQUERY4U = {};
JQUERY4U.UTIL = 
{
	cleanUpHTML: function(html) {
		html = html.replace("'",'"');
		html = html.replace(/[^/\"_+-?![]{}()=*.|a-zA-Z 0-9]+/g,'');
		return html;
	}
}
//usage: 
var cleanedHTML = JQUERY4U.UTIL.cleanUpHTML(htmlString);
```

[更多复制和粘贴正则表达式示例](http://www.jquery4u.com/syntax/jquery-basic-regex-selector-examples/)

## 分享这篇文章