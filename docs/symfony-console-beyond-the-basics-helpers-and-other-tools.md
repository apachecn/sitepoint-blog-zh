# Symfony 控制台超越基础——助手和其他工具

> 原文：<https://www.sitepoint.com/symfony-console-beyond-the-basics-helpers-and-other-tools/>

*这篇文章由[沃恩·安切塔](https://www.sitepoint.com/author/wancheta)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

不可否认，在开发软件时，控制台命令非常有用。不久前，我们[重新推出了 Symfony 控制台组件](https://www.sitepoint.com/re-introducing-symfony-console-cli-php-uninitiated/)。

该组件允许我们创建结构化和可测试的 CLI 命令。我们创建了一些简单的命令并测试了它们；但是当我们的命令变得更大更复杂时，我们需要一套不同的工具。

这就是我们今天要看的:高级 Symfony 控制台工具。

让我们创建一个命令来显示这些特性。大多数基本功能在 Symfony 控制台文章的[重新介绍中有所介绍，所以在继续之前一定要检查一下——这是一篇快速但有用的阅读材料！](https://www.sitepoint.com/re-introducing-symfony-console-cli-php-uninitiated/)

![Console screenshot](img/efb9f19d22d88032affb22c84ca44aa7.png)

## 装置

```
composer require symfony/console 
```

*关于 Composer 的基本信息可以在[这里](https://www.sitepoint.com/re-introducing-composer/)找到，如果你不熟悉设计良好的隔离 PHP 环境，在其中开发你的 PHP 应用程序，如[流浪者](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)，我们有一本很棒的书深入解释这一切，可以购买[这里](http://bit.ly/phpenv-sp)。*

## 创建我们的命令

让我们为一个空前的最爱创建一个命令:Fizzbuzz。

Fizzbuzz 是编程面试中经常使用的一个简单问题，用来证明被面试者的编程能力。Fizzbuzz 的定义通常有以下形式:

> 写一个程序，打印从 1 到 x 的数字，但是对于 3 的倍数，打印“嘶嘶”而不是数字，对于 5 的倍数，打印“嗡嗡”。对于同时是三和五的倍数的数字，打印“FizzBuzz”。

我们的指挥部将收到一个争论，这将是 Fizzbuzz 的上限。

首先，让我们创建 Fizzbuzz 类。

```
<?php 
declare(strict_types=1);

namespace FizzBuzz;

class Fizzbuzz{

    public function isFizz(int $value): bool{
        if($value % 3 === 0){
            return true;    
        }
        return false;
    }

    public function isBuzz(int $value): bool{
        if($value % 5 === 0){
            return true;    
        }
        return false;
    }

    public function calculateFizzBuzz(int $number): bool{
        if($this->isFizz($number) && $this->isBuzz($number)){
            echo "FizzBuzz \n";
            return true;
        }
        if($this->isFizz($number)){
            echo "Fizz \n";
            return true;
        }
        if($this->isBuzz($number)){
            echo "Buzz \n";
            return true;
        }
        echo $number . "\n";
        return true;
    }

    public function firstNFizzbuzz(int $maxValue): void{
        $startValue = 1;

        while($startValue <= $maxValue){
            $this->calculateFizzBuzz($startValue);
            $startValue++;
        }
    }
} 
```

很简单。`firstNFizzbuzz()`方法打印 Fizzbuzz 对数字`$maxValue`的结果。它通过递归调用`calculateFizzBuzz()`方法来实现这一点。

接下来，让我们编写命令。用以下内容创建一个`FizzCommand.php`文件:

```
<?php

namespace FizzBuzz;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\Input\InputArgument;

use FizzBuzz\Fizzbuzz;

class FizzCommand extends Command{

    protected function configure(){
        $this->setName("FizzBuzz:FizzBuzz")
                ->setDescription("Runs Fizzbuzz")
                ->addArgument('Limit', InputArgument::REQUIRED, 'What is the limit you wish for Fizzbuzz?');
    }

    protected function execute(InputInterface $input, OutputInterface $output){

        $fizzy = new FizzBuzz();
        $input = $input->getArgument('Limit');

        $result = $fizzy->firstNFizzbuzz($input);
    }

} 
```

最后是我们的`console`文件。

```
#!/usr/bin/env php

<?php 

require_once __DIR__ . '/vendor/autoload.php'; 

use Symfony\Component\Console\Application; 
use FizzBuzz\FizzCommand;

$app = new Application();
$app->add(new FizzCommand());
$app->run(); 
```

在这里，我们创建一个新的控制台应用程序，并将我们的`FizzCommand()`注册到其中。不要忘记使这个文件可执行。

我们现在可以通过运行`./console`命令来检查我们的命令是否被正确注册。我们也可以用`./console FizzBuzz:Fizzbuzz 25`来执行我们的命令。这将计算并打印从 1 到 25 的 Fizzbuzz 结果。

到目前为止，我们还没有做任何新的事情。但是有一些方法可以提高我们的指挥能力。首先，命令不是很直观。我们如何知道我们必须将限制传递给命令？为此，Symfony 控制台为我们提供了**问题助手**。

## 问题助手

问题助手提供向用户询问更多信息的功能。通过这种方式，我们可以交互地收集执行命令所需的信息。

让我们将命令改为，不是通过命令执行提示符接收执行限制，而是向用户询问限制。为此，问题助手有一个简单的方法:`ask()`。这个方法接收一个`InputInterface`、一个`OutputInterface`和一个`question`作为参数。

让我们更改`FizzCommand.php`文件，使其看起来像这样:

```
<?php

namespace FizzBuzz;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Question\Question;

use FizzBuzz\Fizzbuzz;

class FizzCommand extends Command{

    protected function configure(){
        $this->setName("FizzBuzz:FizzBuzz")
                ->setDescription("Runs Fizzbuzz");
    }

    protected function execute(InputInterface $input, OutputInterface $output){

        $fizzy = new FizzBuzz();

        $helper = $this->getHelper('question');
        $question = new Question('Please select a limit for this execution: ', 25);
        $limit = $helper->ask($input, $output, $question);

        $result = $fizzy->firstNFizzbuzz($limit);
    }
} 
```

我们不再期待关于`configure()`方法的争论。我们用默认值 25 实例化一个新的`Question`,并在我们之前讨论过的`ask()`方法中使用它。

现在我们有了一个交互式命令，在执行 Fizzbuzz 之前要求一个限制。

问题助手还为我们提供了验证答案的功能。所以让我们用它来确定极限是一个整数。

```
protected function execute(InputInterface $input, OutputInterface $output){

        $fizzy = new FizzBuzz();

        $helper = $this->getHelper('question');
        $question = new Question('Please select a limit for this execution: ', 25);

        $question->setValidator(function ($answer) {
            if (!is_numeric($answer)) {
                throw new \RuntimeException('The limit should be an integer.');
            }
            return $answer;
        });

        $question->setNormalizer(function ($value) {
            return $value ? trim($value) : '';
        });

        $question->setMaxAttempts(2);
        $limit = $helper->ask($input, $output, $question);

        $result = $fizzy->firstNFizzbuzz($limit);
    } 
```

我们不仅通过使用`setValidator()`函数来确保我们的限制是一个整数，我们还归一化了输入，以防用户插入一些空格，并且还将允许的最大尝试次数设置为两次。

问题助手提供了更多的功能，比如让用户从答案列表中选择答案、多个答案、隐藏用户答案和自动完成。官方文档中有更多的相关信息。

## 桌子

控制台组件提供的另一个非常有用的功能是可以显示表格数据。

为了显示一个表格，我们需要使用`Table`类；设置标题和行，最后呈现表格。这在显示结构化数据时非常有用。假设我们想要创建一个命令来显示一些公制的转换。

让我们将`MetricsCommand.php`添加到新的 php 文件中。

```
<?php

namespace Metric;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Helper\Table;

class MetricsCommand extends Command{

    protected function configure(){
            $this->setName("Metrics")
                ->setDescription("Inches to centimeters table.");
       }

    public function execute(InputInterface $input, OutputInterface $output){    
            $table = new Table($output);
            $table
                ->setHeaders(array('Inches', 'Centimeters'))
                ->setRows(array(
                    array('1', '2.54'),
                    array('5', '12.7'),
                    array('10', '25.4'),
                    array('50', '127'),
            ))
        ;
        $table->render();
    }
} 
```

我们的新`console`文件:

```
#!/usr/bin/env php

<?php 

require_once __DIR__ . '/vendor/autoload.php'; 

use Symfony\Component\Console\Application; 
use Metric\MetricsCommand;

$app = new Application();
$app->add(new MetricsCommand());
$app->run(); 
```

这是一个非常简单的命令:它呈现一个表格，其中的一些值从英寸转换为厘米。如果我们使用`./console Metrics`运行我们的命令，结果将是这样的:

![Our execution result](img/94fbd87b2068dac39cabeb8db54b17f1.png)

`Table`类还为我们的表格提供了不同的分隔符样式。如果你想了解更多，请查看[这一页](http://symfony.com/doc/current/components/console/helpers/table.html)。
。

## 进度条

虽然问题和表格非常有用，但最重要的元素可能是进度条。进度条为我们提供了关于命令执行的反馈，并使我们能够清楚地看到我们可能需要等待多长时间才能完成一项操作。

对于运行时间较长的命令，进度条是必不可少的。要使用它们，我们需要`ProgressBar`，给它传递一个单位总数(如果我们实际上知道我们期望多少个单位)并在命令执行时推进它。

带有进度条的简单命令可能如下所示:

```
<?php

namespace Progress;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Helper\ProgressBar;

class ProgressCommand extends Command{

    protected function configure(){
        $this->setName("Progress")
            ->setDescription("Check Console componenet progress bar.");
       }

    public function execute(InputInterface $input, OutputInterface $output)
    {    
        $progress = new ProgressBar($output);
        $progress->start();

        $i = 0;
        while ($i++ < 50) {
            usleep(300000);
            $progress->advance();
        }

        $progress->finish();
    }
} 
```

以及各自的`console`:

```
#!/usr/bin/env php

<?php 

require_once __DIR__ . '/vendor/autoload.php'; 

use Symfony\Component\Console\Application; 
use Progress\ProgressCommand;

$app = new Application();
$app->add(new ProgressCommand());
$app->run(); 
```

这是一个非常简单的命令。我们设置了 bar 并通过一个`sleep()`函数进行循环。最终输出将如下所示:

![Progress bar example](img/3bb4479b61f002291b26b87b992e71f4.png)

关于进度条的更多信息可以在[官方文档](http://symfony.com/doc/current/components/console/helpers/progressbar.html)中找到。

### 定制我们的进度条

自定义进度条对于在用户等待时提供额外信息非常有用。

默认情况下，进度条中显示的信息取决于`OutputInterface`实例的详细程度。因此，如果我们想显示不同层次的信息，我们可以使用`setFormat()`方法。

```
$bar->setFormat('verbose'); 
```

内置格式有:`normal`、`verbose`、`very_verbose`和`debug`。

如果我们以使用`normal`格式为例，结果会是这样的:

![Progress bar, normal format](img/0358aa6e160a358617481019096ed073.png)

我们也可以设置自己的格式。

进度条是由不同的特定占位符组成的字符串。我们可以组合这些特定的占位符来创建我们自己的进度条。可用的占位符有:`current`、`max`、`bar`、`percent`、`elapsed`、`remaining`、`estimated`、`memory`和`message`。因此，例如，如果我们想要复制完全相同的默认进度条，我们可以使用以下代码:

```
$bar->setFormat(' %current%/%max% [%bar%] %percent:3s%% %elapsed:6s%/%estimated:-6s% %memory:6s%'); 
```

自定义进度条还有很多——在这里阅读相关内容。

## 在命令中调用命令

另一个非常有用的特性是能够在命令中运行命令。例如，我们可能有一个命令依赖于另一个命令才能成功运行，或者我们可能希望按顺序运行一系列命令。

例如，假设我们想要创建一个命令来运行 fizzbuzz 命令。
我们需要在我们的`/src`文件夹和`execute()`方法中创建一个新命令，如下所示:

```
protected function execute(InputInterface $input, OutputInterface $output)
{
    $command = $this->getApplication()->find('FizzBuzz:FizzBuzz');
    $returnCode = $command->run();
} 
```

因为我们的 FizzBuzz 命令没有收到任何参数，这就足够了。如果我们的命令需要参数，我们必须创建一个参数数组，并使用 [ArrayInput 类](http://api.symfony.com/3.3/Symfony/Component/Console/Input/ArrayInput.html)来传递它们。

除此之外，所有这些都是关于使用带有命令名的`find()`方法来查找和注册我们的命令。

## 颜色和风格

对输出进行着色和样式化对于在命令执行过程中向用户发出警告或通知非常有用。为此，我们只需向我们的`writeln()`方法添加以下标签，如下所示:

```
// green text
$output->writeln('<info>Output here</info>');

// yellow text
$output->writeln('<comment>Output here</comment>');

// black text on a cyan background
$output->writeln('<question>Output here</question>');

// white text on a red background
$output->writeln('<error>Output here</error>'); 
```

还可以选择使用 [OutputFormatterStyle 类](http://api.symfony.com/3.3/Symfony/Component/Console/Formatter/OutputFormatterStyle.html)定义我们自己的样式:

```
$style = new OutputFormatterStyle('red', 'yellow', array('bold', 'blink'));
$output->getFormatter()->setStyle('fire', $style);

$output->writeln('<fire>foo</fire>'); 
```

更多关于输出样式的信息可以在[这里](http://symfony.com/doc/current/console/coloring.html)找到。

## 总结

从样式到助手，我们看到 Symfony 控制台提供了很多现成的功能。过了今天，绝对没有借口拥有糟糕的命令行工具！

您经常使用控制台的哪些助手和组件？如何启动 CLI 工具？Symfony 主机对你来说足够了吗，或者你更喜欢另一个选择？

## 分享这篇文章