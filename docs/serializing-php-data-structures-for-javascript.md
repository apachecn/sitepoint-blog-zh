# 为 Javascript 序列化 PHP 数据结构

> 原文：<https://www.sitepoint.com/serializing-php-data-structures-for-javascript/>

目前正在处理一些将 PHP 数据结构序列化为 Javascript 的代码，作为一种简单的数据交换方式。

通过生成 PHP 能够不序列化的字符串，从 Javascript 转换到 PHP 相当容易(见用户评论在[http://www.php.net/serialize](http://www.php.net/serialize))；

 `String.prototype.toPHP=function() {
var s = this
s=s.replace(/\/g, "\\")
s=s.replace(/"/g, "\"")
s=s.replace(/n/g, "\n")
s=s.replace(/r/g, "")
return 's:'+s.length+':"'+s+'";';
}`

它修改了 Javascript String 类，因此您可以对任何字符串调用 toPHP()，并获得它的 PHP 序列化表示。

另一方面，就我目前所拥有的，如果我有一个简单的 PHP 数组，比如；

 `$a = array('x','y');`

一个准备好被 Javascript“解包”的“序列化”字符串将是；

 `'new Function('var a = new Array();a[0]="x";a[1]="y";return a');'`

如果我在一个名为“serializedData”的 Javascript 变量中获得了该字符串(可能通过 [XMLHttpRequest](http://unstable.elemental.com/mozilla/build/latest/mozilla//extensions/dox/interfacensIXMLHttpRequest.html) 获取)，我可以像这样使用它；

 `// Creates a function object
unserializedDataFunc = eval(serializedData);`

`// Get back the data from the function object
unserializedData = unserializedDataFunc();`

将函数对象与 eval()结合使用的原因是为了避免 Javascript 中的所有命名冲突。不能 100%确定这是“序列化”Javascript 数据结构的最佳机制——转义引号可能是一个令人头疼的问题。它确实有助于保持完全匿名，直到我明确地把它赋给一个变量，但是有人有更好的方法吗？顺便说一下，我想避免所有的 XML 格式，在 PHP 和 Javascript 之间有尽可能少的“层”,保持低开销并且易于使用。

另一件棘手的事情是映射 PHP 数组，它既可以是索引的，也可以是关联的。

 `$a = array('x','y','z','a'=>1,'b'=>2);`

Javascript 数组对象只跟踪索引元素——我可以给它添加属性，例如

 `var a = new Array();
a.push('x');
a.push('y');
a.push('z');
a["a"] = 1;
a["b"] = 2;`

但是我需要一个索引元素的 for 循环和一个 for 循环..属性的循环中。用 Javascript 实现一个新的 PHPArray 类很有诱惑力，但我认为这可能会带来麻烦。不确定这是否真的是个问题，因为实际上很少需要迭代索引元素和关联元素，但是如果有人有更好的主意，我很乐意听听。

这里的一般任务，顺便说一句，是让 XUL 与 PHP 对话的简单机制。

## 分享这篇文章