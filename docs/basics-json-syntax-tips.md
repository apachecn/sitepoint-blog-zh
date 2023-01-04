# 回到基础:JSON 语法和技巧

> 原文：<https://www.sitepoint.com/basics-json-syntax-tips/>

回到基础:快速回顾一下什么是 JSON 。

> JavaScript Object Notation (JSON)是一种用于结构化数据序列化的文本格式。它源自 JavaScript 的对象文字

JSON 可以表示四种基本类型(字符串、数字、布尔值和 null)和两种结构化类型(对象和数组)。

## JSON 简单对象示例

这是一个 JSON 对象，具有以下属性:

```
{
    "myObject":
	{
        "name":"obi wan kenobi",
        "weapons":"lightsaber",
        "specialPowers":"the force"
    }
}
[/code

[code lang="js"]
This is a JSON object which contains 
{
    "Africa":{

}
```

## JSON 简单数组示例

这是一个包含 JSON 数组的 JSON 对象:

```
{
    "myObject":
	{
        "name":"obi wan kenobi",
        "weapons": ["lightsaber","smoke grenade","knife","jedi things"],
        "specialPowers":"the force"
    }
}
```

这是一个包含两个对象的 JSON 数组:

```
{
	"africaLagos": [
	  {
		"from": -377711769600000,
		"to": -1588464816000,
		"dst": false,
		"offset": 816,
		"name": "LMT"
	  },
	  {
		"from": -1588464816000,
		"to": 253402300799000,
		"dst": false,
		"offset": 3600,
		"name": "WAT"
	  }
	]
}
```

更多 JSON 示例

使用 JSON 的一些技巧:

*   空白(空格、水平制表符、换行符或换行符或回车符)在 JSON 中无关紧要。它也可以在不影响数据的情况下缩小。
*   对象文字名称必须是小写的(如 null、false、true 等)。
*   将所有名称和值对放在引号中以避免
*   在使用之前验证你的 JSON——http://www.jsonlint.com
*   JSON 的默认 unicode 编码是 UTF-8(参见所有应用程序媒体类型 http://www . iana . org/assignments/Media-Types/Application/index . html)
*   JSON 文本的 MIME 媒体类型是 application/json(分别是类型和子类型)。延伸阅读:多用途互联网邮件扩展(MIME)http://en.wikipedia.org/wiki/MIME

## 在中解析 JSON

由于存在安全隐患，不建议盲目地用 eval()评估任何 JSON 字符串。最好使用 JSON.parse()方法，它是自 ES5 以来该语言的一部分，由现代浏览器中的 Javascript 引擎提供。

在 jQuery 中，有一个 parseJSON()方法:

```
// an input JSON string
var jstr = '{"mykey": "my value"}';
var data = jQuery.parseJSON(jstr);
console.log(data.mykey); // "my value"
```

JSON.parse()方法的反义词是 JSON.stringify()。它接受任何对象或数组(或原语)并将其序列化为 JSON 字符串。

```
var dog = {
    name: "Fido",
    dob:  new Date(),
    legs: [1, 2, 3, 4]
};
var jsonstr = JSON.stringify(dog);
// jsonstr is now:
// {"name":"Fido","dob":"2010-04-11T22:36:22.436Z","legs":[1,2,3,4]}
```

## JSON 字符串中必须转义的字符

*   引号"
*   正斜杠/
*   反斜杠\
*   新行 n
*   回车 r
*   标签 t

## 分享这篇文章