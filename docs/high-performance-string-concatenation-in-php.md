# PHP 中的高性能字符串连接

> 原文：<https://www.sitepoint.com/high-performance-string-concatenation-in-php/>

我们最近研究了 JavaScript 中[快速字符串连接的复杂性，所以我认为对 PHP 重复这个练习会很有用。这对您的应用程序可能更重要:在生成 HTML 页面时，大型字符串操作通常在服务器上处理。](/blogs/javascript-fast-string-concatenation)

标准 PHP 安装中没有特殊的库，字符串连接是使用点运算符实现的:

```
 $str = 'a' . 'b';
$str .= 'c'; 
```

你也可以使用[内爆](http://php.net/manual/en/function.implode.php)函数连接一个字符串数组(或者它的别名，[连接](http://uk.php.net/manual/en/function.join.php)):

```
 $str = implode(array('a', 'b', 'c')); 
```

如果你只是连接几个字符串，你应该使用最实用的方法。可读性和功能性总是比微不足道的性能提升更重要。

## 串联许多字符串

考虑以下功能相同的示例。第一个使用字符串串联运算符:

```
 // standard string append
$str = '';
for ($i = 30000; $i > 0; $i--) {
	$str .= 'String concatenation. ';
} 
```

第二个使用数组连接:

```
 // array join
$str = '';
$sArr = array();
for ($i = 30000; $i > 0; $i--) {
	$sArr[] = 'String concatenation. ';
}
$str = implode($sArr); 
```

哪个最快？

好消息是 PHP5 很快。我测试了 5.3 版本，你更有可能遇到内存不足而不是性能问题。但是，数组内爆方法的时间通常是标准串联运算符的两倍。连接字符串或构建数组需要相当长的时间，但内爆函数会加倍努力。

不出所料，PHP 针对字符串处理进行了优化，在大多数情况下，点运算符将是最快的连接方法。

## 分享这篇文章