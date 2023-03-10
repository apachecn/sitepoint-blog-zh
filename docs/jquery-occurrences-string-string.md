# jQuery 获取一个字符串中出现的所有字符串

> 原文：<https://www.sitepoint.com/jquery-occurrences-string-string/>

在这个例子中，您可以获得网页上出现的所有电子邮件地址。如果您必须更改网站特定页面或子部分的电子邮件地址，这很有用。可能有用的屏幕刮电子邮件地址从网页上了，虽然这不是我在这里试图实现的。查看更多 [jQuery。每个例子]( http://www.jquery4u.com/jquery-functions/jquery-each-examples/)。

I=不敏感(大小写)
**g** =全局搜索
**m** =匹配多行

```
var iframeSrc = 'test.html?param=value&email=noobs@noob.com&moreparams=values';
var emailRegex= /[._a-zA-Z0-9-]+@[._a-zA-Z0-9-]+/igm;

console.log(iframeSrc);
console.log(emailRegex.test(iframeSrc));
console.log(iframeSrc.match(emailRegex));

$.each(iframeSrc.match(emailRegex), function(index, value) { 
     console.log(index + ". " + value);
});
//output: 0\. noobs@noob.com
```

这将查找在<securequery>标签:</securequery>之间的所有 html

```
var secureQueryRegex = /(<securequery>)/igm;
/* loop for each query found in data */
$.each(data.match(secureQueryRegex), function(index, value) {
	console.log(index + ". " + value);
});
//example output: 
//0\.</securequery> <securequery id="abc" template="abc">query=find all shops</securequery>
//1\. <securequery id="xyz" template="xyz">query=find all locations</securequery>
//etc...
```

## 分享这篇文章