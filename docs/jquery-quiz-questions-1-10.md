# jQuery 测验问题 1-10

> 原文：<https://www.sitepoint.com/jquery-quiz-questions-1-10/>

在会员版块中很快会发布一个交互式的 jQuery 小测验，在那里你可以赢取奖品。与此同时，**这里有几个 jQuery 问题**，它们没有出现在测验中，但我认为与您分享它们会很好，可能会对您尝试学习 jQuery 或创建自己的测验有所帮助。嘿，你甚至可以学到一些你不知道的关于 jQuery 的东西。问题不少，下面是问题 1-10。尽情享受吧！

## 问题 1

您可以将 jQuery 事件附加到以下哪些元素？

**答案**

*   目标
*   把…嵌入
*   小应用程序
*   以上都不是

**回答正确**
以上都不是
不能将事件附加到对象、嵌入、applet 元素上。jQuery 的事件系统要求 DOM 元素允许通过元素上的属性附加数据，以便可以跟踪和交付事件。

* * *

## 问题 2

下列说法是对还是错？jQuery 的事件系统要求 DOM 元素允许通过元素上的属性附加数据，以便可以跟踪和提交事件。

**答案**

*   真实的
*   错误的

**正确答案**
正确

* * *

## 问题 3

下列说法是对还是错？jQuery 定义了 focusin 和 focusout 事件，它们是焦点和模糊事件的跨浏览器。

**答案**

*   真实的
*   错误的

**回答正确**
回答正确
W3C 规定焦点和模糊事件不冒泡，但是 jQuery 定义了跨浏览器的 focusin 和 focusout 事件。当使用 focus 和 blur 来附加委托事件处理程序时，jQuery 会映射名称，并分别以 focusin 和 focusout 的形式传递它们。为了保持一致和清晰，请使用冒泡事件类型名称。

* * *

## 问题 4

哪个代码更快？

**答案**

*   $('#test1，. test2，. test3，. test4 ')；
*   $('#test1 ')。添加(' . test2 ')。添加(' . test3 ')。add(' . test 4 ')；

**正确答案**
$('#test1，. test2，. test3，. test4 ')；//更快
jsperf:http://jsperf.com/multiple-jquery-id-selects/2
API:http://api.jquery.com/add/

* * *

## 问题 5

$.grep(array1，function 1)；

上面的语句 _ _ _ array 1 数组中满足 function1 函数的元素。

**答案**

*   分类
*   更新
*   相异的
*   过滤

**正确答案**
滤镜
API:http://api.jquery.com/jQuery.grep/

* * *

## 问题 6

考虑下面的代码片段:

*   项目 1
*   项目 2
*   项目 3

以下哪个代码片段返回的结果与$('#id1 li ')相同。not($(' # li2 '))；？

**答案**

*   $('#li2 ')。兄弟姐妹()；
*   $('#id2 ')。兄弟姐妹(“# li2”)；
*   $('#li2 ')。儿童()；
*   $('#id2 ')。孩子(' # li2 ')；

**正确答案**
$('#li2 ')。兄弟姐妹()；
参见行动回答:https://jsfiddle.net/jquery4u/pHtbq/

* * *

## 问题 7

函数的作用是:隐藏一个元素。

**答案**

*   将该元素的“显示”内联样式属性设置为“无”。
*   将该元素的“可见性”内联样式属性设置为“隐藏”。
*   将该元素的水平属性设置为"-100px "不在可见屏幕上。
*   将该元素的垂直属性设置为"-100px "不在可见屏幕上。

**正确答案**
将该元素的“显示”内联样式属性设置为“无”。
API:http://api.jquery.com/hide/

* * *

## 问题 8

使用 jQuery 创建带有链接文本“Hello”的 div 元素的正确方法是什么？

**答案**

*   $("#idName ")。创建(“div”)。正文("[你好](#)")；
*   $("#idName ")。创建(“div”)。html("[Hello](#)")；
*   $(“idName”)。css("div ")。html("[Hello](#)")；
*   $("#idName ")。追加("[Hello](#)")；

**正确答案**
$("#idName ")。追加("[你好](#)")；

* * *

## 问题 9

考虑下面的代码片段？

函数 function 1()
{
alert(arguments . length())；
}

当您运行 function1()时，下列哪一项是正确的；？

**答案**

*   因为变量 arguments 未定义，所以出现错误。
*   发生错误是因为您调用不带参数的 function1。
*   警告框显示“未定义”。
*   警告框显示 0。

**正确答案**
出现错误是因为你调用了不带参数的 function1。
未捕获的类型错误:对象#的属性“长度”

* * *

## 问题 10

$.extend(false，object0，object1，object 2)；

上面的代码是做什么的？

**答案**

*   通过将 object1 和 object2 与 object0 合并来扩展 object0。
*   通过将 object0 和 object2 与 object1 合并来扩展 object1。
*   通过将 object0 和 object1 与 object2 合并来扩展 object2。
*   该语句无效，因为其参数无效。

**正确答案**
通过将 object1 和 object2 与 object0 合并来扩展 object0。
API:http://api.jquery.com/jQuery.extend/

## 分享这篇文章