# 防错——用超防御编程拯救项目

> 原文：<https://www.sitepoint.com/poka-yoke-saving-projects-with-hyper-defensive-programming/>

*这篇文章由[德姬·阿卡拉](https://www.sitepoint.com/author/dakala)和[马可·皮维塔](https://twitter.com/ocramius)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

当在中型到大型团队中使用相同的代码库时，有时会很难理解彼此的代码以及如何使用它们。有各种解决方案可以帮助解决这个问题。例如，你可以同意遵循一套编码标准，使代码对彼此更具可读性，或者使用一个众所周知的框架(我们有一个很棒的 Laravel intro premium 课程[在这里](https://www.sitepoint.com/premium/courses/laravel-5-2930/))。

然而，这通常是不够的，特别是当有人不得不重新研究一段时间以前编写的应用程序的一部分来修复一个 bug 或添加一个新特性时。很难记住特定的类是如何工作的，不管是单独工作还是相互组合工作。在这一点上，很容易在没有意识到的情况下意外引入副作用或错误。

![A detected bug](img/1197fb5e670ce275e515dfa55b446b1b.png)

这些错误可能会在[质量保证](https://www.sitepoint.com/8-must-have-php-quality-assurance-tools/)中被发现，但也有可能被忽略。即使他们被抓住了，也要花很多时间把代码发送回去并修复它。

那么如何才能防止这种情况呢？输入*“防卡轭”*。

## 什么是防差错控制？

防差错是一个日语术语，大致意思是“防错”。术语
起源于精益制造，指的是帮助机器操作员避免错误的任何机制。

除了制造业，防差错技术还经常用于消费电子产品。以 sim 卡为例，由于其不对称的形状，它只能以一种方式安装在 SIM 托盘上。

![A SIM card and tray, illustrating how the SIM card can only fit one way.](img/8f3970819172da30877ad1157ecb3ff0.png)

缺少防差错功能的硬件的一个例子是 PS/2 端口，它与键盘连接器和鼠标连接器的形状完全相同。它们只能通过使用颜色代码来区分，因此很容易意外地交换连接器并将其插入错误的端口，因为它们的安装方式相同。

![PS/2 ports for keyboards and mice, illustrating how they use an interchangeable format making it too easy to accidentally insert one into the other.](img/c7af20f48dbcff78287bfc7c266d63d3.png)

除了在硬件中使用，防差错的概念也可以应用于编程。这个想法是让我们的代码的公共接口尽可能容易理解，并且一旦我们的代码被不正确地使用就抛出错误。这似乎是显而易见的，但在现实中，我们经常遇到缺乏这些方面的代码。

但是请注意，防差错并不是为了防止故意滥用。目标只是防止意外错误，而不是保护您的代码免受恶意使用。只要有人能够访问您的代码，他们就能够绕过您的保护措施，如果他们真的想这样做的话。

在讨论可以采取哪些具体措施来提高代码的防错性之前，有必要知道防差错机制通常可以分为两类:

*   错误预防
*   错误检测

错误预防技术有助于及早发现错误。它们旨在通过使接口和行为尽可能简单明了来确保没有人会意外地错误使用我们的代码。以 SIM 卡为例，SIM 卡只能单向放入 SIM 卡盘中。

另一方面，错误检测机制存在于我们的代码之外。他们监控我们的应用程序，观察潜在的错误，并警告我们。一个例子可以是检测连接到 PS/2 端口的设备是否是正确类型的软件，如果不是，则向用户显示警告，澄清为什么它不工作。这种特殊的软件无法防止错误，因为连接器在插入时是可以互换的，但它可以检测到一个错误并警告我们，这样错误就可以被修复。

在本文的其余部分，我们将探索几种方法，在我们的应用程序中实现错误预防和错误检测。但是请记住，这个列表只是一个起点。根据您的具体应用，额外的措施可能会使您的代码更加防错。此外，记住防差错的前期成本很重要，并确保它对于您的特定项目是值得的。根据您的应用程序的复杂性和规模，与错误的潜在成本相比，一些措施可能太昂贵了。因此，由您和您的团队来决定哪些措施最适合您。

## 错误预防示例

![A targeted virus, molecular scale view CG render](img/9c6f8e920b8bc163aaf4e709027a53de.png)

### (标量)类型声明

以前在 PHP 5 中称为类型提示，类型声明是在 PHP 7 中开始防止函数和方法签名出错的一种简单方法。

通过为函数参数指定特定的类型，在调用函数时就很难混淆参数的顺序。

例如，让我们把这个`Notification`发送给一个用户:

```
<?php

class Notification {
    private $userId;
    private $subject;
    private $message;

    public function __construct(
        $userId,
        $subject, 
        $message
    ) {
        $this->userId = $userId;
        $this->subject = $subject;
        $this->message = $message;
    }

    public function getUserId()
    {
        return $this->userId;
    }

    public function getSubject()
    {
        return $this->subject;
    }

    public function getMessage()
    {
        return $this->message;
    }
} 
```

没有类型声明，我们很容易注入不正确的变量类型，这可能会破坏我们的应用程序。例如，我们可以假设`$userId`应该是一个`string`，而实际上它可能必须是一个`int`。

如果我们注入了错误的类型，这个错误可能会被发现，直到应用程序试图用`Notification`做一些事情。到那时，我们可能会得到一些关于意外类型的模糊错误消息，但不会立即指向我们注入了`string`而不是`int`的代码。

正因为如此，迫使应用程序尽可能快地崩溃通常更有意思，这样在开发的早期就能发现这样的错误。

在这种情况下，我们可以简单地添加一些类型声明，当我们混淆了参数的类型时，PHP 会立即停止并警告我们一个致命错误:

```
<?php

declare(strict_types=1);

class Notification {
    private $userId;
    private $subject;
    private $message;

    public function __construct(
        int $userId,
        string $subject, 
        string $message
    ) {
        $this->userId = $userId;
        $this->subject = $subject;
        $this->message = $message;
    }

    public function getUserId() : int
    {
        return $this->userId;
    }

    public function getSubject() : string
    {
        return $this->subject;
    }

    public function getMessage() : string
    {
        return $this->message;
    }
} 
```

但是请注意，在默认情况下，PHP 会试图将不正确的参数强制转换为它们期望的类型。为了防止这种情况，我们启用`strict_types`很重要，这样当出现错误时，我们实际上会得到一个致命错误。因此，标量类型声明不是防差错的理想形式，但是它们是减少错误的良好开端。即使禁用了`strict_types`，它们仍然可以作为参数类型的指示。

此外，我们为我们的方法声明了返回类型。这些使我们在调用某个函数时更容易确定我们可以期待什么样的值。

明确定义的返回类型也有助于在处理返回值时避免大量的 switch 语句，因为如果没有显式声明的返回类型，我们的方法可能会返回各种类型。因此，使用我们方法的人必须检查在特定场景中实际返回了哪种类型。显然，这些 switch 语句可能会被遗忘，并导致难以检测的错误。这种错误在返回类型中变得不那么普遍了。

### 价值对象

标量类型提示不容易解决的一个问题是，拥有多个函数参数可能会混淆这些参数的顺序。

![Child shape sorter toy, an analogy to sorting the right arguments by type when passing them into a function](img/1fb756d6bd327893ecea6ef5e1f07d63.png)

当所有的参数都有不同的标量类型时，当我们混淆了参数的顺序时，PHP 会警告我们，但是在大多数情况下，我们可能会有一些类型相同的参数。

要解决这个问题，我们可以将参数包装在值对象中，如下所示:

```
class UserId {
    private $userId;

    public function __construct(int $userId) {
        $this->userId = $userId;
    }

    public function getValue() : int
    {
        return $this->userId;
    }
}

class Subject {
    private $subject;

    public function __construct(string $subject) {
        $this->subject = $subject;
    }

    public function getValue() : string
    {
        return $this->subject;
    }
}

class Message {
    private $message;

    public function __construct(string $message) {
        $this->message = $message;
    }

    public function getMessage() : string
    {
        return $this->message;
    }
}

class Notification {
    /* ... */

    public function __construct(
        UserId $userId,
        Subject $subject, 
        Message $message
    ) {
        $this->userId = $userId;
        $this->subject = $subject;
        $this->message = $message;
    }

    public function getUserId() : UserId { /* ... */ }

    public function getSubject() : Subject { /* ... */ }

    public function getMessage() : Message { /* ... */ }
} 
```

因为我们的论点现在都有非常特定的类型，所以几乎不可能把它们混在一起。

与标量类型声明相比，使用值对象的另一个优点是，我们不再需要在每个文件中启用`strict_types`。而如果不是非要记住，也不可能是偶然忘记。

### 确认

![A list of checkboxes being checked, analogy to validation](img/6f4f82013816763ce82f7c2cb0e79762.png)

当使用值对象时，我们可以将数据的验证逻辑封装在对象本身中。这样做，我们可以防止创建具有无效状态的值对象，这可能会导致应用程序的其他层出现问题。

例如，我们可能有一个规则说任何给定的`UserId`应该总是正的。

显然，无论何时我们得到一个`UserId`作为输入，我们都可以验证这个规则，但另一方面，它也很容易在某个地方被遗忘。

即使这个错误会导致我们的应用程序的另一层出现实际错误，错误消息也可能不清楚到底是哪里出了问题，并且很难调试。

为了防止这样的错误，我们可以给`UserId`构造函数添加一些验证:

```
class UserId {
    private $userId;

    public function __construct($userId) {
        if (!is_int($userId) || $userId < 0) {
            throw new \InvalidArgumentException(
                'UserId should be a positive integer.'
            );
        }

        $this->userId = $userId;
    }

    public function getValue() : int
    {
        return $this->userId;
    }
} 
```

通过这种方式，我们可以始终确保当我们处理一个`UserId`对象时，它有一个有效的状态。这使我们不必在应用程序的各个层不断地重新验证我们的数据。

注意，我们可以添加一个标量类型声明，而不是在这里使用`is_int`，但是这将迫使我们在使用`UserId`的任何地方都启用`strict_types`。

如果我们不启用`strict_types`，每当其他类型被传入`UserId`时，PHP 就会悄悄地尝试强制它们使用`int`。这可能会有问题，因为我们可能会注入一个`float`，这实际上可能是一个不正确的变量，因为用户 id 通常不是`floats`。

在其他情况下，比如我们可能正在处理一个`Price`值对象，禁用`strict_types`可能会导致舍入错误，因为 PHP 会自动将`float`变量转换为`int`。

### 不变

默认情况下，PHP 中的对象是通过引用传递的。这意味着当我们对一个对象进行更改时，它会在整个应用程序中立即改变。

![Lego blocks, perhaps the most immutable object in the known universe](img/2757b2f8b3dd373fc362d29afc4a4044.png)

虽然这种方法有其优点，但也有一些缺点。
以通过短信和电子邮件发送给用户的`Notification`为例:

```
interface NotificationSenderInterface
{
    public function send(Notification $notification);
}

class SMSNotificationSender implements NotificationSenderInterface
{
    public function send(Notification $notification) {
        $this->cutNotificationLength($notification);

        // Send an SMS...
    }

    /**
     * Makes sure the notification does not exceed the length of an SMS.
     */
    private function cutNotificationLength(Notification $notification)
    {
        $message = $notification->getMessage();
        $messageString = substr($message->getValue(), 160);
        $notification->setMessage(new Message($messageString));
    }
}

class EmailNotificationSender implements NotificationSenderInterface
{
    public function send(Notification $notification) {
        // Send an e-mail ...
    }
}

$smsNotificationSender = new SMSNotificationSender();
$emailNotificationSender = new EmailNotificationSender();

$notification = new Notification(
    new UserId(17466),
    new Subject('Demo notification'),
    new Message('Very long message ... over 160 characters.')
);

$smsNotificationSender->send($notification);
$emailNotificationSender->send($notification); 
```

因为`Notification`对象是通过引用传递的，所以我们导致了一个意想不到的副作用。通过在`SMSNotificationSender`中削减消息的长度，引用的`Notification`对象在整个应用程序中被更新，这意味着当它稍后被`EmailNotificationSender`发送时也被削减。

为了解决这个问题，我们可以让我们的`Notification`对象不可变。我们可以添加一些`with`方法，在应用更改之前复制原始的`Notification`，而不是提供`set`方法来对其进行更改:

```
class Notification {
    public function __construct( ... ) { /* ... */ }

    public function getUserId() : UserId { /* ... */ }

    public function withUserId(UserId $userId) : Notification {
        $c = clone $this;
        $c->userId = clone $userId;
        return $c;
    }

    public function getSubject() : Subject { /* ... */ }

    public function withSubject(Subject $subject) : Notification {
        $c = clone $this;
        $c->subject = clone $subject;
        return $c;
    }

    public function getMessage() : Message { /* ... */ }

    public function withMessage(Message $message) : Notification {
        $c = clone $this;
        $c->message = clone $message;
        return $c;
    }
} 
```

这样，每当我们对我们的`Notification`类进行修改时，例如通过减少消息的长度，这种修改不再波及整个应用程序，从而防止了任何意想不到的副作用。

但是请注意，在 PHP 中要使一个对象真正不可变是非常困难的(如果不是不可能的话)。但是为了使我们的代码防错，如果我们添加“不可变的”`with`方法而不是`set`方法，这已经很有帮助了，因为类的用户不再需要记住在做出改变之前自己克隆对象。

### 返回空对象

有时我们可能有函数或方法可以返回某个值，或`null`。这些可空的返回值可能会造成问题，因为在我们对它们做一些事情之前，几乎总是需要检查它们是否是`null`。同样，这是我们很容易忘记的事情。为了避免总是检查返回值，我们可以返回空对象。

![An empty poker chip, analogous to a null object](img/c257aab39da95788a16c4f785ef36a7c.png)

例如，我们可以对一个`ShoppingCart`应用或不应用折扣:

```
interface Discount {
    public function applyTo(int $total);
}

interface ShoppingCart {
    public function calculateTotal() : int;

    public function getDiscount() : ?Discount;
} 
```

当计算我们的`ShoppingCart`的最终价格时，我们现在总是必须在调用`applyTo`方法之前检查`getDiscount()`返回的是`null`还是实际的`Discount`:

```
$total = $shoppingCart->calculateTotal();

if ($shoppingCart->getDiscount()) {
    $total = $shoppingCart->getDiscount()->applyTo($total);
} 
```

如果我们不做这种检查，当`getDiscount()`返回`null`时，我们可能会得到 PHP 警告和/或其他意想不到的影响。

另一方面，如果我们在没有设置`Discount`的情况下返回一个空对象，那么这些检查可以被一起删除:

```
class ShoppingCart {
    public function getDiscount() : Discount {
        return !is_null($this->discount) ? $this->discount : new NoDiscount();
    }
}

class NoDiscount implements Discount {
    public function applyTo(int $total) {
        return $total;
    }
} 
```

现在，当我们调用`getDiscount()`时，我们总是得到一个`Discount`对象，即使没有折扣。这样，我们可以将折扣应用到我们的总额中，即使总额中没有折扣，我们也不再需要`if`语句:

```
$total = $shoppingCart->calculateTotal();
$totalWithDiscountApplied = $shoppingCart->getDiscount()->applyTo($total); 
```

### 可选依赖项

出于同样的原因，我们希望避免可空的返回类型，我们可能希望避免可选的依赖项，而只是使所有的依赖项都是必需的。

以下面的类为例:

```
class SomeService implements LoggerAwareInterface {
    public function setLogger(LoggerInterface $logger) { /* ... */ }

    public function doSomething() {
        if ($this->logger) {
            $this->logger->debug('...');
        }

        // do something

        if ($this->logger) {
            $this->logger->warning('...');
        }

        // etc...
    }
} 
```

这种方法有两个问题:

1.  我们必须经常检查我们的`doSomething()`方法中是否存在日志记录器。
2.  当在我们的服务容器中设置`SomeService`类时，有人可能会忘记实际设置一个记录器，或者他们甚至不知道该类有设置记录器的选项。

我们可以通过使`LoggerInterface`成为必需的依赖项来简化这一点:

```
class SomeService {
    public function __construct(LoggerInterface $logger) { /* ... */ }

    public function doSomething() {
        $this->logger->debug('...');

        // do something

        $this->logger->warning('...');

        // etc...
    }
} 
```

这样我们的公共接口变得不那么混乱，每当有人创建一个新的`SomeService`实例时，他们知道这个类需要一个`LoggerInterface`实例，所以他们不会忘记注入一个。

此外，我们省略了对`if`语句的需要，以检查是否注入了日志记录器，这使得我们的`doSomething()`更容易阅读，并且当有人对其进行更改时，不容易出错。

如果在某些时候我们想要使用没有日志记录器的`SomeService`,我们可以应用与 return 语句相同的逻辑，只使用一个空对象来代替:

```
$service = new SomeService(new NullLogger()); 
```

最后，这与使用可选的`setLogger()`方法具有相同的效果，但是它使我们的代码更容易理解，并且减少了依赖注入容器中出错的机会。

### 公共接口

为了使我们的代码更容易使用，最好将我们的类中的`public`方法的数量保持在最低限度。这样，应该如何使用我们的代码就不那么令人困惑了，我们需要维护的代码也更少了，重构时破坏向后兼容性的机会也更小了。

![Arrows pointing towards a machine-based center, indicating public methods entering a class' major functionality](img/2f65d3d11dd2c238bf112064ae306247.png)

为了将`public`方法保持在最低限度，将公共方法视为事务会有所帮助。

以在两个银行账户之间转账为例:

```
$account1->withdraw(100);
$account2->deposit(100); 
```

虽然底层数据库可以提供一个交易来确保如果存款不能被提取，或者反之亦然，但是数据库不能防止我们忘记调用`$account1->withdraw()`或`$account2->deposit()`，这将导致不正确的余额。

幸运的是，我们可以通过用一个事务方法替换两个独立的方法来轻松解决这个问题:

```
$account1->transfer(100, $account2); 
```

因此，我们的代码变得更加健壮，因为只完成部分事务就很难出错。

## 错误检测示例

与错误预防机制相反，错误检测机制不是为了预防错误。相反，它们是为了在检测到问题时提醒我们。

大多数时候，它们都存在于我们的应用程序之外，并定期运行以监控我们的代码或对代码的特定更改。

### 单元测试

单元测试是确保新代码正确工作的好方法，但它也有助于确保无论何时有人重构系统的一部分，现有的代码仍能按预期工作。

因为有人仍然可能忘记实际运行我们的单元测试，所以当使用像 [Travis CI](https://www.sitepoint.com/php-continuous-integration-travis-ci/) 和 [Gitlab CI](https://about.gitlab.com/) 这样的服务进行更改时，自动运行它们是明智的。这样，当发生重大变更时，开发人员会自动得到通知，这也有助于我们审查拉请求，以确保变更按预期进行。

除了错误检测之外，单元测试也是一种很好的方式来提供代码的特定部分如何工作的例子，这反过来可以防止别人使用我们的代码时出现错误。

### 代码覆盖报告和变异测试

因为我们总是会忘记编写足够的测试，所以每当我们的单元测试运行时，使用像 [Coveralls](https://coveralls.io/) 这样的服务来自动生成代码覆盖报告是有益的。每当我们的代码覆盖率下降时，工作服就会给我们发送一个通知，这样我们就可以添加一些单元测试，我们也可以了解我们的代码覆盖率是如何随着时间的推移而发展的。

![Graphs indicating change in trends over time](img/7eabc69d0156e0ba11241b3a2f7bfc6d.png)

另一个更好的方法是建立一些突变测试，例如使用 [Humbug](https://www.sitepoint.com/testing-your-tests-who-watches-the-watchmen/) 来确保我们的代码有足够的单元测试。顾名思义，这些测试旨在通过稍微修改我们的源代码，然后运行我们的单元测试，并确保相关的测试由于突变而开始失败，来验证我们有相当多的代码覆盖率。

使用代码覆盖报告和突变测试，我们可以确保我们的单元测试覆盖足够的代码，以防止意外的错误或缺陷。

### 代码分析器

代码分析器可以在开发过程的早期检测应用程序中的错误。例如，像 [PHPStorm](https://www.sitepoint.com/phpstorm-8-released-see-whats-new-grab-free-license/) 这样的 ide 使用代码分析器来警告我们错误，并在我们编写代码时给出建议。从简单的语法错误到重复代码的检测。

除了大多数 ide 中内置的分析器之外，还可以将第三方甚至定制的分析器集成到我们的应用程序的构建过程中，以发现特定的问题。可以在[exa kat/PHP-static-analysis-tools](https://github.com/exakat/php-static-analysis-tools)找到适合 PHP 项目的分析器的非详尽列表，从编码标准分析器到检查安全漏洞的分析器。

在线解决方案也存在，例如 [SensioLabs Insights](https://insight.sensiolabs.com/) 。

### 记录消息

与大多数其他错误检测机制相反，当应用程序在生产中运行时，日志消息可以帮助我们检测应用程序中的错误。

![Illustration of a scroll, indicating a long log](img/f083794e6edcf39414380ed1814f494f.png)

当然，首先需要我们的代码在发生意外时记录消息。即使我们的代码支持日志程序，它们也很容易在设置时被遗忘。因此，我们应该尽量避免可选的依赖关系(见上文)。

虽然大多数应用程序至少会记录一些消息，但它们提供的信息只有在使用像 [Kibana](https://www.elastic.co/products/kibana) 或 [Nagios](https://www.nagios.com/solutions/log-monitoring/) 这样的工具进行主动分析和监控时才会变得真正有趣。像这样的工具可以在用户主动使用应用程序时，而不是在内部测试时，对应用程序中出现的错误和警告给出新的见解。在这里，我们有一篇关于用这个 ELK 栈[监控 PHP 应用的很棒的帖子。](https://www.sitepoint.com/how-can-the-elk-stack-be-used-to-monitor-php-apps/)

### 不要隐瞒错误

即使在主动记录错误消息时，也经常会发生一些错误被隐藏的情况。每当“可恢复的”错误发生时，PHP 倾向于继续运行，好像它想通过保持应用程序运行来帮助我们。然而，当开发或测试一个新特性时，错误通常是非常有用的，因为它们通常表明我们代码中的错误。

这就是为什么大多数代码分析器在检测到你使用`@`来抑制错误时会警告你，因为它可以隐藏漏洞，一旦访问者实际使用应用程序，这些漏洞将不可避免地再次出现。

一般来说，最好将 PHP 的`error_reporting`级别设置为`E_ALL`，这样即使是最轻微的警告也会被报告。但是，请确保将这些消息记录在某个地方，并对用户隐藏它们，这样就不会向最终用户暴露有关应用程序架构或潜在安全漏洞的敏感信息。

除了`error_reporting`配置之外，始终启用`strict_types`也很重要，这样 PHP 就不会试图自动将函数参数强制转换为预期的类型，因为这通常会导致从一种类型转换为另一种类型时难以检测的错误(例如，从`float`转换为`int`时的舍入错误)。

## PHP 之外的用法

由于防差错更多的是一个概念，而不是一种具体的技术，所以它也可以应用于 PHP 之外(但与之相关)的领域。

### 基础设施

在基础设施层面，通过使用像[vagger](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)这样的工具，拥有一个与生产环境相同的共享开发设置，可以避免很多错误。

使用像 [Jenkins](https://jenkins.io/) 和 [GoCD](https://www.gocd.io/) 这样的构建服务器来实现部署过程的自动化，也有助于防止在部署对我们的应用程序的更改时出现错误，因为这通常会包括一系列根据应用程序而定的必需步骤，而这些步骤很容易被遗忘。

### REST APIs

在构建 REST APIs 时，我们可以加入 Poka Yoke 来使我们的 API 更容易使用。例如，我们可以确保每当在 URL 查询或请求体中传递未知参数时，我们总是返回一个错误。这可能看起来很奇怪，因为我们显然想避免“破坏”我们的 API 的客户端，但是通常最好尽快警告使用我们 API 的开发人员不正确的用法，这样可以在开发过程的早期修复错误。

例如，我们可以在 API 上有一个`color`参数，但是使用我们 API 的人可能会意外地使用一个`colour`参数。在没有任何警告的情况下，这种错误可以很容易地进入生产环境，直到最终用户因为意外的行为而注意到它。为了学习如何构建不会伤害你的 API，一本像本这样的[好书可能会派上用场。](https://www.sitepoint.com/build-apis-wont-hate-review/)

### 应用程序配置

几乎所有的应用程序都至少依赖于一些定制配置。通常，开发人员喜欢为配置提供尽可能多的默认值，这样配置应用程序的工作量就少了。

然而，就像上面的`color`和`colour`例子一样，很容易输入错误的配置参数，这将导致我们的应用程序意外地回到默认值。当应用程序没有引发错误时，这些类型的错误可能很难跟踪，对于不正确的配置引发错误的最佳方式是不提供任何默认值，并且在配置参数丢失时立即引发错误。

### 防止用户错误

![Policeman illustration holding up an open palm towards the viewport as if to say 'stop'](img/ba8cc32d064b52645b87c845fd47a5b3.png)

防差错概念也可以用于防止或检测用户错误。例如，在支付软件中，用户输入的账号可以使用[校验位](https://en.wikipedia.org/wiki/Luhn_algorithm)算法进行验证。这可防止用户意外输入错别字的帐号。

## 结论

虽然防差错更多的是一个概念，而不是一套特定的工具，但我们可以将各种原则应用到我们的代码和开发过程中，以确保在早期防止或检测到错误。这些机制通常特定于应用程序本身及其业务逻辑，但是我们可以使用一些简单的技术和工具来使任何代码更加可靠。

也许要记住的最重要的事情是，虽然我们显然想避免生产中的错误，但它们在开发过程中非常有用，我们不应该害怕尽快提出它们，这样就更容易跟踪错误。这些错误可能是由代码本身引发的，也可能是由独立于我们的应用程序运行并从外部监控它的独立进程引发的。

为了进一步减少错误，我们应该尽可能保持代码的公共接口简单明了。

如果你有更多关于如何将防差错应用于 PHP 开发或编程的技巧，欢迎在评论中分享！

## 进一步阅读

### 防卡轭

*   [防差错——丰田生产系统指南](http://blog.toyota.co.uk/poka-yoke)描述了防差错在丰田生产过程中的作用。
*   [如何使用防差错技术提高软件质量](http://www.softwaretestinghelp.com/poka-yoke/)给出了如何使用防差错技术提高软件功能质量的技巧。
*   你的代码给出了如何将防差错应用于一般编程的快速概述。
*   [防差错——在软件中应用防错功能](http://techie-notebook.blogspot.be/2012/07/poka-yoke-applying-mistake-proofing-to.html)更详细地概述了如何在编程中应用防差错功能。

### PHP 中的防错

*   极度防御性 PHP 是一个关于如何让你的 PHP 代码更加防错的讲座。
*   使用不可变对象的 3 个好处很好地概述了不可变对象的优点。
*   PHP 中的不可变值对象给出了一个如何让值对象变得不可变(或者至少尽可能不可变)的快速概述。
*   [PHP 和不变性](https://www.simonholywell.com/post/2017/03/php-and-immutability/)更深入地探讨了 PHP 中不变性是如何工作的。
*   [写好代码:如何减少代码的认知负荷](http://chrismm.com/blog/writing-good-code-reduce-the-cognitive-load/)描述了各种方法，使你的代码更容易遵循，从而减少了别人在使用你的代码或对代码进行修改时出错的机会。

## 分享这篇文章