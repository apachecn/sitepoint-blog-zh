# jQuery 1.4.4 中的新特性

> 原文：<https://www.sitepoint.com/jquery-144-whats-new/>

jQuery 1.4 的第四个次要版本现在已经发布了——距离 1.4.3 发布还不到[个月。](https://www.sitepoint.com/jquery-143-whats-new/)

不出所料，1.4.4 版本主要是根据用户社区的反馈进行的一系列错误修复。只有一个新功能…

## jQuery.fadeToggle()

[。fadeToggle()](http://api.jquery.com/fadeToggle/) 方法提供淡入淡出动画。如果某个元素可见，则其不透明度会降低，当不透明度达到零时，display 属性会设置为 none，因此该元素会从页面布局中消失。

打电话。不可见元素上的 fadeToggle()使项目可见并淡入。

## 主要错误修复

如果您一直在努力使您的代码工作，您会很高兴地知道以下问题已经得到修复:

*   的。宽度()和。隐藏元素的高度()被正确报告(在某些情况下，以前的版本会返回负值)。这可能是大多数 1.4.3 到 1.4.4 更新兼容性问题的原因。
*   现在，在确定 Ajax 请求是本地请求还是远程请求时，主机名和协议名是不区分大小写的。
*   元素的计算 CSS 现在一致地返回“auto ”,而不是空字符串。
*   绑定到文档就绪事件的函数现在将触发一次(它触发了两次)。
*   的。removeData()故障已修复。
*   属性不等于选择器([foo！=bar])现在在 Firefox 中也能工作了。
*   Child(>)、next sibling (+)、previous sibling (~)选择器现在可以与伪选择器(如:last)结合使用。
*   。show()不会失败，如果。hide()最初是在隐藏元素上调用的。

## 链接

从以下网址获取 jQuery 1.4.4:

*   未压缩版本(179 kb)–
    [https://code.jquery.com/jquery-1.4.4.js](https://code.jquery.com/jquery-1.4.4.js)
*   缩小版(26kB gzipped/76kB non-gzipped)–
    [https://code.jquery.com/jquery-1.4.4.min.js](https://code.jquery.com/jquery-1.4.4.min.js)
*   微软 CDN-
    [http://ajax.microsoft.com/ajax/jquery/jquery-1.4.4.min.js](http://ajax.microsoft.com/ajax/jquery/jquery-1.4.4.min.js)
*   jQuery 1.4.4 文档—
    [http://api.jquery.com/category/version/1.4.4/](http://api.jquery.com/category/version/1.4.4/)

## 在 jQuery 1.5 上

下一个主要的 jQuery 版本将于 2011 年初发布。如果你迫切需要一个新的特性或者想要提出改进建议，你可以[创建一个新标签](http://bugs.jquery.com/newticket)并且[为版本 1.5](https://spreadsheets.google.com/viewform?formkey=dGRmVVlFWS05QW1rT3lNOHVCZjE5Tmc6MQ) 提名它。或者，请在下面留下您的评论，我们将确保您的评论得到传达。

## 分享这篇文章