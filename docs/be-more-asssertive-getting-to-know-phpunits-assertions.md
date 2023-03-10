# 更加自信:了解 PHPUnit 的断言+

> 原文：<https://www.sitepoint.com/be-more-asssertive-getting-to-know-phpunits-assertions/>

在我的[上一篇文章](https://www.sitepoint.com/tutorial-introduction-to-unit-testing-in-php-with-phpunit/)中，我带您了解了 PHPUnit 单元测试的一些基础知识。我向您介绍了测试您的代码意味着什么，以及它能为您和您的代码带来的真正好处。这次我想更深入一点，更详细地了解 PHPUnit。

## 标记跳过或未完成的测试

PHPUnit 包括两种方法，如果使用正确，可以使您的测试生活稍微简单一些。两个方法，`markTestSkipped`和`markTestIncomplete`，允许你的测试有不同的结果，而不仅仅是通过或失败。`markTestSkipped`如果在运行测试的过程中遇到问题，会给你一点“提示”。

假设您正在测试在给定方法执行之后是否存在数据库记录。在一个完美的世界里，你总是能够连接到数据库。但是，如果你不能，会发生什么呢？你的测试肯定会失败，因为它找不到那一行。但是失败的测试可能会让你认为你的代码有问题，尽管可能没有问题。在`markTestSkipped`的帮助下，您可以简单地跳过当前测试运行中的特定测试。这里有一个例子:

```
 <?php

public function testThisMightHaveADb()
{
  $myObject->createObject();

  try {
    $db = new Database();
    $this->assertTrue($db->rowExists());
  } catch (DatabseException $e) {
    $this->markTestSkipped('This test was skipped because there was a database problem');
  }
}
?>
```

这里有三种可能的结果。首先，当然，如果一切都按预期运行，测试将通过。如果`rowExists`方法返回`false`，那么`assertTrue`也有可能失败。

但是最后，也是最有趣的，数据库对象可能抛出一个`DatabaseException`类型的异常。我们的`try` / `catch`块将捕捉到异常，这表明数据库不可用，因此测试的全部内容都没有意义。它没有失败，而是跳过了测试。“跳过”的结果仍然会出现在您的所有测试运行中，所以您仍然会意识到这个问题最终需要解决。

`markTestIncomplete`方法为我们提供了类似的功能，但是有所不同。它旨在为您提供一种将测试标记为不完整的方法。这意味着，如果您开始编写一个测试，并打算以后再回来完成它，您可以在测试结果中清楚地表明这不是应用程序代码的失败，而是一个不完整的测试。您甚至可以定义一条自定义消息，这样您就可以知道测试被跳过的原因:

```
<?php

public function testAreNotEnoughHours()
{
  $this->markTestIncomplete("There aren't enough hours in the day to have my tests go green");
  $trueVariable = true;
  $this->assertTrue($trueVariable);
}
?>
```

值得注意的是，这两种方法只是为了方便起见，不应该成为你编写草率测试的借口。您的应用程序的好坏取决于支持它的测试，所以一旦您完成了正在测试的主要功能，请确保重新访问任何未完成或跳过的测试。

## 断言，断言，断言

在上一篇文章中，我回顾了 PHPUnit 提供的一些常用断言。现在，我们将回顾它们，并在一些示例代码中将它们付诸实践，向您展示它们是如何使用的。

让我们从将要测试的示例类开始:

```
<?php

class Testable
{
  public $trueProperty = true;
  public $resetMe = true;

  public $testArray = array(
    'first key' => 1,
    'second key' => 2
  );

  private $testString = "I do love me some strings";

  public function __construct()
  {
  }

  public function addValues($valueOne,$valueTwo) {
    return $valueOne+$valueTwo;
  }

  public function getTestString()
  {
    return $this->testString;
  }
}

?>
```

让我们也建立我们的测试类。这将是我们放入其余测试的容器，这样它们就可以运行我们的`Testable`类:

```
 <?php

class TestableTest extends PHPUnit_Framework_TestCase
{
  private $_testable = null;

  public function setUp()
  {
    $this->_testable = new Testable();
  }

  public function tearDown()
  {
    $this->_testable = null;
  }

  /** test methods will go here */
}

?>
```

我们使用`setUp`和`tearDown`方法为我们的测试运行做准备。我在上一篇文章中简要地提到了这些方法，但是现在是时候使用它们了。`setUp`方法将在测试类中的所有测试之前运行，而`tearDown`将在所有测试方法之后运行。在这种情况下，我们只是创建了一个新的`Testable`类的实例，并存储它，这样我们就可以在每个测试方法中方便地访问它。

### 对还是错

现在我们已经有了一个要测试的类实例，让我们进入断言。PHPUnit 使得一次测试一点点应用程序变得非常简单。记住，单元测试的目的是测试代码的“单元”，而不是应用程序的整个流程。如果您在应用程序中跟踪路径，那么您可能做错了。当然，这个规则也有例外，但这是一个很容易遵循的规则。让我们从一些最简单的断言开始— `assertTrue`和`assertFalse`。

```
<?php

public function testTruePropertyIsTrue()
{
  $this->assertTrue($this->_testable->trueProperty,"trueProperty isn't true");
}

public function testTruePropertyIsFalse()
{
  $this->assertFalse($this->_testable->trueProperty, "trueProperty isn't false");
}

?>
```

我们在上一篇文章中看到了`assertTrue`和`assertFalse`，但是我们在这里增加了一个转折。看到那个方便的消息作为每个断言的第二个参数了吗？这允许您定义一个定制的消息，如果测试失败，就输出这个消息，而不是默认的 PHPUnit 输出，这个输出有时有点晦涩。“Failed asserting that is false”不会帮助您理解您的应用程序代码有什么问题，但是类似“无法创建用户”这样的消息更有帮助！

### 精确的

接下来，我们将看看一些更面向数学的断言。这些让你评估传入的变量，看看它们是如何关联的。为了便于说明，让我们将它们全部放入一个大测试中:

```
<?php

public function testValueEquals()
{
  $valueOne = 4;
  $valueTwo = 2;
  $this->assertEquals($this->_testable->addValues($valueOne,$valueTwo),6);
}
public function testValueGreaterThan()
{
  $valueOne = 4;
  $valueTwo = 2;
  $this->assertGreaterThan($valueTwo,$valueOne);
}
public function testLessThanOrEqual()
{
  $valueOne = 4;
  $valueTwo = 2;
  $this->assertLessThanOrEqual($valueTwo,$valueOne);
}
public function testAreObjectsEqual()
{
  $testTwo = new Testable();
  $this->_testable->resetMe = false;
  $this->assertEquals($this->_testable,$testTwo);
}

?>
```

由于方法的明确命名，上面的大部分代码都是不言自明的:您可以访问诸如`assertGreaterThan`、`assertLessThan`、`assertGreaterThanOrEqual`、`assertLessThanOrEqual`和`assertEquals`这样的方法。

第一个`assertEquals`调用演示了如何使用测试类中的方法来提供测试值。

第二个`assertEquals`调用演示了这个断言的一个非常有趣的用法。它不仅可以用来比较数值，还可以比较其他东西，比如对象。看到我们在测试中创建的那个`testTwo`对象了吗？我们已经将`resetMe`属性改为`false`而不是`true. The` `assertEquals`将对象作为一个整体来查看它们是否匹配。由于 resetMe 值已更改，这两个对象不匹配，因此断言失败。`assertEquals`还有其他一些技巧:它可以比较 DOMDocument 对象或数组。有关这些比较如何工作的更多细节，请查看 PHPUnit 文档中的[相关章节。](http://www.phpunit.de/manual/current/en/api.html#api.assert.assertEquals)

### 吊着你

除了 PHPUnit 附带的与数学相关的方法之外，还有一些专门用于处理字符串的方法。当然，请记住，PHP 本身提供了大量的字符串处理方法，所有这些方法都可以在测试中使用。这些 PHPUnit 断言只是为了让编写测试变得更加简单和干净。让我们来看看其中的几个:

```
<?php
public function testStringEnding()
{
  $testString = $this->_testable->getTestString();
  $this->assertStringEndsWith('frood',$testString);
}

public function testStringStarts()
{
  $testString = $this->_testable->getTestString();
  $this->assertStringStartsWith('hoopy',$testString);
}

public function testEqualFileContents()
{
  $this->assertStringEqualsFile('/path/to/textfile.txt','foo');
}

public function testDoesStringMatchFormat()
{
  $testString = $this->_testable->getTestString();
  $this->assertStringMatchesFormat('%s',$testString);
}

public function testDoesStringFileFormat()
{
  $testString = $this->_testable->getTestString();
  $this->assertStringMatchesFormatFile('/path/to/textfie.txt','foo');
}

?>
```

与数学方法一样，这些断言的命名清楚地表明了它们的作用。第一个和第二个方法查看字符串，看它是以给定的字符串开头还是结尾。`assertStringEqualsFile`更有趣一点:它检查你给它的文件，并测试它的内容是否等于给定的字符串。这省去了您必须调用`file_get_contents`来获取数据并亲自检查的麻烦。在上面的例子中，如果在给定的路径中有一个包含字符串`"foo"`的文件，测试就会通过。

接下来的两个方法，assertStringMatchesFormat 和 assertStringMatchesFormatFile，允许您在匹配技术上更细粒度一些。它们允许您定义匹配的模式，使用一组占位符，您可以在 PHPUnit 站点上找到这些占位符。这样，您可以验证您的值是否符合给定的格式，您可以将它作为字符串或文件提供。

### 更多相同的？

现在让我们看看另外两个在我的测试中经常派上用场的断言:`assertNull`和`assertSame`。他们都做的和他们名字描述的差不多，但是这里有一个例子来巩固你头脑中的想法。

```
<?php

public function testStringIsNotNull()
{
  $notANull = “i'm not a null!”;
  $this->assertNull($notANull);
}
public function testStringIsSame()
{
  $numberAsString = '1234';
  $this->assertSame(1234,$numberAsString);
}

?>
```

我们上面测试中的第一个断言将会失败。它在检查`$notANull`中的值是否是`NULL`，但它是一个字符串。assertNull 的优点是，在这种情况下，它比试图用`assertTrue`或`assertFalse`完成同样的检查更加清晰易读。

现在为了`assertSame`。你可能知道，PHP 是松散类型的，所以它认为数字`1234`和字符串`"1234"`是相等的。结果，`assertEquals(1234, "1234")`就过去了。然而，作为一个认真的编码者，你会非常小心你的数据类型，并希望确保给定的变量*与另一个变量*完全匹配，包括内容*和类型*。嗯，你很幸运，因为这正是`assertSame`所做的。因此，上例中的断言将失败，因为`1234`不是`the same`的`"1234"`。

### 混杂在一起

我将涉及 PHPUnit 中一些不太常见的断言。虽然您可能不会在您编写的每个测试中使用它们，但是当您确实需要它们时，它们会非常方便:

```
<?php

public function testArrayKeyExists()
{
	$this->assertArrayHasKey('first key',$this->_testable->testArray);
}
public function testAttributeExists()
{
	$this->assertClassHasAttribute('resetMe',get_class($this->_testable));
}
public function testFileIsReal()
{
	$this->assertFileExists('/path/to/file.txt');
}
public function testIsInstance()
{
	$this->assertInstanceOf('OtherClass',$this->_testable);
}

?>
```

我在这里介绍了四个新的断言，涵盖了广泛的特性——数组键、类属性、文件存在和对象类型。让我们一次带一个，感受一下他们做了什么。第一个断言只是从我们的样本类中检查数组，看看`"first key"`是否是它的有效数组键。我们已经给了我们的`Testable`类一个带有那个键的数组，所以这个测试成功通过了。接下来，感谢`assertClassHasAttribute`方法，我们可以检查我们的类是否有给定的属性。同样，这个断言顺利通过，因为我们的测试类具有`resetMe`属性。但是请注意，这并不是检查一个属性是否存在于一个给定的对象上，而是检查它是否在一个类中定义。

`assertFileExists`是另一个简单的方法——它只是查看本地文件系统，看看文件是否在那里。这与 PHP 文件系统方法遵循相同的规则——如果您不能访问它，断言将失败。最后，还有`assertInstanceOf`，这是一个快捷的方法来判断你正在处理的对象是否是一个给定类的实例。当然，这和`assertTrue($this->_testable instanceof OtherClass)`没什么区别，但是它更简洁，更容易阅读。

对于本文的最后一个断言，我们将看看最灵活的一个:

```
<?php

public function testDoesMatchRegex()
{
  $testString = $this->_testable->getTestString();
  $this->assertRegExp('/[a-z]+/',$testString);
}

?>
```

这是一个简单的例子(我敢肯定你在你的时代已经见过一些怪物正则表达式)，但它得到了要点。

## 最后

在第二篇文章中，我试图列出大多数更有用的断言，这样在开始测试之前，您可以对那里有更好的了解。做事情总有不止一种方法，但是这些内置的断言可以为您节省大量时间和麻烦。记住，为你的应用程序编写测试的目的不仅仅是说你有，或者达到 100%的代码覆盖率。编写好的测试可以让你在代码上工作，不用担心会引入新的 bug(或者重新引入旧的 bug)。最终，更好的测试会产生更好的代码。

## 分享这篇文章