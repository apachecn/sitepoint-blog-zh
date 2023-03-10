# jQuery 测验问题 11-20

> 原文：<https://www.sitepoint.com/jquery-quiz-questions-11-20/>

继 [jQuery 测验问题 1-10](http://www.jquery4u.com/?p=18102) 之后，下面是问题 11-20。希望您可以学到一些以前不知道的关于 jQuery 的新知识。再次，如果你发现任何错误，请随时留下评论和更正。尽情享受吧！

## 问题 11

使用 jQuery 检查哪个键被按下的正确方法是什么？

**答案**

```
    $('#txtValue').keypress(function (event) {
        $('#txtvalue').alert((event.keyCode));
    });

    ```

```
    $(‘#txtValue’).keypress(function (event) {
        alert(String.fromCharCode((event.keyCode)));
    });

    ```

```
    $(‘#txtValue’).keypress(function (event) {
        alert(fromCharCode((event.keyCode)));
    });

    ```

```
    $(‘#txtValue’).keypress(function (event) {
        $(‘#txtvalue’).alert((event.which));
    });

    ```

**正确答案**

```
$(‘#txtValue’).keypress(function (event) {
    $(‘#txtvalue’).alert((event.which));
});

```

http://api.jquery.com/keypress/

* * *

## 问题 12

考虑下面的代码片段:

```
$('#ul1 li').on('click', function1);
$('#ul1').after('
```

*   最后一项');

点击“lastLi”是否执行 function1？

**答案**

*   是
*   不

**正确答案**
否。after()函数将列表项添加到 UL 标签之外。

* * *

## 问题 13

```
$("ul#myId > li");
```

上面的语句返回什么？

**答案**

*   id 为“li”的一组标签。
*   一组包含类“李”的标签。
*   一组 li 标签，它们是具有“myId”类别的 ul 标签的子标签。
*   一组 li 标签，它们是具有“myId”id 的 ul 标签的子标签。

**回答正确**
一组 li 标签，它们是具有“myId”id 的 ul 标签的子标签。

* * *

## 问题 14

考虑下面的代码片段:

```
$('#table1').find('tr').filter(function (index) {
    return index % 3 == 0
}).addClass('firstRowClass');

```

上面代码片段的结果是 __。

**答案**

*   表 1 中顺序为 3n + 1 (n = 0，1，2，…)的行具有类 firstRowClass
*   表 1 中顺序为 3n (n = 1，2，…)的行具有类 firstRowClass
*   table1 的所有行都有 firstRowClass 类
*   表 1 中没有包含 firstRowClass 类的行

**正确答案**
表 1 中顺序为 3n + 1 (n = 0，1，2，…)的行有 class firstRowClass
看实际答案:【https://jsfiddle.net/jquery4u/zJW3B/】T4

* * *

## 问题 15

将一个元素移动到另一个元素的正确方法是什么？

**答案**

```
    $('#source').prependTo('#destination');
    ```

```
    $("#source").add("#destination");
    ```

```
    $("#source").html("#destination");
    ```

```
    $("#source").add().html().("#destination");
    ```

**正确答案**

```
$("#source").add("#destination");
```

* * *

## 问题 16

考虑下面的代码片段:

```
$('span.item').each(function (index) {
    $(this).wrap('*   项目');
});
```

本质上，这段代码是做什么的？

**答案**

*   将每个包含类项的 span 标记包装在 li 标记中。
*   将具有类项目的每个 span 标签插入到 li 标签中。
*   将项目插入到具有项目类的每个范围中。
*   用替换每个包含类项的 span 标记
*   项目

。

**回答正确**
将每个包含类项目的 span 标签包装在一个 li 标签中。
看答案在行动:【https://jsfiddle.net/jquery4u/gv2vq/】T4

* * *

## 问题 17

以下代码片段的结果是什么？

```
jQuery.unique([1, 2, 2, 3, 3, 1]);

```

**答案**

*   [1, 2, 3].
*   [3, 2, 1].
*   [1, 1, 2, 2, 3, 3].
*   以上都不是。

**正确答案**
【1，2，3】。
看答案在行动:【https://jsfiddle.net/jquery4u/gkJsP/】T4

* * *

## 问题 18

考虑下面的代码片段:

```
$('#table1').find('tr').hide().slice(10, 20).show();

```

这段代码的结果是什么？

**答案**

*   显示表 1 从第 11 行到第 20 行。
*   显示表 1 从第 10 行开始的 20 行。
*   删除表 1 第 10 到 20 行。
*   从第 10 行开始删除表 1 的 20 行。

**回答正确**
显示表 1 的第 11 至 20 行。
看答案在行动:【https://jsfiddle.net/jquery4u/MQjer/】T4

* * *

## 问题 19

```
$("div").find("p").andSelf().addClass("border");

```

该语句将类边框添加到 __。

**答案**

*   div 标签中包含的所有 p 标签
*   div 标签中的所有 div 标签和 p 标签
*   所有 div 标签
*   所有 p 标签

**正确答案**
div 标签中的所有 div 标签和 p 标签
看答案在行动:【https://jsfiddle.net/jquery4u/eUBup/】T4

* * *

## 问题 20

以下哪个语句返回一组包含“jQuery”的 p 标签？

**答案**

1.  ```
    $('p:contains(jQuery)');
    ```

2.  ```
    $('p:contains("jQuery")');
    ```

3.  ```
    $('p:has("jQuery")');
    ```

4.  1 和 2
5.  1 和 3

**正确答案**
1 和 2
看答案在行动:【https://jsfiddle.net/jquery4u/cAnHC/】T4

## 分享这篇文章