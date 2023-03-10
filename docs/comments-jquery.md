# 如何在 jQuery 中进行注释

> 原文：<https://www.sitepoint.com/comments-jquery/>

如何在 jQuery 中使用注释，使用插件自动添加注释，在不卸载注释的情况下有效地使用注释。

## 基本 jQuery 注释

jQuery 注释以双正斜杠开始，它告诉解释器该行后面的文本不能作为 JavaScript 处理。jQuery 注释可以在函数内部，也可以在函数外部，这没有区别。

```
function myFunction ( myVar, myVar2 ) {
// Pay attention to spaces around
// the brackets and curly brackets
}
```

```
// some prefere it like this
function myFunction()
{
...
}
```

## 多行 jQuery 注释

```
Multi-line jQuery comments are enclosed within a combination of a forward slash and an asterisk like so:
 /*注释*/

## jQuery 注释指南

下面是一些在 jQuery 中注释的提示:

1.  长注释应该使用/*...*/.

2.  单行注释应该总是在他们自己的行上，并且在他们引用的行的上方。此外，在它上面应该有一个
    额外的端线。例如:

```

```
var some = "stuff";

   // We're going to loop here
   for ( var i = 0; i 
Source: http://docs.jquery.com/JQuery_Core_Style_Guidelines
```

