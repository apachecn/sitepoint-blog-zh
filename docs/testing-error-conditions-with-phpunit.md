# 用 PHPUnit 测试错误条件

> 原文：<https://www.sitepoint.com/testing-error-conditions-with-phpunit/>

假设您正在维护使用 PHP 原生`trigger_error()`函数记录错误信息的代码。假设你正在使用 PHPUnit 为代码编写单元测试。

如果你参考 PHPUnit 手册，有一节是关于错误条件的[测试。它描述了 PHPUnit 如何实现自己的错误处理程序，将错误、警告和通知转换成异常，以及如何捕捉这些异常来处理这些类型的错误。](http://www.phpunit.de/manual/current/en/writing-tests-for-phpunit.html#writing-tests-for-phpunit.errors "Chapter 4\. Writing Tests for PHPUnit")

然而，根据您的代码的样子，您可能会遇到 PHPUnit 处理这个问题的方法的问题。本文将详细说明这个问题是什么，它如何影响您测试代码的能力，以及如何着手解决它。

## 有什么问题？

错误和异常的行为方式完全不同。与本文特别相关的事实是，如果传递给代码的[错误级别常数](http://php.net/manual/en/errorfunc.constants.php "PHP: Predefined Constants - Manual")不是致命错误的指示，代码可以在紧接着`trigger_error()`之后的点继续执行。当抛出异常时，执行将在 catch 块的开始处继续，该 catch 块被发现对应于该异常的类，它可能在抛出异常的点之后，也可能不在该点之后。

让我们来看看这些行为的一些例子。首先，错误。

```
<?php
error_reporting(E_ALL | E_STRICT);
echo "Before warningn";
trigger_error("Danger Will Robinson!", E_USER_WARNING);
echo "After warningn";
```

如果运行上述代码，您将得到以下输出:

```
Before warning
PHP Warning:  Danger Will Robinson! in /home/matt/error_handler.php on line 4
After warning
```

由此我们看到执行了`trigger_error()`调用后的 echo 语句。

现在，例外。

```
<?php
try {
    echo "Before exceptionn";
    throw new Exception("Danger Will Robinson!");
    echo "After exceptionn";
}
catch (Exception $e) {
    echo "In catch blockn";
}
```

输出是:

```
Before exception
In catch block
```

与错误的示例情况相反，抛出异常后的代码不会被执行。因为 PHPUnit 将错误转换为异常，所以错误在单元测试中的行为方式与异常相同。被触发的错误之后的任何代码在被测试时都不会被执行。

这是另一个例子:

```
<?php
function foo($param) {
    if (is_string($param)) {
        trigger_error(__FUNCTION__ . " no longer supports strings, pass an array", E_USER_NOTICE);
    }
    // do useful stuff with $param
    ...
}
```

对于错误到异常的转换，没有办法测试$param 是否完成了有用的工作，因为当错误被转换为异常时，代码将永远不会被执行。

## PHPUnit 行为的副作用

这种错误到异常的转换导致代码在开发和测试中的行为与在生产中的行为有所不同。这里有一个例子:

```
<?php
function error_handler($errno, $errstr) {
    throw new Exception($errstr);
}
set_error_handler("error_handler");

try {
    trigger_error("Danger Will Robinson!", E_USER_WARNING);
}
catch (Exception $e) {
    var_dump(error_get_last());
}
restore_error_handler();
trigger_error("Danger Will Robinson!", E_USER_WARNING);
var_dump(error_get_last());
```

这是它的输出:

```
NULL
PHP Warning:  Danger Will Robinson! in /home/matt/exception_converter.php on line 16
array(4) {
  ["type"]=>
  int(512)
  ["message"]=>
  string(21) "Danger Will Robinson!"
  ["file"]=>
  string(59) "/home/matt/exception_converter.php"
  ["line"]=>
  int(14)
}
```

第一个`var_dump()`调用输出 NULL，在此期间，将错误转换为异常的自定义错误处理程序生效。第二个`var_dump()`调用，在此期间 PHP 的默认错误处理程序生效，输出关于被触发的错误的信息。

注意，第一个`var_dump()`调用输出 NULL 并不是因为使用了定制的错误处理程序，而是因为该错误处理程序抛出了一个异常。如果本例中显示的错误处理程序没有这样做，那么第一个`var_dump()`调用将具有与第二个相同的输出。

## 解决方案

我们需要一个解决方案，允许被测试代码继续执行，同时还允许我们检查是否出现了错误情况。如上面的例子所示，允许代码继续执行可以通过使用一个定制的错误处理程序来完成，它不会将错误转换为异常。这个错误处理程序应该做的是捕获错误信息，以便以后用断言进行分析。这可能是这样的:

```
<?php
class MyTest extends PHPUnit_Framework_TestCase
{
    private $errors;

    protected function setUp() {
        $this->errors = array();
        set_error_handler(array($this, "errorHandler"));
    }

    public function errorHandler($errno, $errstr, $errfile, $errline, $errcontext) {
        $this->errors[] = compact("errno", "errstr", "errfile",
            "errline", "errcontext");
    }

    public function assertError($errstr, $errno) {
        foreach ($this->errors as $error) {
            if ($error["errstr"] === $errstr
                && $error["errno"] === $errno) {
                return;
            }
        }
        $this->fail("Error with level " . $errno .
            " and message '" . $errstr . "' not found in ", 
            var_export($this->errors, TRUE));
    }

    public function testDoStuff() {
        // execute code that triggers a warning
        $this->assertError("Message for the expected error",
            E_USER_WARNING);
    }
}
```

在每个测试方法之前运行的`setUp()`，处理错误处理器的设置，它只是同一个类中的另一个方法，在一个数组中存储关于每个错误的信息。然后像`assertError()`这样其他方法被像`testDoStuff()`这样的测试方法使用来执行针对错误信息的断言，并输出相关的调试信息，比如与预期的错误相比，触发了哪些错误。

其他有用的断言类型包括逻辑反转(即断言没有触发特定的错误)、用匹配正则表达式的消息检查错误，或者检查触发的错误的数量。

## 结论

如果您不在乎测试触发错误后的逻辑是否仍在执行，PHPUnit 的默认行为非常适合您的需要。然而，重要的是你要意识到这种行为的含义。

如果您确实关心这种逻辑的执行，那么同样重要的是，您知道如何补充 PHPUnit 的功能，以便在尽可能接近生产环境的条件下准确测试您的代码。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章