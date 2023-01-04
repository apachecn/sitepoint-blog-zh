# 使用正则表达式验证电子邮件地址的 JavaScript

> 原文：<https://www.sitepoint.com/javascript-validate-email-address-regex/>

这是一个基本 JavaScript 代码片段，使用一个[正则表达式](https://www.sitepoint.com/learn-regex/)来验证电子邮件地址。这是建立在我之前关于[如何在 jQuery](http://www.jquery4u.com/javascript/basic-javascript-regular-expression/) 中使用正则表达式的文章之上的。你也可以在下面的 jsfiddle 中加载代码。

**2013 年 12 月 5 日更新:**分成多个版本进行测试。

## 版本 1

[https://jsfiddle.net/jquery4u/5rPmV/show/](https://jsfiddle.net/jquery4u/5rPmV/show/)

```
var $email = $('form input[name="email'); //change form to id or containment selector
var re = /^(([^<>()[]\.,;:s@"]+(.[^<>()[]\.,;:s@"]+)*)|(
".+"))@(([[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}])|(([a-zA
-Z-0-9]+.)+[a-zA-Z]{2,}))$/igm;
if ($email.val() == '' || !re.test($email.val()))
{
    alert('Please enter a valid email address.');
    return false;
}
```

[在 js dild 中加载代码](https://jsfiddle.net/jquery4u/5rPmV/)

## 版本 2

[https://jsfiddle.net/jquery4u/T2uLp/show/](https://jsfiddle.net/jquery4u/T2uLp/show/)

```
var $email = $('form input[name="email'); //change form to id or containment selector
var re = /[A-Z0-9._%+-]+@[A-Z0-9.-]+.[A-Z]{2,4}/igm;
if ($email.val() == '' || !re.test($email.val()))
{
    alert('Please enter a valid email address.');
    return false;
}
```

[在 js dild 中加载代码](https://jsfiddle.net/jquery4u/T2uLp/)

### 版本

```
//reported to validate incorrectly: t123@.co.in as true
/[A-Z0-9._%+-]+@[A-Z0-9-]+.+.[A-Z]{2,4}/igm

//reported to validate incorrectly: andre@uol.com@ as true
/[A-Z0-9._%+-]+@[A-Z0-9.-]+.[A-Z]{2,4}/igm

//current version
/^(([^<>()[]\.,;:s@"]+(.[^<>()[]\.,;:s@"]+)*)|(
".+"))@(([[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}])|(([a-zA
-Z-0-9]+.)+[a-zA-Z]{2,}))$/
```

## 分享这篇文章