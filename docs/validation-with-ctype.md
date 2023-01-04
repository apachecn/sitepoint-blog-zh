# 用 CType 验证

> 原文：<https://www.sitepoint.com/validation-with-ctype/>

当你把视线从球上移开时会发生什么？ [CType](http://www.php.net/ctype) 扩展成为 PHP 的默认部分！

我不得不承认，直到最近我才接触过它们，并且陷入了“验证=正则表达式”的思维模式。令我困惑的是，我也没见过其他人使用它们——大多数做一些验证的库，包括 [QuickForm](http://pear.php.net/HTML_Quickform) 和 [Validate](http://pear.php.net/Validate) 都使用正则表达式。有人知道为什么吗？使用它们并不难，但是对于旧版本的 PHP，还是要使用正则表达式。

CType 函数是 C 语言的一个标准部分 [ctype.h](http://www-ccs.ucsd.edu/c/ctype.html) 的包装器，因此被有效地测试过。

关键是，对于验证某些基本类型的数据，它们比正则表达式做得更好，既更快又支持国际字符集。这也让你的代码在本质上更具“声明性”——很清楚 [ctype_digit()](http://www.php.net/ctype_digit) 在'/(^-？dd*。d*$)|(^-？dd*$)|(^-？。dd*$)/'不太明显。

例如，假设您的表单中有一个“名字”字段。使用正则表达式，您可能会像这样验证它；

 `if ( !preg_match('/^[a-zA-Z]*$/', $first_name) ) {
die ('Alpha characters only please!');
}`

如果你的名字是乔，那没问题，但是如果你的名字是 H 呢

[](http://blog.phpvolcano.com/)

## 分享这篇文章