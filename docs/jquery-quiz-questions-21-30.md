# jQuery 测验问题 21-30

> 原文：<https://www.sitepoint.com/jquery-quiz-questions-21-30/>

继 [jQuery 测验问题 11-20](http://www.jquery4u.com/?p=18234) 之后，下面是问题 21-30。希望您可以学到一些以前不知道的关于 jQuery 的新知识。再次，如果你发现任何错误，请随时留下评论和更正。尽情享受吧！

## 问题 21

以下哪些值是 addClass 中 secondArgument 的有效值(' turnRed '，secondArgument)；函数，如果我们用 jQuery UI 库？

**答案**

*   “快”
*   慢的
*   1000 毫秒
*   1000 毫秒
*   Three thousand

**正确答案**

*   “快”
*   1000 毫秒
*   Three thousand

* * *

## 问题 22

是()function ___ 表达式的当前选择。

**答案**

*   检查
*   过滤
*   得到
*   相反的

**正确答案**
检查
API:【http://api.jquery.com/is】T4

* * *

## 问题 23

`$('tr.rowClass:eq(1)');`回报什么？

**答案**

*   一个元素集，它是第一个表格的第一行。
*   一个元素集，即第一个表格的第二行。
*   一组具有“rowClass:eq(1)”类的 tr 标签。
*   具有“eq(1)”类的一组 tr 标签。

**正确答案**
一个元素集合的第一个表的第二行是哪一个。
看答案在行动:【https://jsfiddle.net/jquery4u/EHbJq/3/】T4

* * *

## 问题 24

innerHeight 函数返回元素的内部高度，___ 边框，___ 边距，__ 填充。

**答案**

*   排除，排除，排除
*   包括、排除、包含
*   排除，排除，包括
*   包括，包括，包括

**正确答案**
排除，排除，包含
API:【http://api.jquery.com/innerHeight/】T4

* * *

## 问题 25

考虑下面的代码片段:

```
var message = 'Message';
$('#id1').bind('click', {
    msg: message
}, function (event) {
    alert(event.data.msg);
});
message = 'New message';
$('#id2').bind('click', {
    msg: message
}, function (event) {
    alert(event.data.msg);
});

```

如果您单击“id1”，警告框会显示什么？

**答案**

*   消息
*   新消息
*   没有任何东西
*   以上都不是

**正确答案**
留言
看答案在行动:【https://jsfiddle.net/jquery4u/fhaDf/】T4

* * *

## 问题 26

当 end()函数紧接在该函数之后时，下面哪个函数将返回一个空集？

**答案**

*   增加
*   儿童
*   过滤器
*   去除

**正确答案**
去掉
。remove()返回被归类为空集的 jQuery(document)。【https://jsfiddle.net/jquery4u/bScnP/】见答案

* * *

## 问题 27

对于以下代码所示的 ajaxStart(function())方法，以下哪一项似乎是正确的？

```
$("#div1").ajaxStart(function1());

```

**答案**

*   方法在发送 Ajax 请求之前附加一个要执行的函数。
*   方法附加一个函数，每当 Ajax 请求开始时就执行这个函数。
*   方法附加一个函数，每当 Ajax 请求开始时执行，并且还没有激活任何函数。
*   以上都不是。

**回答正确**
方法附加了一个函数，每当 Ajax 请求开始时执行，并且还没有激活。
API:【http://api.jquery.com/ajaxStart/ 

* * *

## 问题 28

考虑下面的代码片段:

```
$('#div1').html().replace(/bad/, "good");

```

**答案**

*   用“好”替换 div1 内部 html 中的“坏”字。
*   将 div1 内部 html 中包含“bad”的单词替换为“good”。
*   用“好”替换 div1 内部 html 中的第一个“坏”字。
*   将 div1 内部 html 中第一个包含“bad”的单词替换为“good”。

**正确答案**
将 div1 内部 html 中包含“bad”的第一个单词替换为“good”。
看答案在行动:【https://jsfiddle.net/jquery4u/95Kv8/1/】T4

* * *

## 问题 29

考虑下面的代码片段:

```
$.map(array1, function1);

```

以下哪个参数是函数 1 的有效参数？

**答案**

*   array1 中要翻译的元素的索引。
*   要翻译的项目。
*   和都
*   函数 1 没有参数。

**正确答案**
甲乙双方
API:【http://api.jquery.com/jquery.map/】T4

* * *

## 问题 30

以下哪些值是 eq()函数的有效参数？

**答案**

*   one
*   ‘2’
*   -1

**正确答案**
全部都是。
看答案在行动:【https://jsfiddle.net/jquery4u/EBcqN/2/】T4API:[http://api.jquery.com/eq/](http://api.jquery.com/eq/)

* * *

## 分享这篇文章