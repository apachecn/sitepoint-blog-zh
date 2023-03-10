# 用 log 4 PPH 实现 PSR-3

> 原文：<https://www.sitepoint.com/implementing-psr-3-with-log4php/>

随着 PSR-3(日志库的公共接口标准)最近的通过，用我最喜欢的日志库来讨论它的实现似乎是合适的。log4php 是一个开源的 Apache 项目，是一个非常通用的日志框架。

通过 log4php 的配置文件，日志记录有多种输出位置，因此可以将特定的日志级别发送到不同的输出位置。例如:debug、info 和 notice 主要用于常规日志记录和调试，因此将它们定向到标准日志文件是有意义的，而 critical 和 above 通常意味着我们的应用程序出现了可怕的错误，通过电子邮件将它们发送给我们自己以便立即诊断更有意义。

## 获取依赖关系

在我们开始写代码之前，我们需要了解我们的依赖关系。需要 PSR-3 接口类；目前[可以在 GitHub](https://github.com/php-fig/log/tree/master/Psr/Log "log/Psr/Log at master - php-fig/log - GitHub") 上找到，并且可以通过[包装商](https://packagist.org/packages/psr/log "psr/log - Packagist")的 composer 安装到[。还有就是需要 log4php 框架，可以](https://www.sitepoint.com/php-dependency-management-with-composer/ "PHP Dependency Management with Composer")[从 Apache](http://logging.apache.org/log4php/download.html "Apache log4php - Download - Apache log4php") 下载，用 PEAR、[或者 composer](https://packagist.org/packages/apache/log4php "apache/log4php - Packagist") 。

这里有一个设置好一切的示例文件`composer.json`:

```
{
    "require": {
        "psr/log": "dev-master",
        "apache/log4php": "2.3.0"
    }
}
```

## 包装日志 4 页

安装了必要的第三方库后，我们可以创建一个通用的日志类，它实现了 PSR-3 接口并使用 log4php。

PSR-3 接口公开了八种不同的日志级别:调试、信息、通知、警告、错误、关键、警报和紧急。log4php 包含六个日志级别:跟踪、调试、信息、警告、错误和致命。我们必须将 PSR-3 水平映射到适当的 log 4 PPH 水平。下面的类实现了接口，并管理 log4php logger 实例来映射级别。

```
<?php
require_once 'vendor/autoload.php';

class MyLogger implements PsrLogLoggerInterface
{
    private $logger;

    public function __construct($logger = 'main', $config = null) {
        Logger::configure($config);
        $this->logger = Logger::getLogger($logger);
    }

    /**
     * System is unusable.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function emergency($message, array $context = array()) {
        $this->logger->fatal($this->interpolate($message, $context));
    }

    /**
     * Action must be taken immediately.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function alert($message, array $context = array()) {
       $this->logger->fatal($this->interpolate($message, $context));
    }

    /**
     * Critical conditions.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function critical($message, array $context = array()) {
        $this->logger->fatal($this->interpolate($message, $context));
    }

    /**
     * Runtime errors that do not require immediate action but should
     * be logged and monitored.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function error($message, array $context = array()) {
        $this->logger->error($this->interpolate($message, $context));
    }

    /**
     * Exceptional occurrences that are not errors.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function warning($message, array $context = array()) {
        $this->logger->warn($this->interpolate($message, $context));
    }
    /**
     * Normal but significant events.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function notice($message, array $context = array()) {
        $this->logger->info($this->interpolate($message, $context));
    }

    /**
     * Interesting events.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function info($message, array $context = array()) {
        $this->logger->info($this->interpolate($message, $context));
    }

    /**
     * Detailed debug information.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function debug($message, array $context = array()) {
        $this->logger->debug($this->interpolate($message, $context));
    }

    /**
     * Logs with an arbitrary level.
     *
     * @param mixed $level
     * @param string $message
     * @param array $context
     * @return null
     */
    public function log($level, $message, array $context = array()) {
        throw new Exception('Please call specific logging message');
    }

    /**
     * Interpolates context values into the message placeholders.
     * Taken from PSR-3's example implementation.
     */
    protected function interpolate($message, array $context = array()) {
        // build a replacement array with braces around the context
        // keys
        $replace = array();
        foreach ($context as $key => $val) {
            $replace['{' . $key . '}'] = $val;
        }

        // interpolate replacement values into the message and return
        return strtr($message, $replace);
    }
}
```

除了实现 PSR-3 接口，这个类还包含一个接受两个可选参数`$logger`和`$config`的构造函数。第一个参数允许您为 logger 指定一个名称，log4php 将维护一个对它的引用，允许您使用不同的名称同时维护多个 logger 实例。第二个参数允许您指定一个配置文件。如果没有指定，log4php 将使用其默认配置。

现在可以用下面的例子测试新创建的`MyLogger`类:

```
<?php
$logger = new MyLogger();
$logger->debug('My debug test');
```

调试的缺省 log4php 设置将把消息输出到标准输出，所以这个例子的结果将被发送到 web 浏览器。

## log4php 配置

log4php 附带了一个默认配置，所以我们可以马上开始使用它，但是它也提供了使用 XML 或 php 代码覆盖配置的能力。最受支持的配置是 XML。

让我们用一个新的 appender 创建一个`config.xml`文件，它将告诉 log4php 将所有带有 warn 及以上的消息记录到一个文件中:

```
<configuration >
 <appender name="myAppender" class="LoggerAppenderFile">
  <param name="file" value="myLog.log"/>
 </appender>
 <root>
  <level value="WARN"/>
   <appender_ref ref="myAppender"/>
  </root>
</configuration>
```

当我们调用它的构造函数时，我们现在需要将新配置文件的名称传递给`MyLogger`。

```
<?php
$logger = new MyLogger('main', 'config.xml');
$logger->debug('My debug test');
```

如果您重新运行该示例，当调用调试消息时，log4php 将忽略它，因为调试低于 warn，而我们的配置只记录 warn 及以上。

## 结论

通过利用 PSR-3 日志接口和 log4php，符合标准的日志现在可以非常简单地添加到我们的任何项目中。要了解 log4php 的更多信息，请访问[快速入门指南](http://logging.apache.org/log4php/quickstart.html "Apache log4php - Quick start- Apache log4php")。关于 PSR-3 的更多信息，请阅读 GitHub 上的[标准。](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md "fig-standards/accepted/PSR-3-logger-interface.md at master - php-fig/fig-standards - GitHub")

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章