# 增强 Javascript 的内置类型

> 原文：<https://www.sitepoint.com/enhancing-javascripts-built-in-types/>

Javascript 经常被错误地嘲笑为简单的语言，缺乏其他现代脚本语言所青睐的面向对象的特性。持有这种观点的人真的需要再看一看这种语言，因为在其初学者友好的内部 Javascript 打包了一些强大的语言特性。除了支持函数式编程(函数可以像它们操作的数据结构一样在脚本中传递)之外，Javascript 还支持一种被称为基于原型的继承的 OOP 形式。

web 上已经充斥着关于面向对象 Javascript 的教程，所以我不会重复这些教程，我只是想演示一个更简洁的方法:给 Javascript 的内置类型添加新的功能。以下是我最喜欢的例子之一:

`Array.prototype.indexOf = function(value) {
for (var i = 0; i < this.length; i++) { if (this[ i] == value) { return i; } } return -1; }`

上面的代码向 Javascript 数组类型添加了一个名为 indexOf 的全新方法，该方法接受一个值并返回包含该值的数组的第一个索引，如果在数组中没有找到该值，则返回-1。通过为 array 类的原型分配一个新属性，这个新属性(在本例中是一个函数)就可以用于所有其他 Array 对象，甚至是那些已经创建的对象。

另一个适合增强的类是 String 类。下面是一个简单的 trim()函数，它返回一个去掉了任何前导和尾随空格的字符串副本:

`String.prototype.trim = function() {
var match = /s*(.*?)s*/.exec(this);
return match[1];
}`

SitePoint 论坛上有一个帖子更详细地讨论了这种技术。感谢 [Octal](https://www.sitepoint.com/forums/member.php?u=26099) 建议将此作为博客条目的主题。

## 分享这篇文章