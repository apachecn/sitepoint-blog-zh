# PHP 中的类型提示

> 原文：<https://www.sitepoint.com/type-hinting-in-php/>

从 PHP 5 开始，您可以使用类型提示来指定函数声明中参数的预期数据类型。当您调用该函数时，PHP 将检查参数是否是指定的类型。否则，运行时将引发一个错误，执行将被暂停。

有效类型是接收对象的参数的类名和接收数组的参数的数组。这里有一个例子:

```
<?php
function enroll(Student $student, School $school) {
echo "Enrolling " . $student->name . " in " . $school->name;
}
```

通过告诉 PHP`enroll()`方法期望接收哪种类型的对象，您可以确保学生注册的是学校，而不是修道院或 401K。同样，你知道你不会有任何顽固的厚皮动物出现在三年级的第一天。

如果我尝试加入医疗保险会发生什么？

```
<?php
$me = new Student("Amanda");
$medicare = new Program("Medicare");
$enroll = enroll($me, $medicare);
```

虽然我是学生，但可能会发生以下错误:

```
Catchable fatal error: Argument 2 passed to enroll() must be an instance of School, instance of Program given, called in typehint.php on line 32 and defined in typehint.php on line 6
```

如果 null 用作参数的默认值，也是允许的。这里有一个例子，这次是用数组:

```
<?php
function startParty(array $guests, array $food = null) {
    // party stuff...
}
$guests = array("Susan Foreman", "Sarah Jane Smith", "Rose Tyler", "Donna Noble");
startParty($guests, null);
```

只要有客人，不管有没有吃的，都会有派对。

## 狩猎的局限性

任何已定义的类都可以是有效的类型提示，尽管 PHP 不支持泛型对象的类型提示。其他的呢？

下面是 PHP 类型提示局限性的一个特殊例子:

```
<?php
function stringTest(string $string) {
    echo $string;
}
stringTest("definitely a string");
```

```
Catchable fatal error: Argument 1 passed to stringTest() must be an instance of string, string given, called in typehint.php on line 42 and defined in typehint.php on line 39
```

你不是第一个想到“这是什么疯狂？我给了你一个字符串实例，你却抱怨它必须是字符串的实例！”没关系。它发生在我们最好的人身上。事实上，乍一看，这可能是一个非常令人困惑的错误消息。

`stringTest()`不是寻找一个字符串，而是寻找一个`string`类的实例。PHP 的类型提示不支持标量数据类型，如字符串或整数值。但是没关系！如果您需要在参数不是标量类型(如字符串或整数)时引发错误或异常，您可以使用像`is_string()`或`is_int()`这样的函数来执行基本的验证。

## 标量战争

关于在 PHP 5.4 中增加标量 PHP 类型提示，一直存在一些争议。反对改变的人认为这种支持将违背 PHP 的基本设计。PHP 被认为是一种弱类型语言。本质上，这意味着 PHP 不需要你声明数据类型。变量仍然有相关的数据类型，但是你可以做一些激进的事情，比如把一个字符串加到一个整数上，而不会产生错误。

2010 年 5 月，PHP 主干增加了对标量类型提示的支持。但是由于社区的反应，这个特性不会出现在 5.4 版本中。

## 摘要

类型提示是 PHP 中引入的一种面向对象编程技术(专门用于识别被捕获异常的类型)。我鼓励你在这里阅读更多关于使用对象的内容。

图片 via[Carlos e . Santa Maria](http://www.shutterstock.com/gallery-73309p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章