# PHPUnit 5 概述——新特性？什么来了？

> 原文：<https://www.sitepoint.com/an-overview-of-phpunit-5-whats-new-whats-coming/>

*这篇文章已经过[cláudio Ribeiro](https://www.sitepoint.com/introduction-to-sitepoints-peer-review/)和 [Thomas Punt](https://www.sitepoint.com/author/tpunt/) 的同行评审。非常感谢让 SitePoint 的内容永远保持优秀的评论者！*

* * *

PHPUnit 开发团队[宣布](https://github.com/sebastianbergmann/phpunit/wiki/Release-Announcement-for-PHPUnit-5.0.0)PHPUnit 5.0 发布的时间是 2016 年 1 月。

![Color pen on paper, putting checkmarks into some boxes and red X-es into others](img/059dd7b73be6fdc41edca07dc36f905c.png)

虽然此后已经发布了几个次要版本，但 PHPUnit 的主要版本 5 引入了几个新功能，并取消了其他几个功能。在本文中，我们将看看最显著的变化。

## 要求

PHPUnit 5 需要 PHP 5.6。他们已经放弃了对 PHP 5.3、5.4 和 5.5 的支持，因为根据他们的发布过程，PHP 项目并不积极支持它们。你可以在这里看到所需 PHP 扩展的完整列表。

## 新的断言方法

栈中增加了三个断言方法，包括`assertFinite()`、`assertInfinite()`和`assertNan()`。

`assertFinite()`断言期望值是一个**有限的**值，这意味着它是一个合法的数字，在测试运行的平台上 PHP 浮点允许的范围内。在幕后，它使用 PHP 的内置函数 [is_finite](http://php.net/manual/en/function.is-finite.php) ，来检查是否满足约束。

考虑以下测试:

```
<?php
class FiniteTest extends PHPUnit_Framework_Testcase
{
    public function testFinite()
    {
        $this->assertFinite(INF);
    }
} 
```

上面的测试会失败，因为`INF`是一个 PHP 常量，代表一个无穷大的值。

`assertInfinite()`验证实际值是否是一个无穷大的数(正数或负数)，这意味着一个太大的值不适合当前平台上的 float。这个方法使用 PHP 的 [is_infinite](http://php.net/manual/en/function.is-infinite.php) 函数。

```
<?php
class InfiniteTest extends PHPUnit_Framework_Testcase
{
    public function testInfinite()
    {
        $this->assertInfinite(INF);
    }
} 
```

上面的断言给我们开了绿灯，因为`INF`是一个无穷大的值。

第三个断言是`assertNan()`，如果预期值不是一个`NAN`值，它将失败。
你可能知道，`NAN`的意思是:**不是数字**，是 PHP 中一个无效计算的结果，像`acos(8)`。

```
<?php
class NanTest extends PHPUnit_Framework_Testcase
{
    public function testNan()
    {
        $this->assertNan(14);
    }
} 
```

上面的测试也失败了，因为实际值是`14`，而期望值是`NAN`。

以上所有方法都采用相同的参数:第一个参数是实际值，第二个(可选)是断言失败时要打印的消息。

## 传入对象的深度克隆

在 PHPUnit 之前的版本中，传递的对象(通过使用`@depends`)按原样传递，这意味着它是对实际对象的引用(不是克隆)。考虑以下代码:

```
<?php

class Foo
{
    public $bars = [];
}

class Bar
{
    public $name;

    function __construct($name)
    {
        $this->name = $name;
    }
}

class FooTest extends PHPUnit_Framework_TestCase
{
    public function testProducer()
    {
        $foo = new Foo();
        $foo->bars[] = new Bar('James');
        $foo->bars[] = new Bar('Kirk');
        $foo->bars[] = new Bar('Rob');

        $this->assertEquals(3, count($foo->bars));
        $this->assertEquals('Rob', $foo->bars[2]->name);

        return $foo;
    }

    /**
     * @depends testProducer
     */
    public function testFirstDependent($foo)
    {
        $foo->bars[0]->name = 'Lars';
        $this->assertEquals('Kirk', $foo->bars[1]->name);
    }

    /**
     * @depends testProducer
     */
    public function testSecondDependent($foo)
    {
        $this->assertEquals('James', $foo->bars[0]->name);
    }
} 
```

故事是这样的:我们有一个名为`Foo`的类，它存储了类`Bar`的实例。在我们的测试类中，我们有三种测试方法:`testProducer()`、`testFirstDependent()`和`testSecondDependent()`。后两种方法依赖于`testProducer()`的返回值。

当使用`@depends`时，`$foo`是通过引用传递的，这意味着两个依赖测试接收相同的对象实例。这意味着在一个测试方法中对对象的任何修改都会影响另一个测试方法中的对象。

也就是说，`testFirstDependent()`把`$foo->bars[0]->name`变成了`James`。这导致`testSecondDependent()`失败，因为`$foo->bars[0]`已经被`testFirstDependent()`修改。

这是 PHPUnit 版本 4.8.24 中的测试结果:

```
PHPUnit 4.8.24 by Sebastian Bergmann and contributors.

..F

Time: 75 ms, Memory: 5.00Mb

There was 1 failure:

1) FooTest::testSecondDependent
Failed asserting that two strings are equal.
--- Expected
+++ Actual
@@ @@
-'James'
+'Lars'

/var/www/phpunit-example/tests/FooTest.php:47

FAILURES!
Tests: 3, Assertions: 4, Failures: 1. 
```

从 PHPUnit 5.0 开始，传递的对象可以通过使用`@depends clone`进行深度克隆。如果我们运行上面的测试(从 5.0 版本开始使用`@depends clone`注释)，我们会得到一个绿灯，因为每个测试都在修改和声明它自己的被传递对象的副本。

```
<?php

// ...

class FooTest extends PHPUnit_Framework_TestCase
{
    public function testProducer()
    {
        $foo = new Foo();
        $foo->bars[] = new Bar('James');
        $foo->bars[] = new Bar('Kirk');
        $foo->bars[] = new Bar('Rob');

        $this->assertEquals(3, count($foo->bars));
        $this->assertEquals('Rob', $foo->bars[2]->name);

        return $foo;
    }

    /**
     * @depends clone testProducer
     */
    public function testFirstDependent($foo)
    {
        $foo->bars[0]->name = 'Lars';
        $this->assertEquals('Kirk', $foo->bars[1]->name);
    }

    /**
     * @depends clone testProducer
     */
    public function testSecondDependent($foo)
    {
        $this->assertEquals('James', $foo->bars[0]->name);
    }
} 
```

下面是 PHPUnit 版本 5.3.2 的测试结果:

```
PHPUnit 5.3.2 by Sebastian Bergmann and contributors.

...                                                                 3 / 3 (100%)

Time: 86 ms, Memory: 3.50Mb

OK (3 tests, 4 assertions) 
```

## 传递带有期望的模拟对象

从 PHPUnit 5.0 开始，我们可以将模拟对象**连同它们的**期望**(一个期望断言某个方法何时以及如何被调用)一起传递给依赖的测试方法。这在 PHPUnit 5.0 之前的版本中是不可能的。此外，我们可以将模拟对象传递给相关的测试方法，但是没有任何期望。考虑下面的例子:**

```
<?php
class fooTest extends PHPUnit_Framework_TestCase
{
    public function testProducer()
    {
        $fooMock = $this->getMock('Foo');

        $fooMock->expects($this->once())
                ->methods('bar')
                ->will($this->returnValue(true));

        return $fooMock;
    }

    /**
     * @depends clone testProducer
     */
    public function testDependant($mock)
    {
        // This will fail in versions prior to 5.0.0 as the actual value will be NULL
        $this->assertTrue(true, $mock->bar())
    }
} 
```

上述测试在 PHPUnit 之前的版本中会失败，因为我们期望的是`true`，而实际值是`null`。原因是`testProducer`方法返回的模拟对象没有预期，这意味着所有方法都将返回`null`。

```
PHPUnit 4.8.24 by Sebastian Bergmann and contributors.

.F

Time: 74 ms, Memory: 5.00Mb

There was 1 failure:

1) MockTest::testDependent
Failed asserting that null is true.

/var/www/phpunit-example/tests/MockTest.php:31

FAILURES!
Tests: 2, Assertions: 2, Failures: 1. 
```

PHPUnit 5 会给我们开绿灯，因为所有的模拟都按照他们的期望通过了。

```
PHPUnit 5.3.2 by Sebastian Bergmann and contributors.

..                                                                  2 / 2 (100%)

Time: 57 ms, Memory: 3.75Mb

OK (2 tests, 2 assertions) 
```

## 代码覆盖率的白名单文件现在是强制性的

从 PHPUnit 5.0 开始，必须为代码覆盖分析配置一个白名单。在 5.0 之前的版本中，我们只收到一条警告，指出没有为代码覆盖配置白名单，但报告仍然会生成:

```
PHPUnit 4.8.24 by Sebastian Bergmann and contributors.
Warning:    No whitelist configured for code coverage

...........

Time: 2.42 seconds, Memory: 15.50Mb

OK (11 tests, 27 assertions)

Generating code coverage reports in HTML format ... done 
```

从版本 5.0 开始，我们得到一个错误，并且不会生成代码覆盖率:

```
PHPUnit 5.3.2 by Sebastian Bergmann and contributors.

Error:         No whitelist configured, no code coverage will be generated 
```

我们应该始终在`phpunit.xml`中配置白名单:

```
<filter>
  <whitelist processUncoveredFilesFromWhitelist="true">
    <directory suffix=".php">/path/to/files</directory>
    <file>/path/to/file</file>
    <exclude>
      <directory suffix=".php">/path/to/files</directory>
      <file>/path/to/file</file>
    </exclude>
  </whitelist>
</filter> 
```

## 创建无构造函数模拟的便捷方式

在 PHPUnit 5 中，我们可以通过使用`getMockWithoutInvokingTheOriginalConstructor()`方法直接创建一个带有禁用构造函数的 mock。这个方法实际上是`disableOriginalConstructor()`方法的包装器:

```
<?php
public function getMockWithoutInvokingTheOriginalConstructor($originalClassName)
    {
        return $this->getMockBuilder($originalClassName)
                    ->disableOriginalConstructor()
                    ->getMock();
    } 
```

根据上面的源代码我们可以这样使用它:

```
<?php
class MockTest extends PHPUnit_Framework_TestCase
{
    // ...
    protected $mock;
    public function setUp()
    {
        $this->mock = $this->getMockWithoutInvokingTheOriginalConstructor('Foo');
        $this->mock->expects($this->any())
             ->method('bar')
             ->will($this->returnValue(true));
    }
    // ...
} 
```

## 命令行中的新选项

PHPUnit 5 在命令行中引入了几个选项。其中一个选项是`--reverse-list`，它允许测试失败被反向排序，所以第一个失败被打印在底部。当几个测试依赖于一个测试通过时，这个选项真的很方便，如果我们修复了第一个，剩下的可能会自动解决。

另一个新选项是`--whitelist`，为代码覆盖分析配置一个白名单:

```
phpunit --whitelist /path/to/php/files 
```

还有另一个选项，在某些情况下可能会很方便:`--atleast-version`。
该选项以一个数字作为 PHPUnit 的最低版本，并检查当前版本是否大于该值。只有安装了特定版本的 PHPUnit，我们才能使用它来运行测试:

```
phpunit --atleast-version 5.3 && phpunit --whitelist path/to/php/files 
```

## 移除的功能

PHPUnit 4 中标记为不推荐使用的功能在版本 5 中被删除。

下列断言已被删除:

*   `assertSelectCount()`
*   `assertSelectRegExp()`
*   `assertSelectEquals()`
*   `assertTag()`
*   `assertNotTag()`

配置文件中的`--strict`命令行选项和`strict`属性不再存在。

`PHPUnit_Selenium`不再捆绑在 PHAR 发行版中，并且配置文件中的所有相关设置都已删除。

要查看添加、更改和删除的功能和错误修复的完整列表，您可以访问此页面。

## 包扎

PHPUnit 极大地改变了一些特性，从而让一些没有准备好的开发人员感到困惑——我们希望我们的帖子有助于澄清这些问题。一些断言已经被添加到堆栈中，其他一些断言已经被弃用。PHPUnit 5 已经放弃了对 5.6 之前 PHP 版本的支持。PHPUnit 6 已经排到 2017 年 2 月 3 日，不再支持 PHP 5。

你已经升级到 PHPUnit 5 了吗？如果您有任何意见或建议，或者如果我们在本文中遗漏了什么，请在评论中告诉我们！

## 分享这篇文章