# 使用 PHP 三元运算符

> 原文：<https://www.sitepoint.com/using-the-ternary-operator/>

您可能已经熟悉 PHP 的`if`语句。它与许多其他编程语言中的对应物非常相似，代表了编程中最基本的概念之一。`if`语句通俗易懂，容易掌握。这可能是你所习惯的:

```
<?php
if ($coolFactor >= 10) {
    $message = "You are one cool dude!";
}
else {
    $message = "Sorry, you aren't that cool!";
}
```

但是有一种方法可以建立在这个概念上，并在这个过程中增加你的`$coolFactor`。请允许我向您介绍三元运算符，它是`if`语句的简写符号。

## 三元运算符简介

三元运算符由三部分组成，使用由问号和冒号分隔的三个表达式。问号跟在测试表达式后面，可以认为是在问:“嗯，是真的吗？”然后冒号分隔两个可能的值，如果测试表达式为真，将选择第一个值，如果测试表达式为假，将选择第二个值。观察:

```
<?php
$message = ($coolFactor >= 10) ? "You're one cool dude!" : "Sorry, you aren't that cool!";
```

我最喜欢使用三元运算符的一个地方是检查是否设置了值，如果没有，就将变量设置为默认值。

```
<?php
$favoriteColor = isset($_GET["color"]) ? $_GET["color"] : "pink";
```

如果一个颜色参数从 URL 传入脚本，它的值被分配给`$favoriteColor`。如果不是，则分配默认值“粉色”。

从 PHP 5.3 开始，可以通过排除中间表达式来简化三元语句。如果测试表达式在布尔上下文中评估为 true，则返回其值。否则，将返回替代项。

```
<?php
$favoriteColor = $_GET["color"] ?: "pink";
```

但是伴随着巨大的冷静而来的是巨大的责任！正确使用三元运算符可以使代码更简洁；滥用三元运算符会把事情搞得一团糟。永远不要牺牲代码的可读性或可维护性来增加亮点。

## 不要滥用！

在使用三元运算符之前，您应该考虑当前情况的复杂性。避免嵌套或堆叠您的操作，即使您习惯使用它们，因为这会导致非常混乱的代码和不直观的结果。对于复杂的情况，最好还是使用`if`语句。最重要的是，对下一个人好一点，尽量让你的代码简洁易懂。

将三元表达式拆分成多行也不是没有听说过。与编程中的大多数事情一样，有许多方法可以自由地使用空格来提高代码的可读性。

```
<?php
$message = $isWinner
    ? "Congratulations! You just won a whole bunch of money and prizes!"
    : "Sorry, you didn't get any money or prizes this time.";
```

一如既往，可读性应该是关键。

## 摘要

三元运算符起初可能看起来有点奇怪，但是很容易掌握，并且很容易向将来可能维护您的代码的其他人解释。通过一点点实践，你将能够给你的 PHP 代码带来额外的惊喜，并从你的生活中清除一点点困惑。

图片 via [阿曼·曾德](http://www.shutterstock.com/gallery-73477p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art") / [梭托](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章