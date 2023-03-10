# jQuery 速度测试:$(这个)。attr(" id ")；vs this.id

> 原文：<https://www.sitepoint.com/jquery-speed-test-this-attrid-this-id/>

从[速度测试$(this) vs .get() vs .eq()](http://www.jquery4u.com/?p=16718) 开始，我决定做一个快速测试来比较$(this)的速度。attr(" id ")；与 this.id 相比，this . id 可用于 id、src、href、style 等本机属性，但不能用于 bgcolor、data 等属性

## 背景

在上下文中，这种情况会发生变化，但一般来说:

$(this)是一个 jQuery 对象，它可以访问所有的 jQuery API
。这是对 DOM 元素的引用

对尚未创建的 DOM 元素使用$(this)。

## 速度

总的来说，没有 jQuery 包装器的情况下会稍微快一些。

**$(本)。attr(" id "):42 毫秒
this . id:1 毫秒**

查看结果:[https://jsfiddle.net/jquery4u/F9rP7/](https://jsfiddle.net/jquery4u/F9rP7/)

延伸阅读:

*   [jQuery 的这次揭秘](http://remysharp.com/2007/04/12/jquerys-this-demystified/)

## 分享这篇文章