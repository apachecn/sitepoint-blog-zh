# 简单测试远程测试

> 原文：<https://www.sitepoint.com/simple-test-remote-testing/>

继续被[简单测试](http://www.lastcraft.com/simple_test.php)有多好而震惊。

出于杰夫[在这里](http://www.procata.com/blog/archives/2004/05/27/rephlux-and-php-memory-usage/)解释的原因，我们一直在寻找运行[WACT](http://wact.sourceforge.net)测试套件的方法，这种方法不会遇到 PHP 的内存限制，显而易见的解决方案是通过网络在更小的组中执行测试，这意味着 Apache 将为每个组创建一个新的子组，每个子组都有一个新的内存块可供消耗。

乍一看，这似乎是一项艰巨的任务，首先以某种易于解析的形式(如 XML)生成测试结果，然后让一些客户端能够充当“浏览器”，运行并解析测试输出。事实证明这并不困难；简单测试领先于游戏，打包了一个以 XML 格式交付测试结果的“报告器”和一个 RemoteTestCase 类，准备连接到一个 URL 并解析 XMLReporter 的输出。

这里有一个简单的例子(我假设你已经阅读了[优秀文档](http://www.lastcraft.com/simple_test.php))；

```
 /**
* The class I've written that I want to test
*/
class Example {
    var $message = NULL;

    function setMessage($message) {
        $this->message = $mssage; // Whoops
    }

    function getMessage() {
        return $this->message;
    }
}

if ( !defined ('SIMPLE_TEST') ) {
    define ( 'SIMPLE_TEST','path/to/simpletest/' );
}

// Include the main unit tester
require_once SIMPLE_TEST . 'unit_tester.php';

// Include the XMLReporter
require_once SIMPLE_TEST . 'xml.php';

/**
* The test class
*/
class TestOfExample extends UnitTestCase {

    var $Example;

    function TestOfExample() {
        parent::UnitTestCase('Test Of Example');
    }

    function setUp () {
        $this->Example = & new Example();
    }

    function tearDown() {
        unset($this->Example);
    }

    function testEmptyMessage() {
        $this->assertNull($this->Example->getMessage());
    }

    function testMessage() {

        $this->Example->setMessage('Hello World!');
        $this->assertEqual($this->Example->getMessage(),'Hello World!');

    }
}

// Create an instance of the test class
$T = &new TestOfExample();

// Run it using the XMLReporter
$T->run(new XMLReporter());
?></code><p>The test output from running this looks like;</p>

<code>
< ?xml version="1.0"?>
<run>
  <case>
    <name>Test Of Example</name>
    <test>
      <name>testemptymessage</name>
      <pass>[NULL] should be null at line 38</pass>
    </test>

    <test>
      <name>testmessage</name>
      <fail>
        Equal expectation fails as [NULL]
        does not match [String: Hello World!] at line 44
      </fail>
      <exception>
        Unexpected PHP error [Undefined variable:  mssage]
        severity [E_NOTICE] in [/home/hfuecks/public_html/test.php]
        line [6]
      </exception>
    </test>
  </case>
</run> 

```

在使您的测试结果对其他程序和环境可用方面，这已经是向前迈出的一大步，但是简单测试并没有就此止步。还有一个 RemoteTestCase 类，用于将 HTML report 的输出转换回普通的 HTML 报告；

```
 if ( !defined ('SIMPLE_TEST') ) {
    define ( 'SIMPLE_TEST','/path/to/simpletest/' );
}

// Include the RemoteTestCase
require_once SIMPLE_TEST . 'remote.php';

// Include the normal HTML reporter
require_once SIMPLE_TEST . 'reporter.php';

$T = &new RemoteTestCase('http://localhost/~hfuecks/test.php');
$T->run(new HTMLReporter()); 

```

如果你在为你的客户维护网站，这可能会非常有用。例如，您可以测试 PHP 的配置，以获得您的应用程序所需的设置，并在问题发生时捕捉由您的客户的主机提供商(他们刚刚升级了 PHP)引起的问题，使用测试用例如下；

```
 if ( !defined ('SIMPLE_TEST') ) {
    define ( 'SIMPLE_TEST','/path/to/simpletest/' );
}

// Include the main unit tester
require_once SIMPLE_TEST . 'unit_tester.php';

// Include the XMLReporter
require_once SIMPLE_TEST . 'xml.php';

/**
* The test class
*/
class TestOfPHPConfiguration extends UnitTestCase {

    var $Example;

    function TestOfPHPConfiguration() {
        parent::UnitTestCase('Test Of PHP Configuration');
    }

    // Test magic_quotes_gpc is off
    function testMagicQuotesGPC() {
        $this->assertEqual(ini_get('magic_quotes_gpc'),0);
    }

}

// Create an instance of the test class
$T = &new TestOfPHPConfiguration();

// Run it using the XMLReporter
$T->run(new XMLReporter()); 

```

通过一点 cron 自动化，(也许还有你自己的 EMailReporter)你可以让你的测试在有问题的时候通知你。防止脱发的好方法。

## 分享这篇文章