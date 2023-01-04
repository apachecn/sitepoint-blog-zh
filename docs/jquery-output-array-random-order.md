# jQuery 以随机顺序输出数组

> 原文：<https://www.sitepoint.com/jquery-output-array-random-order/>

只是一些 jQuery 片段的一些分享，以随机顺序显示/排序。

## 排序代码片段

在[对象文字格式](http://www.jquery4u.com/javascript/jquery-function-namespacing-plain-english/)中使用该函数。

```
shuffleAds: function(arr)
{
  for(var j, x, i = arr.length; i; j = parseInt(Math.random() * i), x = arr[--i], arr[i] = arr[j], arr[j] = x);
  return arr;
}
```

[观看演示](http://www.jquery4u.com/jquery-functions/display-blog-ads-spots-random-order/)

另一个功能来做同样的事情。

```
function randsort(c) {
    var o = new Array();
    for (var i = 0; i  0 ) --i;
        else o.push(n);
    }
    return o;
}
```

我也认为这个 jQuery Shuffle 插件值得加入。

```
/*
 * jQuery shuffle
 *
 * Copyright (c) 2008 Ca-Phun Ung <caphun at="" yelotofu="" dot="" com="">
 * Dual licensed under the MIT (MIT-LICENSE.txt)
 * and GPL (GPL-LICENSE.txt) licenses.
 *
 * http://yelotofu.com/labs/jquery/snippets/shuffle/
 *
 * Shuffles an array or the children of a element container.
 * This uses the Fisher-Yates shuffle algorithm <http:>
 */

(function($){

    $.fn.shuffle = function() {
        return this.each(function(){
            var items = $(this).children().clone(true);
            return (items.length) ? $(this).html($.shuffle(items)) : this;
        });
    }

    $.shuffle = function(arr) {
        for(var j, x, i = arr.length; i; j = parseInt(Math.random() * i), x = arr[--i], arr[i] = arr[j], arr[j] = x);
        return arr;
    }

})(jQuery);</http:></caphun>
```

## 分享这篇文章