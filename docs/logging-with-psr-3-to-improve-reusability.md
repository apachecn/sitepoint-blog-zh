# 使用 PSR-3 进行日志记录，以提高可重用性

> 原文：<https://www.sitepoint.com/logging-with-psr-3-to-improve-reusability/>

日志是 PHP 中最常见的任务之一。我们使用日志来跟踪错误消息，记录重要事件，并调试代码中的问题。在任何 PHP 项目中，代码很可能充满了对为我们处理这些操作的日志库的调用。

不幸的是，对分散在代码中的日志库的调用使得代码依赖于该库的可用性，这明显违反了依赖性反转原则。即使我们使用依赖注入来给我们的对象提供对日志库的访问，日志库之间的差异意味着在它们之间切换是困难和耗时的，需要我们整个代码库的重大重构。

为了促进日志库之间的兼容性，PHP-FIG 小组最近发布了 [PSR-3](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md "fig-standards/accepted/PSR-3-logger-interface.md at master - php-fig/fig-standards - GitHub") ，这是一个日志对象的公共接口。在本文中，我将讨论由 PSR-3 定义的日志接口如何允许我们编写不依赖于任何特定日志实现的可重用代码。

## 首先，快速入门

在我们研究 PSR-3 如何使我们的代码更加可重用之前，有必要了解什么是 PSR-3。如果你已经熟悉 PSR-3，你可以跳过这一节。

该规范的核心是 logger 对象的接口。这个接口公开了八种处理不同严重程度消息的方法，以及一个通用的`log()`方法，它可以接受任意严重程度。

新 PSR 协议支持的八个严重级别基于 [RFC 5424](http://tools.ietf.org/html/rfc5424 "RFC 5424 - The Syslog Protocol") ，描述如下:

*   `Emergency`–系统不可用
*   `Alert`–需要立即采取行动
*   `Critical`–临界条件
*   `Error`–不需要立即关注但应该监控的错误
*   `Warning`–非错误的异常或不良事件
*   `Notice`–正常但重要的事件
*   `Info`–有趣的事件
*   `Debug`–用于调试目的的详细信息

每个日志记录方法都接受一条消息，这条消息必须是一个字符串或一个带有`__toString()`方法的对象。另一个参数接受一个数组，该数组可以为日志消息提供上下文信息。

这些方法和参数的完整解释可以在 [PSR-3 规范](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md "fig-standards/accepted/PSR-3-logger-interface.md at master - php-fig/fig-standards - GitHub")中找到。

## 获取 PSR-3 文件

获得使用 PSR-3 所需的文件很容易——你可以在 Psr/Log [GitHub 库](https://github.com/php-fig/log "php-fig/log - GitHub")中找到它们。你也可以使用[作曲家](https://www.sitepoint.com/php-dependency-management-with-composer/ "PHP Dependency Management with Composer")从[包装商](https://packagist.org/packages/psr/log "psr/log - Packagist")那里获得文件。下面是一个示例 composer.json 文件，用于检索 Psr/Log 文件:

```
{
    "require": {
        "psr/log": "dev-master"
    }
}
```

## 日志记录如何限制代码重用

PHP 有许多不同的日志库，每一个都有自己收集和记录数据的方法。虽然它们之间有一些共同的想法，但是每个库都有自己独特的一套日志记录方法。这意味着在日志记录程序之间切换可能很有挑战性，通常需要在使用日志记录的地方修改代码。这违背了代码重用的思想和面向对象设计的坚实原则。我们面临的情况是，要么声明对特定日志库的依赖，要么完全避免日志。

为了更清楚地说明这个问题，需要一个具体的例子。假设我们正在创建一个简单的 Mailer 对象来处理发送电子邮件。我们希望邮件发送者在发送邮件时记录一条消息，我们决定使用优秀的 [Monolog](https://github.com/Seldaek/monolog "Seldaek/monolog - GitHub") 库来处理我们的记录需求。

```
<?php
namespace Email;

class Mailer
{
    private $logger;

    public function __construct($logger)
    {
        $this->logger = $logger;
    }

    public function sendEmail($emailAddress)
    {
        // code to send an email...

        // log a message
        $this->logger->addInfo("Email sent to $emailAddress");
    }
}
```

我们可以用下面的代码来使用这个类:

```
<?php
// create a Monolog object
$logger = new MonologLogger("Mail");
$logger->pushHandler(new MonologHandlerStreamHandler("mail.log"));

// create the mailer and send an email
$mailer = new EmailMailer($logger);
$mailer->sendEmail("email@example.com");
```

运行这段代码将在`mail.log`文件中产生一个新条目，记录该电子邮件已发送。

此时，我们可能会说我们已经编写了一个可重用的 Mailer 对象。我们通过依赖注入使日志记录器对邮件程序可用，这样我们就可以交换不同的日志记录器配置，而不必接触我们的邮件程序代码。看来我们已经成功地遵循了坚实的原则，并避免了创建任何硬依赖。

但是假设我们想在一个不同的项目中重用我们的 Mailer 类，这个项目使用[模拟](https://github.com/jbroadway/analog/ "jbroadway/analog - GitHub")来处理日志交互。现在我们遇到一个问题，因为 Analog 没有一个`addInfo()`方法。要用 Analog 记录信息级别的消息，我们调用`Analog::log($message, Analog::INFO)`。

我们可以修改 Mailer 类来使用 Analog 的方法，如下所示。

```
<?php
namespace Email;

class Mailer
{
    public function sendEmail($emailAddress)
    {
        // code to send an email...

        // log a message
        Analog::log("Email sent to $emailAddress", Analog::INFO);
    }
}
```

我们可以使用下面的代码来使用更新后的 Mailer 类:

```
<?php
// set up Analog
Analog::handler(AnalogHandlerFile::init("mail.log"));

// create the mailer and send an email
$mailer = new EmailMailer();
$mailer->sendEmail("email@example.com");
```

虽然这可行，但远非理想。我们已经遇到了 Mailer 对特定日志实现的依赖性，每当引入新的日志记录程序时，都需要更改该类。这降低了类的可重用性，并迫使我们在依赖特定日志记录程序的可用性和完全放弃从类中记录日志之间做出选择。

## 使用 PSR-3 避免日志依赖

正如 Alejandro Gervasio 在他关于这个主题的优秀文章中解释的那样，依赖倒置原则告诉我们，我们应该依赖抽象而不是具体。在日志记录的例子中，到目前为止，我们的问题是缺少一个可以依赖的像样的抽象。

这就是 PSR-3 的用武之地。PSR-3 旨在通过为记录器提供一个通用接口来克服日志实现不兼容的问题，这个接口被恰当地命名为`LoggerInterface`。通过提供一个不绑定到任何特定实现的接口，PSR-3 将我们从依赖特定的记录器中解放出来——我们可以根据`LoggerInterface`的类型提示来获得一个 PSR-3 兼容的记录器。我更新了下面的 Mailer 类来演示这一点:

```
<?php
namespace Email;

class Mailer
{
    private $logger;

    public function __construct(PsrLogLoggerInterface $logger)
    {
        $this->logger = $logger;
    }

    public function sendEmail($emailAddress)
    {
        // code to send an email...

        // log a message
        $this->logger->info("Email sent to $emailAddress");
    }
}
```

构造函数已经被修改以接受一个`LoggerInterface`的实现者，并且`sendEmail()`方法现在调用 PSR 3 中指定的`info()`方法。

Monolog 已经兼容 PSR-3，Analog 提供了一个实现`LoggerInterface`的包装器对象，所以我们现在可以使用这两个记录器，而不需要修改我们的 Mailer 类。

下面是用独白调用该类的方法:

```
<?php
// create a Monolog object
$logger = new MonologLogger("Mail");
$logger->pushHandler(new MonologHandlerStreamHandler("mail.log"));

// create the mailer and send an email
$mailer = new EmailMailer($logger);
$mailer->sendEmail("email@example.com");
```

对于模拟:

```
<?php
// create a PSR-3 compatible Analog wrapper
$logger = new AnalogLogger();
$logger->handler(AnalogHandlerFile::init("mail.log"));

// create the mailer and send an email
$mailer = new EmailMailer($logger);
$mailer->sendEmail("email@example.com");
```

现在我们可以在任一库中使用我们的 Mailer 对象，而不必编辑 Mailer 类或改变我们使用它的方式。

## 对不支持 PSR-3 的记录器使用适配器模式

到目前为止，我们已经通过请求一个`LoggerInterface`的实现者成功地将 Mailer 对象从任何特定的日志实现中分离出来。但是那些还没有增加对 PSR-3 支持的伐木工呢？例如，流行的 [KLogger](https://github.com/katzgrau/KLogger/ "katzgrau/KLogger - GitHub") 库已经有一段时间没有更新了，目前与 PSR-3 不兼容。

幸运的是，通过利用[适配器模式](http://en.wikipedia.org/wiki/Adapter_pattern "Adapter pattern - Wikipedia, the free encyclopedia")的能力，我们很容易将 KLogger 公开的方法映射到那些由`LoggerInterface`定义的方法。 [Psr/Log 存储库](https://github.com/php-fig/log "php-fig/log - GitHub")中的支持文件通过为我们提供一个可以扩展的`AbstractLogger`类，使得创建适配器类变得容易。抽象类只是将在`LoggerInterface`中定义的八个特定级别的日志记录方法转发给一个通用的`log()`方法。通过扩展`AbstractLogger`类并定义我们自己的`log()`方法，我们可以很容易地为不支持 PSR-3 的记录器创建 PSR-3 兼容的适配器。下面我将通过为 KLogger 创建一个简单的适配器来演示这一点:

```
<?php
namespace Logger;

class KloggerAdapter extends PsrLogAbstractLogger implements PsrLogLoggerInterface
{
    private $logger;    

    public function __construct($logger)
    {
        $this->logger = $logger;
    }

    public function log($level, $message, array $context = array())
    {
        // PSR-3 states that $message should be a string
        $message = (string)$message;

        // map $level to the relevant KLogger method
        switch ($level) {
            case PsrLogLogLevel::EMERGENCY:
                $this->logger->logEmerg($message, $context);
                break;
            case PsrLogLogLevel::ALERT:
                $this->logger->logAlert($message, $context);
                break;
            case PsrLogLogLevel::CRITICAL:
                $this->logger->logCrit($message, $context);
                break;
            case PsrLogLogLevel::ERROR:
                $this->logger->logError($message, $context);
                break;
            case PsrLogLogLevel::WARNING:
                $this->logger->logWarn($message, $context);
                break;
            case PsrLogLogLevel::NOTICE:
                $this->logger->logNotice($message, $context);
                break;
            case PsrLogLogLevel::INFO:
                $this->logger->logInfo($message, $context);
                break;
            case PsrLogLogLevel::DEBUG:
                // KLogger logDebug() method does not accept a second
                // argument
                $this->logger->logDebug($message);
                break;
            default:
                // PSR-3 states that we must throw a
                // PsrLogInvalidArgumentException if we don't
                // recognize the level
                throw new PsrLogInvalidArgumentException(
                    "Unknown severity level"
                );
        }
    }
}
```

`log()`方法只是将`LoggerInterface`方法映射到各自的 KLogger 方法，KLogger 处理实际的日志记录活动。通过以这种方式包装 KLogger 类，我们能够在不破坏`LoggerInterface`契约的情况下使用它。

我们现在可以使用 KLogger 适配器来处理 Mailer 类:

```
<?php
// create a new KLogger object which will log to the "logs" directory
$klogger = KLogger::instance("logs");

// inject KLoggger to a PSR-3 compatible KloggerAdapter
$logger = new LoggerKloggerAdapter($klogger);

// send an email
$mailer = new EmailMailer($logger);
$mailer->sendEmail("email@example.com");
```

使用 adapter 类，我们能够在不修改 Mailer 类的情况下使用 KLogger，并且仍然遵守`LoggerInterface`。

KLogger 不接受调试级别消息的第二个参数，所以即使有适配器，它在技术上也不符合 PSR-3。扩展 KLogger 使之与 PSR-3 完全兼容是一项微不足道的任务，但这超出了本文的范围。然而，可以肯定地说，使用我们的适配器类使我们非常接近完全符合 PSR-3，并允许我们将`LoggerInterface`与 KLogger 类一起使用。

## 结论

在本文中，我们看到了使用 PSR-3 如何帮助我们编写不依赖于特定日志记录实现的日志记录无关代码。许多主要的 PHP 项目已经增加了对 PSR-3 的支持，包括 Monolog、 [Symfony](https://twitter.com/fabpot/status/289416851082051586 "Twitter/fabpot: #Symfony...") 和[Mustache.php](https://github.com/bobthecow/mustache.php/commit/1515f5126b35436228f40ee3338c8ede5398d7fb "Update logger to implement accepted PSR log spec - 1515f51 - bobthecow/mustache.php")，其他大牌如 [Drupal](http://drupal.org/node/1289536#comment-6925208 "#1289536: Add test coverage and/or one real use of PSR-3 based watchdog logger object | drupal.org") 正在讨论如何最好地集成它。随着 PSR-3 使得日志不再是代码重用的障碍，我们应该会看到更多的库和框架恰当地使用日志来向开发人员提供有用的信息。

PSR-3 会影响您在应用程序中使用日志记录的方式吗？请在下面的评论区告诉我们。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章