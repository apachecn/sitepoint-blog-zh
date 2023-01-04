# 15 个 JavaScript 字符串函数

> 原文：<https://www.sitepoint.com/15-javascript-string-functions/>

这里列出了所有基本的 JavaScript 字符串函数，供您参考。它们包括用于基本字符串操作的函数，对于 jQuery 脚本非常有用。

## 运行中的 JavaScript 字符串函数示例

**1。charAt(x)** 返回字符串中“x”位置的字符。

```
//charAt(x)
var myString = 'jQuery FTW!!!';
console.log(myString.charAt(7));
//output: F
```

**2。charCodeAt(x)** 返回字符串中位置“x”处字符的 Unicode 值。

```
//charAt(position)
var message="jquery4u"
//alerts "q"
alert(message.charAt(1))
```

**3。concat(v1，v2，...)**将一个或多个字符串(参数 v1，v2 等)合并到现有的字符串中，并返回合并后的字符串。原始字符串未被修改。

```
//concat(v1, v2,..)
var message="Sam"
var final=message.concat(" is a"," hopeless romantic.")
//alerts "Sam is a hopeless romantic."
alert(final)
```

**4。fromCharCode(c1，c2，...)**返回使用指定的 Unicode 值序列(参数 c1，c2 等)创建的字符串。方法的字符串对象，而不是字符串实例。例如:String.fromCharCode()。

```
//fromCharCode(c1, c2,...)
console.log(String.fromCharCode(97,98,99,120,121,122))
//output: abcxyz
console.log(String.fromCharCode(72,69,76,76,79))
//output: HELLO
//(PS - I have no idea why you would use this? any ideas?)
```

另请参见:[JavaScript 字符代码的完整列表](http://www.jquery4u.com/javascript/character-codes/)

**5。indexOf(substr，[start])** 搜索并(如果找到)返回字符串中被搜索字符或子字符串的索引号。如果没有找到，则返回-1。“Start”是一个可选参数，指定字符串中开始搜索的位置。默认值为 0。

```
//indexOf(char/substring)
var sentence="Hi, my name is Sam!"
if (sentence.indexOf("Sam")!=-1)
alert("Sam is in there!")
```

**6。lastIndexOf(substr，[start])** 搜索并(如果找到)返回字符串中被搜索字符或子字符串的索引号。从头到尾搜索字符串。如果没有找到，则返回-1。“Start”是一个可选参数，指定字符串中开始搜索的位置。默认值为 string.length-1。

```
//lastIndexOf(substr, [start])
var myString = 'javascript rox';
console.log(myString.lastIndexOf('r'));
//output: 11
```

**7。match(regexp)** 根据正则表达式在字符串中搜索匹配项。如果没有找到匹配，它返回一个信息数组或 null。

```
//match(regexp) //select integers only
var intRegex = /[0-9 -()+]+$/;  

var myNumber = '999';
var myInt = myNumber.match(intRegex);
console.log(isInt);
//output: 999

var myString = '999 JS Coders';
var myInt = myString.match(intRegex);
console.log(isInt);
//output: null
```

另请参见: [jQuery RegEx 示例。match()](http://www.jquery4u.com/syntax/jquery-basic-regex-selector-examples/)

**8。replace(regexp/substr，replacetext)** 搜索并用替换的文本替换正则表达式(或子字符串)部分(match)。

```
//replace(substr, replacetext)
var myString = '999 JavaScript Coders';
console.log(myString.replace(/JavaScript/i, "jQuery"));
//output: 999 jQuery Coders

//replace(regexp, replacetext)
var myString = '999 JavaScript Coders';
console.log(myString.replace(new RegExp( "999", "gi" ), "The"));
//output: The JavaScript Coders
```

**9。search(regexp)** 测试字符串中的匹配项。它返回匹配的索引，如果没有找到则返回-1。

```
//search(regexp)
var intRegex = /[0-9 -()+]+$/;  

var myNumber = '999';
var isInt = myNumber.search(intRegex);
console.log(isInt);
//output: 0

var myString = '999 JS Coders';
var isInt = myString.search(intRegex);
console.log(isInt);
//output: -1
```

10。slice(start，[end]) 根据“start”和“end”索引参数返回字符串的子字符串，不包括“end”索引本身。“End”是可选的，如果没有指定，片段包括从“start”到字符串结尾的所有字符。

```
//slice(start, end)
var text="excellent"
text.slice(0,4) //returns "exce"
text.slice(2,4) //returns "ce"
```

**11。split(delimiter，[limit])** 根据指定的分隔符将一个字符串拆分成多个，并返回包含每个元素的数组。可选的“limit”是一个整数，允许您指定要返回的最大元素数。

```
//split(delimiter)
var message="Welcome to jQuery4u"
//word[0] contains "We"
//word[1] contains "lcome to jQuery4u"
var word=message.split("l")
```

**12。substr(start，[length])** 返回从“start”开始到指定字符数“length”的字符串中的字符。“Length”是可选的，如果省略，则假定一直到字符串的末尾。

```
//substring(from, to)
var text="excellent"
text.substring(0,4) //returns "exce"
text.substring(2,4) //returns "ce"
```

13。substring(from，[to]) 返回“from”和“to”索引之间的字符串中的字符，不包括“to”本身。“To”是可选的，如果省略，则假定一直到字符串的末尾。

```
//substring(from, [to])
var myString = 'javascript rox';
myString = myString.substring(0,10);
console.log(myString)
//output: javascript
```

**14。toLowerCase()** 返回所有字符都转换为小写的字符串。

```
//toLowerCase()
var myString = 'JAVASCRIPT ROX';
myString = myString.toLowerCase();
console.log(myString)
//output: javascript rox
```

15。toUpperCase() 返回所有字符都转换为大写的字符串。

```
//toUpperCase()
var myString = 'javascript rox';
myString = myString.toUpperCase();
console.log(myString)
//output: JAVASCRIPT ROX
```

## 分享这篇文章