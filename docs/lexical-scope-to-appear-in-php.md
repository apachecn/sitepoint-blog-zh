# PHP 中出现的词法范围？

> 原文：<https://www.sitepoint.com/lexical-scope-to-appear-in-php/>

在我的上一篇文章的[中，我简单地提到 Wez Furlong 在三月份为](https://www.sitepoint.com/the-state-of-functional-programming-in-php/)[做了一个补丁](http://pastebin.ca/400952)，它允许使用本地语法来创建匿名函数。这可能会取代令人发指的`create_function`。

从那以后，我又在 php 内部列表中提到了 Wez 的原始补丁。这在上周引发了一些激烈的辩论。反对批准该补丁的主要理由似乎是，人们会期望静态作用域规则适用于匿名函数。毕竟类似的语言都是这样，支持匿名函数。人们只能假设，改变 PHP 来支持这一点，将是一项重大的任务。

看起来，其中一个是错误的。

今天，克里斯蒂安·塞勒在[发布了一个补丁](http://news.php.net/php.internals/34216)，允许匿名函数的词法范围。虽然还有一些细节，但总体来说似乎是可行的。不是让所有变量都遵循静态范围规则，而是引入了一个新的关键字(`lexical`)。它的工作方式类似于`global`，因为它必须被显式声明，而这些变量是在词法范围内的。这是它实际运行的样子:

```
 function getAdder($x) {
  return function ($y) {
    lexical $x;
    return $x + $y;
  };
}
$add2 = getAdder(2);
$add2(8); // return 10 
```

现在判断这是否会进入语言还为时过早。这仍然只是一个提议，还需要进一步的工作来完善，但至少在技术上是可行的。我们必须等等看。

否则，只剩下祝大家圣诞快乐了。

## 分享这篇文章