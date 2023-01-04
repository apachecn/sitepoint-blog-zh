# jQuery 字符串包含函数

> 原文：<https://www.sitepoint.com/jquery-string-functions/>

查找一个字符串是否包含另一个字符串可以不使用 jQuery 而是使用普通的 ole JavaScript 来实现！

这是你怎么做的。

```
if (str.indexOf("Yes") >= 0)
```

注意这是区分大小写的。如果你想要一个不区分大小写的搜索，你可以写

```
if (str.toLowerCase().indexOf("yes") >= 0)
//OR
if (/yes/i.test(str))
//OR
//You could use search or match for this.
str.search( 'Yes' )
```

检查一个字符串是否包含另一个字符串的另一个例子。

```
if (v.indexOf("http:") == -1) 
{
  //string doesn't contain http
}
```

这将返回匹配的位置，如果没有找到，则返回-1。

## 分享这篇文章