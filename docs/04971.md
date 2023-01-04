# 用 PsySH 进行交互式 PHP 调试

> 原文：<https://www.sitepoint.com/interactive-php-debugging-psysh/>

现在是凌晨 1:00，您的 web 应用程序交付的最后期限是 8 个小时…但它没有工作。当你试图弄清楚发生了什么时，你用`var_dump()`和`die()`填充你的代码，看看哪里有 bug

你很恼火。每当你想尝试一个返回值或变量赋值时，你必须改变你的源代码，执行你的应用程序，并看到结果…最后，你不确定你是否已经从代码中删除了所有的 var_dumps。这种情况你熟悉吗？

## 心理救援

[PsySH](https://psysh.org/) 是一个读取-评估-打印循环(或 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) )。您可能以前通过浏览器的 javascript 控制台使用过 REPL。如果您有，那么您知道它拥有强大的功能，在调试 JS 代码时非常有用。

说起 PHP，你可能之前用过 PHP 的交互控制台(`php -a`)。在那里，您可以编写一些代码，只要您按下 enter 键，控制台就会执行这些代码:

```
php -a
Interactive shell

php > $a = 'Hello world!';
php > echo $a;
Hello world!
php >
```

不幸的是，交互式 shell 不是 REPL，因为它缺少“P”(打印)。我必须执行一个`echo`语句来查看`$a`的内容。在一个真实的 REPL 中，我们会在给它赋值后立即看到它。

您可以使用`composer g require`全局安装 PsySH，或者下载 PsySH 可执行文件:

**作曲家**

```
composer g require psy/psysh:~0.1
psysh
```

**直接下载** (Linux/Mac)

```
wget psysh.org/psysh
chmod +x psysh
./psysh
```

此外，您可以使用 composer 将它包含在每个项目中，您将在本文后面看到这一点。

现在让我们来玩玩 PsySH。

```
./psysh                                                                                                                                             

Psy Shell v0.1.11 (PHP 5.5.8 — cli) by Justin Hileman                                                                                                                                                                              
>>>
```

主要的帮助会是你最好的朋友。它将为您提供各种命令及其解释:

```
>>> help

  help      Show a list of commands. Type `help [foo]` for information about [foo].      Aliases: ?

  ls        List local, instance or class variables, methods and constants.              Aliases: list, dir

  dump      Dump an object or primitive.

  doc       Read the documentation for an object, class, constant, method or property.   Aliases: rtfm, man 

  show      Show the code for an object, class, constant, method or property.

  wtf       Show the backtrace of the most recent exception.                             Aliases: last-exception, wtf?

  trace     Show the current call stack.

  buffer    Show (or clear) the contents of the code input buffer.                       Aliases: buf

  clear     Clear the Psy Shell screen.

  history   Show the Psy Shell history.

  exit      End the current session and return to caller.                                Aliases: quit, q
```

```
>>> help ls

Usage:

ls [--vars] [-c|--constants] [-f|--functions] [-k|--classes] [-I|--interfaces] [-t|--traits] [-p|--properties] [-m|--methods] [-G|--grep="..."] [-i|--insensitive] [-v|--invert] [-g|--globals] [-n|--internal] [-u|--user] [-C|--
category="..."] [-a|--all] [-l|--long] [target]

Aliases: list, dir

Arguments:

 target             A target class or object to list.

Options:

 --vars             Display variables.

 --constants (-c)   Display defined constants.

 --functions (-f)   Display defined functions.

 --classes (-k)     Display declared classes.

 --interfaces (-I)  Display declared interfaces.

 --traits (-t)      Display declared traits.

 --properties (-p)  Display class or object properties (public properties by default).

 --methods (-m)     Display class or object methods (public methods by default).

 --grep (-G)        Limit to items matching the given pattern (string or regex).

 --insensitive (-i) Case-insensitive search (requires --grep).

 --invert (-v)      Inverted search (requires --grep).

 --globals (-g)     Include global variables.

 --internal (-n)    Limit to internal functions and classes.

 --user (-u)        Limit to user-defined constants, functions and classes.

 --category (-C)    Limit to constants in a specific category (e.g. "date").

 --all (-a)         Include private and protected methods and properties.

 --long (-l)        List in long format: includes class names and method signatures.

 Help:

 List variables, constants, classes, interfaces, traits, functions, methods, and properties.

 Called without options, this will return a list of variables currently in scope.

 If a target object is provided, list properties, constants and methods of that target. If a class, interface or trait name is passed instead, list constants and methods on that class.

 e.g. 

 >>> ls
 >>> ls $foo
 >>> ls -k --grep mongo -i
 >>> ls -al ReflectionClass
 >>> ls --constants --category date
 >>> ls -l --functions --grep /^array_.*/
 >>>
```

基本上，REPL 能做的是:

```
>>> $a = 'hello';
=> "hello"
>>>
```

请注意，如果我们将 PsySH 与 PHP 的交互控制台进行比较，PsySH 会在赋值后立即打印出`$a`值。

一个更复杂的例子如下:

```
>>> function say($a) {
...     echo $a;
... }
=> null
>>> say('hello');
hello
=> null
>>>
```

我定义了函数`say()`并调用了它。你看到的那两个`null`是因为函数定义和它的执行都没有返回值(函数回显值)。另外，在定义功能时，提示从`>>>`变为`...`。

我们能定义一个类并实例化它吗？

```
>>> class Foo
... {
...     protected $a;
...
...     public function setA($a) {
...         $this->a = $a;
...     }
...
...     public function getA() {
...         return $this->a;
...     }
... }
=> null
>>> $foo = new Foo();
=> <Foo #000000001dce50dd000000002dda326e> {}
>>> $foo->setA('hello');
=> null
>>> $foo->getA();
=> "hello"
>>>
```

当我实例化 Foo 时，构造函数返回了对对象的引用。这也是 PsySh 打印`<Foo #000000001dce50dd000000002dda326e>`的原因。现在让我们看看 PsySH 和 objects 有什么有趣的地方。

```
>>> ls $foo
Class Methods: getA, setA
>>>
```

如果您忘记了 Foo 类定义了哪些方法，那么现在您已经有了答案。你用过 Linux 操作系统或者 Mac 命令行界面吗？那么您可能熟悉`ls`命令。还记得`-la`选项吗？

```
>>> ls -la $foo
Class Properties:

  $a   "hello" 

Class Methods:

  getA   public function getA()
  setA   public function setA($a)
```

很甜蜜，不是吗？

当与 web 应用程序集成时，PsySH 的真正威力大放异彩，所以让我们来构建一个。

## 演示应用程序

我将实现一个快速应用程序来展示[装饰设计模式](https://www.oodesign.com/decorator-pattern.html)。这样一个模式的 UML 类图如下:
![Decorator Design Pattern](img/fc6de7e7b13af2daad4251ffbd64b594.png)

如果你不太了解 [UML](https://en.wikipedia.org/wiki/Unified_Modeling_Language) 或者[设计模式](https://en.wikipedia.org/wiki/Software_design_pattern)，不要担心，理解它们并不是本文所必需的。

同样对于这个项目，我创建了一组测试用例。那些测试用例可以由 [phpUnit](https://phpunit.de/) 运行。同样，你不必熟悉[单元测试](https://en.wikipedia.org/wiki/Unit_test)来理解这篇文章。

这个小应用程序的完整源代码可以在 https://github.com/sitepoint-examples/PsySH 找到

首先，让我们定义我们的`composer.json`文件，以便声明对 **PsySH** 的依赖:

```
{
    "name": "example/psysh",
    "authors": [
        {
            "name": "John Doe",
            "email": "john@doe.tst"
        }
    ],
    "require": {
        "psy/psysh": "~0.1"
    },
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```

经过一段时间后，你应该可以走了。

请看下面来自文件`public/decorator.php`的源代码。它将实例化 SimpleWindow、DecoratedWindow 和 TitledWindow 对象来展示装饰器模式:

```
<?php
chdir(dirname(__DIR__));

require_once('vendor/autoload.php');

use Acme\Patterns\Decorator\SimpleWindow;
use Acme\Patterns\Decorator\DecoratedWindow;
use Acme\Patterns\Decorator\TitledWindow;

echo PHP_EOL . 'Simple Window' . PHP_EOL;

$window = new SimpleWindow();

echo $window->render();

echo PHP_EOL . 'Decorated Simple Window' . PHP_EOL;

$decoratedWindow = new DecoratedWindow($window);

echo $decoratedWindow->render();

echo PHP_EOL . 'Titled Simple Window' . PHP_EOL;

$titledWindow = new TitledWindow($window);

echo $titledWindow->render();
```

我们可以通过 PHP 的 CLI(命令行界面)或 web 服务器(如果配置了的话)来执行代码。我们也可以使用 PHP 的内部网络服务器。

### 在 cli 中调试

通过命令行界面执行上述代码将如下所示:

```
php public/decorator.php 

Simple Window
+-------------+
|             |
|             |
|             |
|             |
|             |
+-------------+

Decorated Simple Window
+-------------+
|             |
|             |
|             |
|             |
|             |
+-------------+

Titled Simple Window
+-------------+
|Title        |
+-------------+
|             |
|             |
|             |
|             |
|             |
+-------------+
```

怎样才能和 PsySH 互动？只需在代码中想要调试应用程序的任何地方添加`\Psy\Shell::debug(get_defined_vars());`，通常是您要插入`var_dump()`语句的地方:

```
<?php
chdir(dirname(__DIR__));

require_once('vendor/autoload.php');

//... a lot of code here

$titledWindow = new TitledWindow($window);

echo $titledWindow->render();

\Psy\Shell::debug(get_defined_vars()); //we want to debug our application here!
```

保存文件后，我们将获得以下输出:

```
php public/decorator.php 

Simple Window
+-------------+
|             |
|             |
|             |
|             |
|             |
+-------------+

Decorated Simple Window
+-------------+
|             |
|             |
|             |
|             |
|             |
+-------------+

Titled Simple Window
+-------------+
|Title        |
+-------------+
|             |
|             |
|             |
|             |
|             |
+-------------+

Psy Shell v0.1.11 (PHP 5.5.8 — cli) by Justin Hileman
>>>
```

脚本的执行将被暂停，我们现在可以使用 PsySH 的提示了。我将`get_defined_vars()`作为参数传递给`Psy\Shell::debug()`,这样我就可以访问 shell 中所有已定义的变量:

```
>>> ls
Variables: $_COOKIE, $_FILES, $_GET, $_POST, $_SERVER, $argc, $argv, $decoratedWindow, $titledWindow, $window
>>>
```

让我们检查一下`$window`变量:

```
>>> ls -al $window

Class Methods:
  render   public function render()  
>>>
```

在应用程序中使用 PsySH 的好处是我们可以检查实例化对象的源代码。

```
>>> show $window
class Acme\Patterns\Decorator\SimpleWindow implements Acme\Patterns\Decorator\Window
class SimpleWindow implements Window
{
    public function render()
    {
        $returnString = <<<EOL
+-------------+
|             |
|             |
|             |
|             |
|             |
+-------------+
EOL;
        return $returnString . PHP_EOL;
    }
}
>>>
```

所以，`$window`是 SimpleWindow 的一个实例，实现了窗口接口…不知道窗口接口的源代码是什么样子的…

```
>>> show Acme\Patterns\Decorator\Window
interface Acme\Patterns\Decorator\Window
interface Window
{
    public function render();
}
>>>
```

为什么 SimpleWindow 和 DecoratedWindow 的输出是一样的？让我们检查一下`$decoratedWindow`对象。

```
>>> ls -al $decoratedWindow

Class Properties:
  $windowReference   <Digitec\Patterns\Decorator\SimpleWindow #000000003643d67700000000731101b7>  

Class Methods:
  __construct          public function __construct(Digitec\Patterns\Decorator\Window $windowReference)         
  getWindowReference   public function getWindowReference()                                                    
  render               public function render()                                                                
  setWindowReference   public function setWindowReference(Digitec\Patterns\Decorator\Window $windowReference)  
>>>
```

这个对象比 SimpleWindow 对象“重”,所以源代码可能会很长……让我们只看一下`render()`方法的源代码:

```
>>> show $decoratedWindow->render
public function render()
    public function render()
    {
        return $this->getWindowReference()->render();
    }
```

调用`getWindowReference()`方法，然后它从`render()`方法返回结果。让我们来看看`getWindowReference()`来源:

```
>>> show $decoratedWindow->getWindowReference
public function getWindowReference()
    public function getWindowReference()
    {
        return $this->windowReference;
    }
>>>
```

这个方法返回对象的 windowReference 属性，正如我们从上面的`ls -al`命令中看到的，它是`Acme\Patterns\Decorator\SimpleWindow`的一个实例。当然，我们可以只看一下`DecoratedWindow::__construct()`是如何工作的，但这是我们可以检查的另一种方式。

### 使用嵌入式服务器调试

不幸的是，不支持通过 Apache 这样的 web 服务器进行调试。然而，我们可以使用 PHP 的嵌入式服务器来调试我们的应用程序:

```
$ cd public
$ php -S localhost:8080
PHP 5.5.8 Development Server started at Wed Jul 23 17:40:30 2014
Listening on https://localhost:8080
Document root is /home/action/workspace/lab/PsySH/public
Press Ctrl-C to quit.
```

开发服务器现在正在监听端口 8080 上的连接，所以只要我们通过这个 web 服务器([https://localhost:8080/decorator . PHP](https://localhost:8080/decorator.php))请求`decorator.php`文件，我们就会看到以下内容:

```
Psy Shell v0.1.11 (PHP 5.5.8 — cli-server) by Justin Hileman
>>>
```

我们可以像使用 CLI 一样开始使用 PsySH

```
>>> ls -al

Variables:
  $_COOKIE           []                                                                              
  $_FILES            []                                                                              
  $_GET              []                                                                              
  $_POST             []                                                                              
  $_SERVER           Array(19)                                                                       
  $decoratedWindow   <Acme\Patterns\Decorator\DecoratedWindow #0000000031ef2e3e000000003c2d3a90> 
  $titledWindow      <Acme\Patterns\Decorator\TitledWindow #0000000031ef2e39000000003c2d3a90> 
  $window            <Acme\Patterns\Decorator\SimpleWindow #0000000031ef2e3f000000003c2d3a90> 
  $_                 null                                                                            
>>> exit
Exit:  Goodbye.
```

### 使用单元测试进行调试

作为一名优秀的开发人员，您应该为您的代码编写单元测试，以证明它正在按预期工作。在项目的文件中，您将找到`tests`文件夹，如果您安装了 phpUnit，您可以在其中运行测试。

```
cd tests/
phpunit
PHPUnit 4.0.14 by Sebastian Bergmann.

Configuration read from /home/action/workspace/lab/PsySH/tests/phpunit.xml

...F+-------------+
|Title        |

Time: 66 ms, Memory: 4.50Mb

There was 1 failure:

1) AcmeTest\Patterns\Decorator\TitledWindowTest::testAddTitle
Failed asserting that true is false.

/home/action/workspace/lab/PsySH/tests/Patterns/Decorator/TitledWindowTest.php:46

FAILURES!                            
Tests: 4, Assertions: 7, Failures: 1.
```

即使代码看起来运行完美，测试还是失败了。我们可以通过运行失败的测试来进一步检查:

```
$ phpunit --verbose --debug --filter=TitledWindowTest::testAddTitle
PHPUnit 4.0.14 by Sebastian Bergmann.

Configuration read from /home/action/workspace/lab/PsySH/tests/phpunit.xml

Starting test 'AcmeTest\Patterns\Decorator\TitledWindowTest::testAddTitle'.
F+-------------+
|Title        |

Time: 60 ms, Memory: 4.25Mb

There was 1 failure:

1) AcmeTest\Patterns\Decorator\TitledWindowTest::testAddTitle
Failed asserting that true is false.

/home/action/workspace/lab/PsySH/tests/Patterns/Decorator/TitledWindowTest.php:46

FAILURES!                            
Tests: 1, Assertions: 1, Failures: 1.
```

我们有生成错误的测试、文件和行。让我们来看看`TitledWindowTest.php`

```
<?php
namespace AcmeTest\Patterns\Decorator;

use PHPUnit_Framework_TestCase;
use Acme\Patterns\Decorator\TitledWindow;
use ReflectionMethod;

class TitledWindowTest extends PHPUnit_Framework_TestCase 
{
    public function testRender()
    {
        /* some test code here */

    }

    public function testAddTitle()
    {
        $renderString = 'bar';

        $window = $this->getMock('Acme\Patterns\Decorator\SimpleWindow');
        $window->expects($this->any())->method('render')->will($this->returnValue($renderString));

        $titledWindow = new TitledWindow($window);

        $reflectionMethod = new ReflectionMethod($titledWindow, 'addTitle');
        $reflectionMethod->setAccessible(true);

        $rs = $reflectionMethod->invoke($titledWindow);

        $this->assertFalse(empty($rs));

    }
}
```

如果您不熟悉 phpUnit，不要太关注代码。简而言之，我正在设置一切来测试`TitledWindow::addTitle()`方法，并期望得到一个非空值。

那么，我们如何使用 PsySh 来检查正在发生的事情呢？就像我们之前做的那样添加`Shell::debug()`方法。

```
<?php
namespace DigitecTest\Patterns\Decorator;

use PHPUnit_Framework_TestCase;
use Digitec\Patterns\Decorator\TitledWindow;
use ReflectionMethod;

class TitledWindowTest extends PHPUnit_Framework_TestCase 
{
    public function testRender()
    {
        /* Some test code here */
    }

    public function testAddTitle()
    {
        $renderString = 'bar';

        $window = $this->getMock('Digitec\Patterns\Decorator\SimpleWindow');
        $window->expects($this->any())->method('render')->will($this->returnValue($renderString));

        $titledWindow = new TitledWindow($window);

        $reflectionMethod = new ReflectionMethod($titledWindow, 'addTitle');
        $reflectionMethod->setAccessible(true);

        $rs = $reflectionMethod->invoke($titledWindow);

        \Psy\Shell::debug(get_defined_vars());

        $this->assertFalse(empty($rs));

    }
}
```

我们准备好摇滚了！

```
$ phpunit --verbose --debug --filter=TitledWindowTest::testAddTitle
PHPUnit 4.0.14 by Sebastian Bergmann.

Configuration read from /home/action/workspace/lab/PsySH/tests/phpunit.xml

Starting test 'AcmeTest\Patterns\Decorator\TitledWindowTest::testAddTitle'.
Psy Shell v0.1.11 (PHP 5.5.8 — cli) by Justin Hileman
>>>
```

所以在`$rs`中我们应该有一个字符串；让我们看看我们到底有什么。

```
>>> $rs
=> null
```

空值，难怪测试失败…我们来查一下`TitledWindow::addTitle()`的源代码。如果我们执行一个`ls`命令，我们可以看到我们通过`$titledWindow`对象获得了该对象的方法。

```
>>> show $titledWindow->addTitle
protected function addTitle()
    protected function addTitle()
    {
        $returnString = <<<EOL
+-------------+
|Title        |
EOL;
        echo $returnString . PHP_EOL;
    }
>>>
```

有一只虫子。该方法回显该值，而不是返回该值。尽管应用程序看起来工作正常，但是通过单元测试和 PsySH，我们发现了一个缺陷，现在我们可以修复它了。

## 结论

本文并不打算详尽展示 PsySH 的所有潜在能力。还有其他一些很酷的功能(比如“doc”)你应该尝试一下。单独使用 PsySH 可能不是很有用，但是如果与其他工具和您聪明的调试能力相结合，它可以被证明是一个有价值的资产。

## 分享这篇文章