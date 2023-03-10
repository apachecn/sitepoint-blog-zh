# jQuery 从字符串中删除字符串

> 原文：<https://www.sitepoint.com/jquery-remove-string-string/>

可以这么说，这就是使用 jQuery 从字符串中删除字符串的方法。下面的例子有望阐明我的意思。它使用了 jQuery.grep()，这是一个非常酷的搜索功能——它有点像 PHP 的子字符串……有点像。

一如既往的只是复制、粘贴、运行利用 [Firebug](http://www.jquery4u.com/snippets/jquery-list-functions-firebug/) 进行测试和实验。

```
(function($) {
var myFruits = "Apples, Bananas, Mangos, Blackberries, Oranges";
myFruits = myFruits.replace(/bMangos(, |$)/gi, "");

var myFruits = "Apples, Bananas, Mangos, Blackberries, Oranges";
var result = $.grep(myFruits.split(', '), function(v) { return v != "Mangos"; }).join(', ');
console.log(result);

function filterOut(my_str, t) { //string, term
  return $.grep(my_str.split(', '), function(v) { return v != t; }).join(', ');
}
})(jQuery);

//output: Apples, Bananas, Blackberries, Oranges
```

## 分享这篇文章