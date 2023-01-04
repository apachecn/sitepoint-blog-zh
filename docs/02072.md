# PHP 7 中异常处理的速成变化

> 原文：<https://www.sitepoint.com/a-crash-course-of-changes-to-exception-handling-in-php-7/>

*这篇文章由[托马斯·庞特](https://www.sitepoint.com/author/tpunt)、[尼克拉斯·凯勒](https://github.com/kelunik)和[尤尼斯·拉菲](https://www.sitepoint.com/author/yrafie/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

异常处理在最不寻常的情况下保存您的代码。PHP 7 引入了两个新的类来帮助开发者轻松处理错误，这也是我们在这篇文章中要关注的。在 PHP 7 中引入这些类之前，异常错误类是用来处理不同类型的错误的。

![Illustration of female leg stepping on rake with the word Oops in big letters, indicating pending accident](img/a6a0e31116c55776f8197fc214fca366.png)

## 可投掷类

[Throwable](http://php.net/manual/en/class.throwable.php) 是 **[异常](http://php.net/manual/en/class.exception.php)** 和 **[错误](http://php.net/manual/en/class.error.php)** 类分支出来的接口。这个特殊的类帮助您捕捉任何可抛出的错误，不管它们是异常还是错误。例如:

```
<?php
try {
    throw new Exception("This is an exception");
}
catch (Throwable $e) {
    echo $e->getMessage();
} 
```

或者新定义的`ParseError`:

```
<?php
try {
    $result = eval("2*'7'");
}
catch (Throwable $e) {
    echo $e->getMessage();
} 
```

执行这段代码后，您将得到一个 ParseError，因为**；”**在`eval()`里面不见了。

用户定义的类不能直接实现`Throwable`，而必须扩展实现`Throwable`的`Exception`。

## 误差等级

PHP 7 中的 [Error](http://php.net/manual/en/class.error.php) 类是一个新类型的类，它处理不同的错误——它们要么是致命错误，要么是类型错误，这个类只用于内部 PHP 错误。错误分为四个子类:

1.  [算术错误](http://php.net/manual/en/class.arithmeticerror.php)
2.  [类型错误](http://php.net/manual/en/class.typeerror.php)
3.  [ParseError](http://php.net/manual/en/class.parseerror.php)
4.  [断言错误](http://php.net/manual/en/class.assertionerror.php)

在升级到 PHP 7 之前要记住的一件事是，如果你已经定义了一个自定义的错误类，你必须确保在升级之前更改了名称。如果你不这样做，你会得到一个致命的错误。

下面我们就以上四类逐一讨论。

### 算术误差

执行数学运算时会出现此错误。例如，当您使用`intdiv()`时:

```
<?php

try {
    var_dump(intdiv(PHP_INT_MIN, -1));
}
catch (ArithmeticError $e) {
    echo $e->getMessage();
} 
```

你将会得到 PHP_INT_MIN 除以-1 的结果*，因为我们将它移动了一个负数。*

 *另一类， [`DivisionByZeroError`](http://php.net/manual/en/class.divisionbyzeroerror.php) ，也是从`ArithmeticError`延伸而来。在两种不同的情况下会引发此错误:

**注意**:只有和`PHP_INT_MIN`结合才会得到`-1`。

首先，如果你对一个数进行 0 的模运算:

```
<?php
try {
    $result = 5 % 0;
    echo $result;
}
catch (DivisionByZeroError $e) {
    echo $e->getMessage();
} 
```

如果您使用与上述相同的方法，将`%`改为`/`，您将得到一个警告，结果可能是`+INF`、`-INF`或`NAN`中的任何一个。如果您希望这导致一个异常，最好使用一个错误处理程序，将警告转换为抛出的异常。

但是，如果您执行以下代码，将会出现***DivisionByZeroError***异常:

```
<?php
try {
    $result = is_finite(1.0 / 0);
    if (in_array($result, [INF, NAN,-INF])) {
        throw new DivisionByZeroError('Division by zero error');
    }
}
catch (DivisionByZeroError $e) {
    echo $e->getMessage();
} 
```

另一个让你得到 ***除法运算*** 的方法是再次使用`intdiv()`。

**注意**:PHP.net 已经[报告了这个问题的一个 bug 报告。](https://bugs.php.net/bug.php?id=71306)

### TypeError

这个错误主要用于 PHP 7 中的标量类型声明。当您创建了特定数据类型的函数或变量，并试图保存不同数据类型的值时，将显示该错误。例如:

```
<?php
declare (strict_types = 1);
function add(int $a, int $b)
{
    return $a + $b;
}
try {
    echo add("3", "4");
}
catch (TypeError $e) {
    echo $e->getMessage();
} 
```

如果运行上面的代码，将会抛出一个`TypeError`并得到`must be of the type integer, string was given`错误。如果你运行上面没有`declare(strict_types=1);`的代码，你不会得到任何问题，结果将是 7，除非你把数字变成一个非数字的字符串。

### ParseError

当使用`eval()`插入新的代码行或使用包含语法错误的外部 PHP 文件时，会抛出该错误。在`ParseError`之前，当您的外部 PHP 文件或`eval()`中有语法错误时，您的代码会被破坏并显示一个致命错误。例如，假设我们有一个包含以下代码的 PHP 文件:

```
<?php
$a = 4
$result = $a * 5; 
```

我们在另一个 PHP 文件中调用它:

```
<?php
try {
    require "index3.php";
}
catch (ParseError $e) {
    echo $e->getMessage();
} 
```

执行这段代码时，显示的是`syntax error, unexpected end of file`而不是致命错误。在引入这个类之前，轻松处理语法和致命错误几乎是不可能的。

### 断言错误

在引入`AssertionError`类之前，当使用 assert_options()绑定自定义函数时，我们必须创建自己的函数来处理断言异常。只有当通过`assert()`做出的断言失败时，才会显示该错误。要使用它，首先需要在`PHP.ini`中配置 assert 指令:

1.  **Assert.exception:** 默认情况下，它的值为 **0** ，它只为对象生成一个警告，而不会显示错误。但是，当该值更改为 1 时，它将抛出一个异常或断言错误，可以被捕获。

2.  **Zend.assertions:** 默认为 **-1** ，这是针对生产模式的，即不会生成断言代码。当它被设置为 **1** 时，它将处于开发模式，在该模式下将生成并执行断言代码。当设置为 **0** 时，将生成断言代码，但不会在运行时执行。

例如，让我们做一个将会失败的断言。

```
<?php
try {
    assert(2 < 1, "Two is less than one");
}
catch (AssertionError $ex) {
    echo $ex->getMessage();
} 
```

当执行上面的代码时，您只会得到一个警告:`"assert(): Two is less than one failed"`并且您的异常不会被捕获，因为`assert.exception`是 0。为了让`AssertionError`捕捉断言异常，我们需要将`assert.exception`改为 1。因此，当您运行以下代码时:

```
<?php
ini_set('assert.exception', 1);
try {
    assert(2 < 1, "Two is not less than one");
}
catch (AssertionError $ex) {
    echo $ex->getMessage();
} 
```

您将看到的不是警告，而是捕获到一个错误，并且只显示一条错误消息，即“二小于一”

自从引入新的类以来，许多致命的和可恢复的致命错误都是从 Error 类继承而来的。不能保证您使用`set_exception_handler()`设置的自定义处理程序会捕捉到这些错误。因此，如果您想在代码中抛出一些自定义异常，您不必重置您的自定义处理程序，因为它现在可以通过使用`Throwable`来捕捉错误。

## 摘要

如果您使用的 PHP 版本早于 7，您应该在转换之前记住这些事情。保险起见，你也可以看看 [php 7 升级指南](http://php.net/manual/en/migration70.php)。

新的错误和扩展类给你带来了痛苦吗？他们在 PHP 7 中的介绍你喜欢还是不喜欢？如果有的话，你希望他们有什么改变？* 

## *分享这篇文章*