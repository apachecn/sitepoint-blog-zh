# Laravel 中的 Behat 和 PhpSpec 入门

> 原文：<https://www.sitepoint.com/bdd-in-laravel-getting-started-with-behat-and-phpspec/>

这篇文章最初发表在托管持续集成和部署服务 [Semaphore CI](https://semaphoreci.com) 的社区教程部分。

* * *

BDD ( [行为驱动开发](http://guide.agilealliance.org/guide/bdd.html))对于许多开发人员来说是一个复杂的主题，以正确的方式开始使用它*通常并不容易——尤其是当需要将其实现到现有框架中的时候。本教程旨在帮助您在很短的时间内建立并运行一个基于 BDD 的 Laravel 项目，向您介绍您需要独立进行的基本概念和工作流。我们将安装和使用 [Behat](http://docs.behat.org/en/v2.5/) 和 [PhpSpec](http://www.phpspec.net/en/latest/) 。*

在本教程中，我们假设您正在 Unix 系统上工作，并且对 BDD 有基本的理论知识，但是几乎没有实践经验。

我们还假设用户说“运行命令”意味着该命令应该在操作系统的终端中运行。

* * *

### 先决条件

*   Unix 操作系统(例如 Ubuntu)
*   PHP 5.6 以上
*   饭桶
*   [作曲](https://semaphoreci.com/community/tutorials/getting-started-with-composer-for-php-dependency-management)(最好是全球安装的

或者，如果您打算将我们在这里设置的内容构建到一个适当的应用程序中，请添加:

*   数据库(MySQL)
*   缓存层(Redis、Memcached ...)

## 创建新的 Laravel 应用程序

为了创建一个新的 Laravel 应用程序，我们运行以下命令:

```
composer create-project laravel/laravel bdd-setup
```

示例应用程序现在已经创建好了，如果您访问应用程序的根目录，应该会看到“Laravel 5”。

![Laravel Greeting Screen](img/151fae9958d8fd6d3b4c5315b442071c.png)

## 设置行为

为了让 Behat 和 Laravel 玩得更好，需要几个软件包。让我们将它们全部安装到应用程序的开发环境中(用`--dev`标志)并解释每一个。

```
composer require behat/behat behat/mink behat/mink-extension laracasts/behat-laravel-extension --dev
sudo ln -s /home/vagrant/Code/bdd-setup/vendor/bin/behat /usr/local/bin/behat
```

`behat/behat`是 Behat 的主包。`behat/mink`包用于模拟浏览器，因此我们可以让测试套件检查我们的 URL 和它们的输出。`behat/mink-extension`是水貂和 Behat 的胶水，最后一包，`behat-laravel-extension`是 [Jeffrey Way](https://github.com/JeffreyWay) 自己实现的 Behat 绑定，专门为 Laravel 制作的。

最后一个`sudo ln -s`行是可选的，它将 Behat 的可执行文件添加到`$PATH`中的一个位置，因此可以从我们项目的根文件夹中不带`vendor/bin`前缀地执行`behat`命令。换句话说，是`behat --init`而不是`vendor/bin/behat --init`。

最后，我们初始化一个 Behat 项目:

```
behat --init
```

这在我们的项目目录中创建了一个名为`features`的新文件夹:

![Features bootstrap folder](img/78d867bc3a144dc7ed008f720e98d510.png)

### 上下文和配置

Behat 使用自动生成的`FeatureContext`类中的[定义](http://docs.behat.org/en/v2.5/guides/2.definitions.html)来理解我们在测试什么——比如“假设我在 URL 上这个和那个…”。

为了获得一些典型的浏览器相关定义，我们确保`FeatureContext`类扩展了包含它们的`MinkContext`类。

因此，我们将`FeatureContext.php`的源代码从:

```
class FeatureContext implements Context, SnippetAcceptingContext
```

到

```
class FeatureContext extends Behat\MinkExtension\Context\MinkContext implements Context, SnippetAcceptingContext
```

通过这一改变，我们让`FeatureContext`继承了`MinkContext`中的定义。

`behat -dl`命令用于列出所有已定义的定义。它现在将输出如下内容:

```
default | Given /^(?:|I )am on (?:|the )homepage$/
default |  When /^(?:|I )go to (?:|the )homepage$/
default | Given /^(?:|I )am on "(?P<page>[^"]+)"$/
default |  When /^(?:|I )go to "(?P<page>[^"]+)"$/
default |  When /^(?:|I )reload the page$/
```

接下来，我们需要设置 Laravel 特定的包。根据[指令](https://github.com/laracasts/behat-laravel-extension)，这是通过向项目根目录添加一个`behat.yml`文件来完成的:

```
default:
    extensions:
        Laracasts\Behat:
            # env_path: .env.behat
        Behat\MinkExtension:
            default_session: laravel
            base_url: http://localhost:8888
            laravel: ~
```

上面提到的`.env.behat`文件包含特定于 Behat 测试会话的环境变量。这个文件默认不存在，所以我们可以通过复制已经包含的`.env.example`文件来创建它:

```
cp .env.example .env.behat
```

**注意:**由于不同 Laravel 版本之间的安装程序不同，您*可能*需要在`config/app.php`和`.env`以及`.env.behat`的 APP_KEY 中添加一个定制的密钥。保持在 32 个字符以下(默认为“某个随机字符串”)将会抛出错误。

## 书写特征

特性是我们用 Behat 测试的。我们将它们写成人类可读的故事，并期望测试套件不仅理解它们，而且确保它们工作。

一个这样的特征可以是当我们访问主页时检查我们是否看到“Laravel 5”。为了编写这个特性，我们在`features`文件夹中创建一个`hometest.feature`文件，并赋予它以下内容:

```
Feature:
    In order to prove that Behat works as intended
    We want to test the home page for a phrase
```

每个功能都是以这样的描述开始的。这仅适用于人类——测试套件并不打算理解这一点。然后遵循*场景*——该套件应该遵循的具体的、计算机可读的步骤。

```
Feature:
    In order to prove that Behat works as intended
    We want to test the home page for a phrase

    Scenario: Root Test
        When I am on the homepage
        Then I should see "Laravel 5"
```

每个场景都以单词“scenario”开头，缩进到特性描述的级别。每个场景都应该有一个名称。

紧接在它和中的另一个缩进级别之下，该场景将有 Behat 要遵循的特定说明。这些指令解析自我们在`FeatureContext`类中定义的定义。在我们的例子中，我们通过扩展`MinkContext`来定义它们。

`When I am on the homepage`是`MinkContext`中的一个具体定义，它规定:

```
/**
     * Opens homepage.
     *
     * @Given /^(?:|I )am on (?:|the )homepage$/
     * @When /^(?:|I )go to (?:|the )homepage$/
     */
    public function iAmOnHomepage()
    {
        $this->visitPath('/');
    }
```

换句话说，两个短语会触发这个:`Given I am on the homepage`和`When I am on the homepage`。该功能将模拟访问根 URL: `/`。

下一个定义，`Then I should see "Laravel 5"`呼吁:

```
/**
     * Checks, that page contains specified text.
     *
     * @Then /^(?:|I )should see "(?P<text>(?:[^"]|\\")*)"$/
     */
    public function assertPageContainsText($text)
    {
        $this->assertSession()->pageTextContains($this->fixStepArgument($text));
    }
```

该函数从呈现的页面中获取所有文本，并检查我们的字符串是否是它的子字符串。

然而，在测试之前，我们需要启动一个本地 PHP 服务器，这样 Mink 就可以访问我们要求它访问的 URL。

```
php -S localhost:8888 -t public
```

上面的命令在 url `localhost`上启动一个服务器(`-S`)，监听目标目录`public`中的端口`8888`。

最后，我们可以测试该特性:

```
> behat

Feature:
  In order to prove that Behat works as intended
  We want to test the home page for a phrase

  Scenario: Root Test             # features/hometest.feature:5
    When I am on the homepage     # FeatureContext::iAmOnHomepage()
    Then I should see "Laravel 5" # FeatureContext::assertPageContainsText()

1 scenario (1 passed)
2 steps (2 passed)
0m0.64s (22.13Mb)
```

行为的基础现在已经具备。我们将在未来的帖子中进行一些深入的集成。

**注意**:通过使用`behat-laravel-extension`包，我们确保所有的 Laravel 功能在`FeatureContext`中立即可用。获得主`$app`对象现在就像`app()`一样简单，获得一个配置变量只是一个`config("somevar")`。这些绑定都是自动可用的，随时可以使用。

### 使用 PHPUnit 的断言

Behat 本身没有断言。因此，您可能希望使用 PHPUnit 的。鉴于 PHPUnit 与新的 Laravel 应用程序捆绑在一起，它已经可用了，要访问断言，只需在`FeatureContext`类中导入该类，如下所示:

```
use PHPUnit_Framework_Assert as PHPUnit;
```

然后，您将可以访问断言，如下所示:

![Autocomplete dialog for assertions in imported PHPUnit class](img/149a51cd342b1892c94af79ec89ec521.png)

点击查看可用断言的完整列表[。](https://phpunit.de/manual/current/en/appendixes.assertions.html)

## PhpSpec

PhpSpec 越来越成为人们武库中 PHPUnit 的常见替代品。Laravel 确实附带了 PHPUnit，但这并不意味着没有用 PhpSpec 替换或补充它的余地。

PhpSpec 和 PHPUnit 之间最显著的区别是语法——PhpSpec 可读性更强，对人更友好，因此非常适合 BDD 和 Behat 的整个概念。测试不一定要以单词`test`开始，所有的方法都是以句子的形式出现，作为我们想要做的动作或者我们想要对象拥有的属性。连[医生](http://tldrify.com/adb)都这么说:

> SpecBDD 和 TDD 之间没有真正的区别。使用 xSpec 工具而不是常规的 xUnit 工具进行 TDD 的价值在于语言。

此外，PhpSpec 有助于测试和类的搭建，以及模拟。我们将在另一个更深入的教程中看到，但现在让我们安装和设置它，然后通过一些基础知识。

让我们安装 PhpSpec:

```
composer require phpspec/phpspec --dev
```

同样，我们可以将安装的可执行文件添加到我们的路径中，这样它就可以在没有`vendor/bin`前缀的情况下运行。要么执行下面的命令(修改路径以匹配您的路径)，要么将整个`vendor/bin`文件夹添加到您的路径中——无论您喜欢哪种方式。

```
sudo ln -s /home/vagrant/Code/bdd-setup/vendor/bin/phpspec /usr/local/bin/phpspec
```

PhpSpec 差不多可以开箱即用了，我们只需要再做一点小小的编辑。在项目文件夹的根目录下，在所有的行下面，我们添加:

```
spec_path: tests
```

这告诉 PhpSpec 把我们的规范文件放在哪里。你可以随意改变它。

### 编写规格

规范是包含测试的类，很像 PHPUnit 中的测试类。为了给一个类创建一个新的规范，我们使用了`desc`命令(用于描述)。假设我们正在制作一个计算器类，打算作为服务构建到 Laravel 中。在版本 1 中，计算器至少应该能够将两个数相加。让我们构建这个版本 1。

```
phpspec desc bddsetup\\Calculator
```

请注意，`bddsetup`是本教程的演示名称空间，如果您选择了不同的名称空间，您应该将其更改为您自己的名称空间。

这在`tests/spec/CalculatorSpec.php`中创建了一个规范文件，包含:

```
<?php

namespace spec\bddsetup;

use PhpSpec\ObjectBehavior;
use Prophecy\Argument;

class CalculatorSpec extends ObjectBehavior
{
    function it_is_initializable()
    {
        $this->shouldHaveType('bddsetup\Calculator');
    }
}
```

**注意**:`$this`关键字指的是被测试类的实例(`Calculator`)，而不是测试类本身！

如果我们现在运行`phpspec`，它将请求我们允许为我们创建缺失的`Calculator`类。让我们允许它。

```
bddsetup/Calculator                                                             
  10  - it is initializable
      class bddsetup\Calculator does not exist.

                                      100%                                       1
1 specs
1 example (1 broken)
105ms

  Do you want me to create `bddsetup\Calculator` for you?                       
                                                                         [Y/n] 
Y
Class bddsetup\Calculator created in /home/vagrant/Code/bdd-setup/app/Calculator.php.

                                      100%                                       1
1 specs
1 example (1 passed)
135ms
```

这自动通过了测试，因为`it_is_initializable`测试成功了——毕竟这个类现在已经存在了。

现在，让我们一起使用 Behat 和 PhpSpec 来创建一个`sum`方法。

## 二重唱

在真正的 BDD 方式中，我们首先设想一个特性，写出它，然后测试它的存在。让我们在`features/calc.feature`创建一个新的特征文件:

```
Feature:
  In order to make sure the calculator works
  As a developer
  I need to get the correct output from its functions

  Scenario: Summing
    Given the method "sum" receives the numbers 4 and 7
    Then the calculated value should be 11
```

求和场景中的两个定义并不存在。我们需要将它们添加到`FeatureContext`中，以便 Behat 能够理解它们。生成空代码片段供我们填写的一个简单方法是使用`--append-snippets`命令。

```
behat --append-snippets
```

`FeatureContext`类现在应该有两个额外的方法:

```
/**
     * @Given the method :arg1 receives the numbers :arg2 and :arg3
     */
    public function theMethodReceivesTheNumbersAnd($arg1, $arg2, $arg3)
    {
        throw new PendingException();
    }

    /**
     * @Then the calculated value should be :arg1
     */
    public function theCalculatedValueShouldBe($arg1)
    {
        throw new PendingException();
    }
```

Behat 自动提取它识别的参数。这意味着方法(以及延伸的定义)是灵活的——我们可以根据自己的需要改变参数。让我们现在填写那些存根。

```
/**
     * @Given the method :arg1 receives the numbers :arg2 and :arg3
     */
    public function theMethodReceivesTheNumbersAnd($arg1, $arg2, $arg3)
    {
        $this->calculator = new Calculator();
        $this->calculator->$arg1($arg2, $arg3);
    }

    /**
     * @Then the calculated value should be :arg1
     */
    public function theCalculatedValueShouldBe($arg1)
    {
        PHPUnit::assertEquals($arg1, $this->calculator->result());
    }
```

您可以在这里看到，我们使用的是以前的 PHPUnit 断言，尽管我们同时拥有 PhpSpec 和 Behat。

如果我们现在运行 Behat，我们应该得到:

```
[Symfony\Component\Debug\Exception\FatalErrorException]  
  Call to undefined method bddsetup\Calculator::sum()
```

这很正常。毕竟我们没有实施。让 PhpSpec 帮助我们解决这个问题。添加一个新方法到`CalculatorSpec`:

```
function it_should_sum()
    {
        $this->sum(4, 7);
        $this->result()->shouldBe(11);
    }
```

当我们运行它时，PhpSpec 将请求许可来清除`sum`和`result`方法:

```
> phpspec run

bddsetup/Calculator                                                               
  15  - it should sum
      method bddsetup\Calculator::sum not found.

                  50%                                     50%                    2
1 specs
2 examples (1 passed, 1 broken)
153ms

  Do you want me to create `bddsetup\Calculator::sum()` for you?                
                                                                         [Y/n] 
Y
  Method bddsetup\Calculator::sum() has been created.

bddsetup/Calculator                                                               
  15  - it should sum
      method bddsetup\Calculator::result not found.

                  50%                                     50%                    2
1 specs
2 examples (1 passed, 1 broken)
136ms

  Do you want me to create `bddsetup\Calculator::result()` for you?             
                                                                         [Y/n] 
Y
  Method bddsetup\Calculator::result() has been created.

bddsetup/Calculator                                                               
  15  - it should sum
      expected [integer:11], but got null.

                  50%                                     50%                    2
1 specs
2 examples (1 passed, 1 failed)
144ms
```

同时，运行失败是因为这些方法没有完成预期的任务。这很好。现在让我们编辑`Calculator`类并完全实现它们。

```
<?php

namespace bddsetup;

class Calculator
{
    protected $result = 0;

    public function sum($argument1, $argument2)
    {
        $this->result = (int)$argument1 + (int)$argument2;
    }

    public function result()
    {
        return $this->result;
    }
}
```

如果我们现在用`behat`运行 Behat，用`phpspec run`运行 PhpSpec，我们应该得到所有绿色的结果——所有测试都应该通过。

现在更容易想象快速有效地扩展类:

*   省略第二个参数可能会将传入的参数添加到上一个操作的结果中
*   sum 方法可以返回 Calculator 实例来启用链接，很好地处理了上述问题
*   等等…

## 结论

有了 Behat 和 PhpSpec 等强大的 BDD 工具，编写故事和测试未来升级的类变得轻而易举，而不是编写模拟的乏味夜晚。

本教程向您展示了如何在一个新的 Laravel 应用程序中开始使用 BDD 工具。这篇文章展示的内容足以吊起你的胃口。未来的帖子将会更详细地讨论一些用例的具体实现。

## 分享这篇文章