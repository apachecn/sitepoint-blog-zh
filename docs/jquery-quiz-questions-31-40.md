# jQuery 测验问题 31-40

> 原文：<https://www.sitepoint.com/jquery-quiz-questions-31-40/>

继 [jQuery 测验问题 21-30](http://www.jquery4u.com/?p=18235) 之后，下面是问题 21-30。希望您可以学到一些以前不知道的关于 jQuery 的新知识。再次，如果你发现任何错误，请随时留下评论和更正。尽情享受吧！

## 问题 31

event.stopPropagation()函数的作用是什么？

**答案**

*   冒泡 DOM 树并在最近的父元素处停止。
*   冒泡 DOM 树，防止任何父处理程序被通知该事件。
*   冒泡 DOM 并阻止同一元素上的其他处理程序运行。

**正确答案**
使 DOM 树冒泡，阻止任何父处理程序被通知该事件。

看答案在行动:[http://www.jquery4u.com/function-demos/stoppropagation/](http://www.jquery4u.com/function-demos/stoppropagation/)
API:[http://api.jquery.com/event.stopPropagation/](http://api.jquery.com/event.stopPropagation/)

* * *

## 问题 32

jQuery。clone()函数默认做什么？

**答案**

*   创建匹配元素集的深层副本，不包括任何附加事件。
*   创建匹配元素集的浅层副本，不包括任何附加事件。
*   创建包含任何附加事件的匹配元素集的深层副本。
*   创建包含任何附加事件的匹配元素集的浅层副本。

**正确答案**
创建匹配元素集的深层副本，不包括任何附属事件。
API:【http://api.jquery.com/clone/ 

* * *

## 问题 33

jQuery。queue()函数可以用来？

**答案**

*   将动画功能排队，以便它们可以同步运行。
*   将动画函数排队，以便它们可以异步运行。
*   之间没有区别。队列()和。animate()回调函数。
*   jQuery 没有这样的功能。

**正确答案**
将动画函数排队，以便它们可以异步运行。

API:[http://api.jquery.com/queue/](http://api.jquery.com/queue/)

* * *

## 问题 34

这段代码是做什么的？

```
$(function() {
    //jQuery code
});

```

**答案**

*   当 DOM 准备好时运行 jQuery 代码。
*   封装 jQuery 代码，保护它不受其他代码的影响。
*   它本质上做的和$(window)一样。load()函数。
*   以上都有。

**正确答案**
以上皆是。
更多答案信息:【http://api.jquery.com/ready/】http://www . jquery 4 u . com/DOM-modification/types-document-ready/
API:

* * *

## 问题 35

统计数据显示，截至 2013 年 3 月，大约有多少网站在使用 jQuery？

**答案**

*   四千五百万
*   两千九百万
*   一千九百万
*   一千二百万

**正确答案**
1900 万
来源:【http://trends.builtwith.com/javascript/jQuery】T4

* * *

## 问题 36

统计数据显示，截至 2014 年 3 月，大约有多少网站在使用 jQuery？

**答案**

*   四千五百万
*   两千九百万
*   一千九百万
*   一千二百万

**正确答案**
4500 万
来源:【http://trends.builtwith.com/javascript/jQuery】T4

* * *

## 问题 37

关于 jQuery 库的最终大小，哪种说法是正确的？

**答案**

*   最终的 2.0.0 文件比 1.9.1 文件小 9%。
*   最终的 2.0.0 文件比 1.9.1 文件小 12%。
*   最终的 2.0.0 文件比 1.9.1 文件小 18%。
*   最终的 2.0.0 文件比 1.9.1 文件小 24%。

**正确答案**
最终的 2.0.0 文件比 1.9.1 文件小 12%。
来源:【http://blog.jquery.com/2013/04/18/jquery-2-0-released/】T4

* * *

## 问题 38

关于香草 jsfiddle.net 在线编码工具，什么是正确的？

**答案**

*   jsfiddle 支持 Firebug。
*   jsfiddle 支持 Chrome 开发工具。
*   jsfiddle 支持 Firebug 和 Chrome 开发工具。
*   jsfiddle 只支持 Firebug Lite。

**正确答案**
jsfiddle 只支持 Firebug Lite。
来源:【http://www.jquery4u.com/tools/firebug-jsfiddle/】T4

* * *

## 问题 39

jQuery 库的最初版本是什么时候？

**答案**

*   2006 年 6 月。
*   2006 年 7 月
*   2006 年 8 月。
*   2006 年 9 月。

**正确答案**
2006 年 8 月。
来源:【http://en.wikipedia.org/wiki/JQuery】T4

* * *

## 问题 40

下列关于的陈述中哪一项是错误的？最近的()，。父()和。parents()函数。

**答案**

*   。closet()从当前元素开始搜索。
*   。parents()从父元素开始搜索。
*   。parent()与相同。parents()，但返回一个元素。
*   。parent()在遇到第一个父元素时结束搜索。

**正确答案**
。parent()与相同。parents()，但返回一个元素。

的。parents()和. parent()方法类似，只是后者只在 DOM 树中向上移动一层。
API:[http://api.jquery.com/parent/](http://api.jquery.com/parent/)http://api.jquery.com/parents/ http://api.jquery.com/closest/

* * *

## 分享这篇文章