# jQuery.get()读取文本文件示例

> 原文：<https://www.sitepoint.com/jquery-read-text-file/>

JQuery 代码片段通过内置的 AJAX jQuery.get()调用读取文本文件，然后逐行处理 txt 文件。该示例将这些行添加到 html 元素中，以便在页面上显示。

**重要提示:**jQuery 代码将只替换第一次出现的单词(jQuery4u)，而不是像 PHP 的 str_replace()那样每次都替换。要替换 JavaScript 中出现的每一个字符串，必须为 replace()方法提供一个正则表达式，第一个参数是一个全局修饰符，如下:

```
.replace(/jQuery4u/g,'jQuery4u FTW!');
```

## jQuery 代码逐行读取文本文件

```
jQuery.get('file.txt', function(data) {
   alert(data);
   //process text file line by line
   $('#div').html(data.replace('n','
'));
});
```

**注意:**出于安全原因，浏览器限制对本地驱动器(和服务器驱动器)的访问。但是，您可以使用标准的 jQuery ajax 调用$。ajax()。

## 分享这篇文章