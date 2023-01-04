# pTest:9 行代码的 PHP 单元测试器

> 原文：<https://www.sitepoint.com/ptest-php-unit-tester-in-9-lines-of-code/>

我最近在开发一个命令行 PHP 工具，不容易使用我们围绕 [SimpleTest](http://simpletest.org/) 构建的普通 PHP 单元测试框架。在几行非[测试驱动开发](http://en.wikipedia.org/wiki/Test-driven_development)之后，我开始有点抓狂——我想我已经陷入了这样的观点，如果代码没有测试，它就是坏的。

我不需要支持[模拟对象](http://en.wikipedia.org/wiki/Mock_object)或复杂的断言——只需要一个简单的基础`assertTrue()`就可以了。所以，我用 9 行代码呈现“pTest”:

```
 /**
 * pTest - PHP Unit Tester
 * @param mixed $test Condition to test, evaluated as boolean
 * @param string $message Descriptive message to output upon test
 */
function assertTrue($test, $message)
{
	static $count;
	if (!isset($count)) $count = array('pass'=>0, 'fail'=>0, 'total'=>0);

	$mode = $test ? 'pass' : 'fail';
	printf("%s: %s (%d of %d tests run so far have %sed)n",
		strtoupper($mode), $message, ++$count[$mode], ++$count['total'], $mode);
} 
```

这里有一些人为的测试案例来演示:

```
 assertTrue(1 + 1 == 2, 'one plus one should equal two');
assertTrue(false, 'false should be true (this one will fail)');
assertTrue(!false, 'false should be false'); 
```

和示例输出:

 <samp>通过:一加一应该等于二(到目前为止运行的 1 个测试中的 1 个已经通过)
失败:假应该为真(这个将失败) (到目前为止运行的 2 个测试中的 1 个已经失败)
通过:假应该为假(到目前为止运行的 3 个测试中的 2 个已经通过)</samp> 

如果您在 web 浏览器中查看输出，您可以很容易地将每个输出消息包装在一个

标签中。或者，如果你使用的是非[崩溃的浏览器](http://support.microsoft.com/kb/329661)，你可以用`header('Content-Type: text/plain');`来提供响应。

显然，这是不要太认真。然后，我发现深入到我正在处理的几个命令行脚本中非常有用。

我看到的这样一个简单测试函数的另一个用途是将外行引入单元测试的世界。我知道在我进入这个领域之前，最大的障碍是框架的复杂性。也许 pTest 会给初学者一个起点，一旦他们超越了简陋的`assertTrue()`，他们就可以从这个起点转移到功能更丰富的框架上，如 [PHPUnit](http://phpunit.de/) 或 [SimpleTest](http://simpletest.org/) 。

## 分享这篇文章