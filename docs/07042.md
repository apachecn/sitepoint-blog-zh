# 在 jQuery 示例中使用散列 URL

> 原文：<https://www.sitepoint.com/hash-urls/>

如今，许多现代网络应用程序使用**散列 URL**来增加页面的独特性(如页面标签、部分、动作等),无需刷新即可识别。这是继[使用 jQuery](http://www.jquery4u.com/snippets/url-parameters-jquery/) 获取 URL 参数，通过 Url 将动态数据传递给页面之后的又一步。它仍然在万维网上广泛使用。

## window.location.hash vs document。统一资源定位器

让我们快速浏览一下，下面是一个正则表达式，你可以用它来获取散列标签。

```
//using window.location.hash
$.fn.urlHash = function()
{
  return window.location.hash.replace('#','');
};
$.urlHash();
```

**重要提示:location.hash 对于 IE** (包括 IE9)是不安全的。此外，如果您的页面包含 iframe，那么在 iframe 内部手动刷新后，内容将获得旧的 location.hash 值(用于第一次页面加载),而手动检索的值不同于 location.hash，因此最好通过 document.URL 检索它。

```
//IE Proof - URL Hash Grab - returns complete hash value
$.fn.urlHash = function()
{
  return document.URL.substr(document.URL.indexOf('#')+1);
};
$.urlHash();
```

因此，作为提取 dayofweek 散列标签值的一个示例，您可以这样做:

```
//in context - extract dayofweek hash
//eg  url#dayofweek1 would return 1
if (document.URL.indexOf('#dayofweek'))
{
    week = parseInt(document.URL.substr(document.URL.indexOf('#')+1).replace('dayofweek',''))-1;
    $resParent.eq(week).showResources();
}
```

## 另一种体面的方式

这是另一种使用更重的正则表达式的好方法(磅符号是可选的，因为。match()从不返回 null)。

```
var match = location.hash.match(/^#?(.*)$/)[1];
if (match)
{
   //do stuff...
}
```

## 失败…

```
var hash = location.hash.match(/#(w+)/)[1];
```

**问题:**当散列中有任何非拉丁或非字母数字字符时，返回错误结果。例如，对于散列#foo@o#bar$%huh hello，只返回“foo”。location.hash 为空时引发 TypeError，因为。match()将返回 null

```
var hash = location.hash.split('#')[1];
```

对于相同的测试散列，它至少会得到“foo@o”部分，这意味着它只在散列包含井号时才会失败。当没有散列时，它不会抛出错误，尽管它返回 undefined 而不是空字符串。

## 参考材料

[获取您的哈希——防弹之路](http://lea.verou.me/2011/05/get-your-hash-the-bulletproof-way/)

## 分享这篇文章