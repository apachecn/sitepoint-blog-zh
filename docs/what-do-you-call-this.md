# 这叫什么:= >

> 原文：<https://www.sitepoint.com/what-do-you-call-this/>

就语言而言，PHP 比某些语言有更多的语法优势。像 [list(…)](http://www.php.net/manual/en/function.list.php) 这样深奥的构造随处可见，这让开发人员的工作变得更容易，但同时也让初学者犯了错误。

一个特别有用的构造是 [foreach 循环](http://www.php.net/foreach)，它提供了一种快速遍历数组(或者，从 PHP5 开始，[一个对象](http://www.php.net/manual/en/language.oop5.iterations.php))的方法:

```
 $arr = array(1, 2, 3, 4);
foreach ($arr as &$value) {
   $value = $value * 2;
} 
```

使用关联数组，您可以通过前面提到的一点语法技巧获得数组中每一项的键和值:

```
 $a = array(
   "one" => 1,
   "two" => 2,
   "three" => 3,
   "seventeen" => 17
);

foreach ($a as $k => $v) {
   echo "$k => $vn";
} 
```

好的，如果你经常编写 PHP 代码，我可能没有告诉你任何你不知道的事情。但问题是:在最后一个代码示例中，您如何称呼= >操作符？来吧——它必须有一个名字，对不对？事实证明，在 PHP 手册中并没有正式的名称记录……那么你们怎么称呼它呢？

[据报道](http://groups.google.com/group/comp.lang.php/browse_thread/thread/38c125b22241cce2)它被称为 PHP 源代码中的[【双箭头】](http://www.php.net/tokens)，但这仅仅比“那个小箭头”好一点点*你*觉得应该叫什么？

## 分享这篇文章