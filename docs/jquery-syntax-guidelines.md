# 21 条最佳 jQuery 语法指南

> 原文：<https://www.sitepoint.com/jquery-syntax-guidelines/>

每个 jQuery 程序员都应该知道这些基本的语法规则，否则他们会死于可怕的键盘死亡。

仔细阅读，回归基础！

1.  左大括号总是以空格为前缀。ie–if(代替 if(
2.  逗号和冒号后一定要有空格。ie–param，param 或 var 定义变量
3.  尝试使用===而不是==来帮助比较
4.  **评论:**长评论要用/* … */
5.  **块:** if/else/for/while/try 总是有大括号，并且总是在多行上
6.  **函数调用:**总是在参数周围包含额外的空格。ie–foo(真)；
7.  **空检查:**使用=== null 或=== undefined
8.  **赋值:**赋值后面应该总是有一个分号。ie–var test 2 = false；
9.  **正则表达式 1:** 应该使用。测试()和。exec()。“弦”。不再使用 match()
10.  **正则表达式 2:** 使用正则表达式删除字符串中的空格:
    $('#title ')。val()。replace(/^s+|s+$/g，“)==”)
11.  **节点:** `.nodeName`应始终优先于`.tagName`使用
12.  **字符串:**字符串应该总是使用双引号而不是单引号
13.  **Switch:** 使用 Switch 语句减少代码并提高性能
14.  **链式选择器:**同时通过链式选择器。ie-$(”。someclass，h3”)
15.  **第一个和最后一个:**使用:First 和:Last 动态选择第一个和最后一个元素
16.  **偶数和奇数:**使用:Even 和:Odd 动态选择替代元素
17.  **放弃目标=_blank:** 在新窗口$('a[href^="http://"]'中打开所有链接。attr({ target:" _ blank " })；
18.  **Alert():** 使用 Alert(" hi ")；为了帮助调试代码
19.  **使用备忘单:** [示例备忘单](http://www.gscottolson.com/weblog/2008/01/11/jquery-cheat-sheet/ )
20.  **选择器上下文:**给你的选择器上下文。ie–var selectedItem = $(' # listItem '+I)；
21.  **IE 控件:**使用特定的 jQuery 修复 IE bug:if($ . browser . msie){//Internet Explorer 修复；}

来源:[http://docs.jquery.com/JQuery_Core_Style_Guidelines](http://docs.jquery.com/JQuery_Core_Style_Guidelines)

## 分享这篇文章