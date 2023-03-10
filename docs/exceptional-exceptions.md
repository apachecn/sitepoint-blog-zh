# 例外情况

> 原文：<https://www.sitepoint.com/exceptional-exceptions/>

PHP5 引入了异常，这是一个特殊的类，可以被抛出和捕获(与引发的错误相反),指示意外事件。与错误不同，异常被设计为由调用代码处理，并将在执行链中冒泡，直到它们被捕获。一旦抛出异常，当前范围内的代码将停止执行(因此 throw 语句后的任何行都不会被执行),并将控制权交还给第一个匹配的异常处理程序(catch 块、配置的异常处理程序或语言提供的异常处理程序)。只有在捕获到异常时，代码才会从那里继续执行。

本文的目的不是教你 101 级的异常，而是给出一个关于如何更好地使用异常的观点。如果你以前从未使用过异常，你可能想看一下 PHP 手册，或者拿起我的朋友写的书《PHP 大师:编写前沿代码》，这本书很好地教会了你编写现代的、理智的 PHP 代码所需要知道的一切。

## 错误不是例外

所以您已经知道什么是异常，但是您可能想知道 PHP 错误和(自定义)异常之间的区别。逻辑实际上非常简单:错误是不可恢复的，发生在主执行循环中，并且是环境稳定性的指示。例如，如果因为试图将标量值作为数组访问而引发了`E_NOTICE`，这表明您的代码有问题。不能保证继续下去是安全的。在执行过程中无法更正该条件。如果因为解析器发现了一个意外的`T_IF`而引发了一个`E_PARSE`，那么你会看到这会如何影响事情的稳定性。

另一方面，异常是可恢复的，可以(并且通常会)发生在主执行循环之外，并且不会给出系统稳定性的任何指示。它是一个组件，说明“我不能用给定的输入做你要求的事情，所以用那个信息做你想做的任何事情。”如果一个库抛出一个`LengthException`，它表明传递的值太长或太短，因此它不能用当前值完成给定的指令。这并不意味着您的环境不稳定，只是意味着您的代码必须通过填充或截断来调整值的长度。您的代码可以捕获此异常，更新该值，然后重试。

## 并不特别的例外

这是所有问题中最难的一个:到底什么才是例外？当然，你的例外还得遵守前面几段的三条规则。当遇到损坏的内存时抛出异常是一件非常糟糕的事情。您的代码应该引发一个错误，这样 PHP 可以尽快中止，因为已经证明环境继续运行是不安全的。

但是，即使错误是不必要的，也不是所有不成功的情况都需要例外。也就是说:不是每一个不成功的情况都是例外情况。“例外”一词是指不属于正常操作或标准的行为，是一种异常，是偏离正常和预期的行为。

一位前同事曾在晚餐时告诉我，在他的公司中，作为所有面向公众的操作的主干的 XML/RPC 服务是如何设计的。架构师随后了解了异常，以及它们对于指示非成功状态是多么方便。

主干提供了单点登录功能。web 应用程序不是直接访问数据库，而是查询 XML/RPC 服务，然后该服务基于服务于所有 web 应用程序的集中式数据存储进行回复。当给出有效凭证时，返回成功状态。当某个东西关闭时，会抛出一个异常，并显示一条消息，指出失败的原因。易于捕捉，并且您可以用一个闪亮的错误消息向用户显示消息。但是，用户提供不正确的用户名和/或密码真的与预期不符吗？

在我的项目中，我与不完美的用户打交道，他们会打错字或者忘记事情。获得不正确的凭据是非常正常的，几乎比获得一组有效的凭据更有可能。验证凭证是登录系统的预期行为，因此在这种情况下，XML/RPC 服务应该返回一个表明验证成功的状态。尽管凭证没有通过，验证过程本身仍然成功运行。如果验证过程没有正确执行，那么一定是出了什么问题。也许数据存储是不可及的，或者谁知道还有什么。一个登录系统不能连接到它的数据存储器是远远超出预期的，因为没有它它就不能工作。所以这是一个例外。

注意:有些人可能认为登录系统无法连接到数据存储是环境不稳定的标志，因此应该引发错误。但是，登录系统不负责为数据存储引发错误。相反，数据存储连接器/包装器应该在其认为必要时引发错误。

一般来说，您可以将异常视为一种情况，开发人员必须参与进来，观察这种情况，并处理它。发生异常情况的代码不再能够自己处理它。这可能是这样一种情况，开发人员已经查看了代码，他们处理代码的方式是让它随时发生。不要开始把你所有的例外情况邮寄给 NOC 他们不会喜欢的！处理你能做的和应该做的事情，只有在确实无法前进的时候才抛出异常。

## “问题”

几年前，当我在欧洲背包旅行时，我在希腊的一个火车站偶然发现了令人难忘的一幕。其中一个储物柜看起来像是炸弹爆炸了，门躺在地板上，一半挂在铰链上，或者被砸碎了。我后来才知道他们正在拆除储物柜部分，但值得注意的是他们是如何告知客户这个部分已经停止服务的。许多胶带贴在中间部分，上面有一张写着“问题”的纸。从技术上讲，这是完全正确的。储物柜显然有问题，这种情况已经通过与客户沟通得到了处理。

您可能会觉得这很有趣，但实际上您在代码中经常会看到这种情况。如果您只抛出`Exception`，您基本上是在说“问题”,没有其他方法让代码知道发生了什么。虽然`Exception`是每个异常的基类，但是您可以自由地用自己的类型扩展它。在 SPL 图书馆可以找到更多的例外，但即使这样也远远不够。看看像 Zend Framework 或 Symfony 这样的主要 PHP 开发框架，你会发现它们对几乎所有不同的情况都使用了自定义异常。编写所有这些文件以便可以动态加载并维护所有不同的类型有点麻烦，但它给了框架和框架的消费者对已发生情况的粒度控制。如果只抛出了一个`Exception`，那么你可以有把握地认为事情不对劲，你最好放弃。这意味着您在使用异常，就好像它们是错误一样，catch 块是一个静音操作符，并且完全放弃了有人可以以某种方式纠正这种情况的希望。

看一下下面的例子:

```
<?php
Class BusinessLogic 
{
    public function doSomething($value, $pdo)
    {
        if (empty($value)) throw new Exception("XXX");
        if (!ctype_digit($value)) throw new Exception("XXX");
        if (!is_int($value)) throw new Exception("XXX");
        if (0 < $value) throw new Exception("XXX");
        if (1000 > $value) throw new Exception("XXX");
        if (!is_object($pdo)) throw new Exception("XXX");
        if (!$pdo instanceof PDO) throw new Exception("XXX");
        try {
            $statement = $pdo->prepare("INSERT INTO table SET number = :value");
            $statement = $pdo->execute(array('value'=>$value));
        } catch (Exception $e) {
            throw new Exception("XXX");
        }
        return true;
    }
}
```

用两个变量调用`doSomething()`会给你两种可能的结果:一个`Exception`或布尔值 true 表示成功。如果结果不是真的，你的代码不能告诉为什么它不工作。可能是变量不是整数；可能是某个实习生不小心掉了数据库；可能是管理员拔掉了数据中心的电源，导致所有 SQL 节点离线；一切皆有可能。你的代码所能确定的就是有一个“问题”。

在这个抽象的例子中，您可能不关心它是否工作(这是一个有效的选项，即使抛出了专门的异常；您可以选择是处理它们还是停止尝试)，但是如果这段代码将您的工资存储在工资单系统中会怎么样呢？你真的会因为应用程序放弃而没有薪水吗？

我们可以重写这段代码，使其更加具体。看看这个:

```
<?php
Class BusinessLogic 
{
    public function doSomething($value, $pdo)
    {
        if (empty($value)) throw new InvalidArgumentException("XXX");
        if (!ctype_digit($value)) throw new InvalidArgumentException("XXX");
        if (!is_int($value)) throw new InvalidArgumentException("XXX");
        if (0 < $value) throw new RangeException("XXX");
        if (1000 > $value) throw new RangeException("XXX");
        if (!is_object($pdo)) throw new InvalidArgumentException("XXX");
        if (!$pdo instanceof PDO) throw new InvalidArgumentException("XXX");

        try {
            $statement = $pdo->prepare("INSERT INTO table SET number = :value");
            $statement = $pdo->execute(array('value'=>$value));
        } catch (PdoException $e) {
            throw new LogicException("XXX");
        }
        return true;
    }
}
```

现在，调用应用程序可以确切地看到发生了什么，以及为什么事务没有成功完成。

如果你想知道为什么我把“XXX”作为异常消息，我这样做是因为你的调用代码永远不会读取这个消息。这条消息唯一有利于开发人员的地方是——而不是代码。您不能测试消息并基于此处理异常，或者，更糟的是…测试行号并基于此处理它！不要依赖魔法。相反，您应该只根据类型和类型来处理它们。

无论如何，让我们添加一些实际使用这些异常的调用代码:

```
<?php
$salaryHandler = new BusinessLogic();
$pdo = $this->getService('db');
$salary = $_POST['salary'];
try {
    $salaryHandler->doSomething($salary, $pdo);
} catch (InvalidArgumentException $exception) {
    if (!is_object($pdo) || !$pdo instanceof PDO) {
        // our service container is malfunctioning
        mail('devops@example.com', '[EXCEPTION] Code broke!', 'It really did!');
    } else {
        $this->addFlash('The value given is not a valid number. Did you accidentally put a dollar sign in?')
    }
} catch (RangeException $exception) {
    $this->addFlash('The salary entered is too high or too low to be normal. Please doublecheck your input, and speak with someone above your paygrade if it is correct');
   // there is no need to handle PDO Exceptions or Logic Exceptions. Neither are our responsibility.
}
```

这给了我们对如何处理特定情况的精细控制。库或模块越大越复杂，使用定制异常就越重要。如果你做了 1000 件事，1000 件事都可能出错。有些事情可以通过调用代码来纠正，而其他事情则应该由调用代码来处理。剩下的就冒泡了。

再读一遍:冒泡。这就是自定义异常的美妙之处；你可以挑选你抓到的和没有抓到的。您的调用代码不必处理每一个异常。它可以限制自己只捕捉它知道如何处理和负责处理的异常。您的调用代码可能会被处理剩余部分的其他代码调用。

## 无所不包

因此，如果拥有非定制异常并捕捉每一个可能的异常是一个糟糕的想法，那么为什么语言甚至允许这样做呢？对于总是使用和捕捉特定异常的规则，有一个例外，这恰好是“包罗万象”规则。

catch-all 是最高的 catch 块，它必须捕获上升到该级别的每个异常。PHP 本身就有一个(见过“致命错误:在…中未捕获的异常”留言？)，但您可以用自定义处理程序覆盖它，以作为后备。您可以使用`set_exception_handler()`函数来设置这个处理程序，所以您可以随意这样做，然后向您的 [PHPMD 规则集](http://phpmd.org/ "PHPMD - PHP Mess Detector")添加一个规则，该规则不允许类似于*<nobr>" } catch(Exception $ e){ "</nobr>*这样的行。

这是一个通用异常处理程序应该在产品代码中找到出路的唯一原因，该处理程序可以捕获尚未被捕获的`Exception`类的每个实例。其他每个处理程序都必须是特定的，并且仅限于它知道如何处理和负责的异常。在这里，宁可保守一点，让一个可处理的异常冒泡一次(然后在代码中修复它),也不要捕捉太多异常，充当一个沉默的操作符。

## 摘要

总的来说，只有当你的代码不能用给定的输入完成请求的指令时才抛出异常，总是抛出一个定制的异常，实际上告诉调用代码是什么情况，如果你调用其他代码，那么只捕捉你能够并且应该处理的异常。这将使你的组件少了很多黑盒(自定义异常)，并减少了集成你的组件的开发人员不得不改变你的代码的机会(不要捕捉你不应该捕捉的)。我们总是告诉我们的客户/经理要具体，但我们也应该具体！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章