# jQuery each not last 元素

> 原文：<https://www.sitepoint.com/jquery-element/>

jQuery 代码片段遍历元素，但不遍历最后一个元素。可能对元素有用，但不是最后一个。也可以使用类似于
的选择器

```
.not(":last")
```

它将页面上选择框的值添加到一个数组中，然后使用。concat()函数将这些值连接成一个字符串。

```
var  controlitems = $('select');
    controlfilters = [],
    controlitemslength = controlitems.length;
controlitems.each(function (i,v)
{
   var filtervalue = $(this).val();

   if (filtervalue !== "")
   {
      controlfilters.push(filtervalue);
   }
});
console.log([].concat(controlfilters));
```

**输出**
输出![ids](img/a277c79eec338a9eb5f4b8c17263d9ca.png "ids")

## 分享这篇文章