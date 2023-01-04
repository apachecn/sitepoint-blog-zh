# 替换 JavaScript 中的字符串字符

> 原文：<https://www.sitepoint.com/replace-string-characters-javascript/>

看待这个问题有两种方式:

1.  在 JAVASCRIPT 代码中替换字符串**中的字符**
2.  替换网页的 HTML 内的字符串**中的字符**

## 替换 JavaScript 代码中的字符串

```
str = str.replace("find","replace")
```

## 替换网页 HTML 中的字符串

### 使用 jquery

```
var value = $("#text").val()+"";
value.replace(".", ":");
var val = $("#text").val();
```

```
$("#text").val(val.replace('.', ':'));
```

### 使用 JavaScript

```
var elmOperator = $("#" + elm.attr('id').replace('Include', 'Operator'));
var elmOperator = $("#" + elm.id.replace('Include', 'Operator'));
```

## 替代方法

## 。replaceWith(新内容)

### 使用 jquery

```
$('.third').replaceWith($('.first'));
```

### 使用 JavaScript

```
v var str="Visit BLOGOOLA!";
document.write(str.replace("BLOGOOLA", "JQUERY4U"));
```

## 替换表单选择选项中的值

```
//For <select multiple="multiple"> elements
$('input:text.items').val(function(index, value) {
 return value + ' ' + this.className;
```

## 替换 html 中的外来字符

```
var temp = new String('This fori<gn chars test st>ring... So?????');
document.write(temp + '<br/>');
temp =  temp.replace(/[^a-zA-Z 0-9]+/g,'');
document.write(temp + '<br/>');
```

来源:http://api.jquery.com/replaceWith/

## 分享这篇文章