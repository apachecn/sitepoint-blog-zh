# 使用 Zend 作业队列调度

> 原文：<https://www.sitepoint.com/zend-queue/>

Web 应用程序通常遵循同步通信模型。然而，非交互式和长时间运行的任务(比如报告生成)更适合异步执行。卸载任务以便稍后运行，甚至在不同的服务器上运行的一种方法是使用作为 [Zend Server 5](http://www.zend.com/products/server/) 的一部分提供的作业队列模块(尽管不是 Community Edition 的一部分)。

作业队列允许基于时间、优先级甚至依赖关系来调度作业。作业可以延期或定期执行，最好的是并行运行！最重要的是，Zend Server 本身提供了一个管理 GUI 来跟踪作业的执行，包括它们的状态、执行时间和输出。

作业队列模块的主要优点是它能够并行执行任务。与 cron 作业不同，作业队列允许:

*   立即运行任务，而不等待它们完成(异步执行)
*   运行任务一次，但不是立即运行(延迟作业)
*   定期运行任务(像 cron 这样的重复作业，但是通过 PHP API 可以完全控制它们——启动、停止、暂停、恢复)
*   能够通过 API 查询作业状态、处理故障和重新排队，以及从 GUI 跟踪过去、当前和待定的作业。

下面是一些异步任务的示例，作业队列在这些任务中会有所帮助:

*   为下一个请求准备数据(预计算)
*   预缓存数据
*   生成定期报告
*   发送电子邮件
*   清理临时数据或文件
*   与外部系统通信
*   与移动设备的后台数据同步

## 作业队列使用

作业队列的 API 通过`ZendJobQueue`类可用。为了执行大多数任务，您将通过实例化一个`ZendJobQueue`对象连接到一个作业队列服务器，并使用`createHttpJob()`方法创建一个作业。

```
<?php
$queue = new ZendJobQueue();
$queue->createHttpJob("http://example.com/jobs/somejob.php");
```

传递到`createHttpJob()`的路径而不是完整的 URL 将创建一个以`$_SERVER["HTTP_HOST"]`的值作为主机名的作业。注意`$_SERVER["HTTP_HOST"]`不可用的情况，比如从 cron 脚本中调度作业。

```
<?php
// both calls are equivalent
$queue->createHttpJob("/jobs/somejob.php");
$queue->createHttpJob("http://" . $_SERVER["HTTP_HOST"] . "/jobs/somejob.php");
```

作业参数可以作为查询字符串的一部分传递，也可以作为第二个参数`createHttpJob()`在一个数组中传递。如果参数作为第二个参数传递，数组必须是 JSON 兼容的。

要访问参数，可以在作业代码中使用`getCurrentJobParams()`静态方法。

```
<?php
$params = ZendJobQueue::getCurrentJobParams();
```

通过`createHttpJob()`的第三个参数可获得额外的工作选项。这是一个带有以下键的关联数组:

*   `name`–可选的工作名称
*   `priority`–由相应的常量`PRIORITY_LOW`、`PRIORITY_NORMAL`、`PRIORITY_HIGH`和`PRIORITY_URGENT`定义的作业优先级
*   `persistent`–一个布尔值，表示是否将作业永远保留在历史中
*   `predecessor`–整数前置任务 ID
*   `http_headers`–附加 HTTP 报头
*   `schedule`–类似 cron 的调度命令
*   `schedule_time`–作业应该执行的时间(但实际上可能在该时间之后运行，这取决于作业队列的负载)

例如，创建一个延迟或重复的作业，如下例所示:

```
<?php
$params = array("p1" => 10, "p2" => "somevalue");

// process in one hour
$options = array("schedule_time" => date("Y-m-d H:i:s", strtotime("+1 hour")));
$queue->createHttpJob("http://example.com/jobs/somejob.php", $params, $options);

// process every other day at 1:05 am
$options = array("schedule" => "5 1 */2 * *");
$queue->createHttpJob("http://example.com/jobs/somejob.php", $params, $options);
```

失败(和成功)可以用以下方式处理:

```
<?php
try {
    doSomething();
    ZendJobQueue::setCurrentJobStatus(ZendJobQueue::OK);
}
catch (Exception $e) {
    ZendJobQueue::setCurrentJobStatus(ZendJobQueue::STATUS_LOGICALLY_FAILED, $e->getMessage());
}
```

## 一个扩展的例子

假设您的 web 应用程序必须根据用户的请求生成并通过电子邮件发送一组报告。

通常，假设 PHP 不支持多处理，并且使用了同步通信模型，那么用户将不得不等待，直到所有请求的报告被逐一生成并通过电子邮件发送。

在这种情况下，使用作业队列不仅允许用户使用应用程序执行其他操作(因为工作将异步完成)，而且应用程序可以同时处理多个报告(因为作业可以并行执行)，因此大多数报告(如果不是全部)将在大约相同的时间完成。

```
<?php
function scheduleReport($reportList, $recipient) {
    // list of scheduled jobs
    $jobList = array();

    $queue = new ZendJobQueue();

    // check that Job Queue is running
    if ($queue->isJobQueueDaemonRunning() && count($reportList) > 0) {
    foreach ($reportList as $report) {
        $params = array("type" => $report["type"],
                        "start" => $report["start"],
                        "length" => $report["length"],
                        "recipient" => $recipient);
        $options = array("priority" => $report["priority"]);

        // execute the job in two minutes unless the priority is urgent
        if ($report["priority"] != ZendJobQueue::PRIORITY_URGENT) {
            $options["schedule_time"] = date("Y-m-d H:i:s", strtotime("+2 minutes"));
        }

        $jobID = $queue->createHttpJob("http://example.com/jobs/report.php", $params, $options);

        // add job ID to the list of successfully scheduled jobs
        if ($jobID !== false) {
           $jobList[] = $jobID;
       }
    }

    return $jobList;
}
```

`scheduleReport()`函数返回与每个计划报告相关的任务标识符列表。在这个函数中，`ZendJobQueue`类的`isJobQueueDaemonRunning()`方法验证适当的服务正在运行，并且作业可以被调度。

根据报告的优先级，可以将作业安排为立即运行或两分钟后运行(如果同时请求许多报告，这是为了减轻服务器的负载)。调度作业后，其 ID 将保存到所有成功创建的作业列表中。为了能够监控作业甚至取消作业，知道作业的 ID 是很重要的。

下面是对`scheduleReport()`函数的调用:

```
<?php
// setup request for a daily sales report and monthly financial report
$reportList = array(
    array("type" => "sales",
          "start" => "2011-12-09 00:00:00",
          "length" => 1,
          "priority" => ZendJobQueue::PRIORITY_URGENT),
    array("type" => "finance",
          "start" => "2011-11-01 00:00:00",
          "length" => 30,
          "priority" => ZendJobQueue::PRIORITY_NORMAL));

// schedule reports
$jobList = scheduleReport($reportList, "user@example.com");

// verify that reports were scheduled
if (empty($jobList)) {
    // show error message
}
```

如前所述，也可以取消计划的作业。一旦工作在进行中，它就会被完成。因此，如果请求的优先级不紧急，用户有两分钟的时间来取消计划报告的传递。

```
<?php
function cancelReport($jobID) {
    $queue = new ZendJobQueue();
    return $queue->removeJob($jobID);
}

if ($jobID !== false && cancelReport($jobID)) {
    // the job was successfully removed from the queue
}
```

`cancelReport()`功能只是将作业从尚未开始运行的计划报告队列中删除。

作业脚本如下所示:

```
<?php
function runReport() {
    $params = ZendJobQueue::GetParamList();

    try {
        $report = prepareReport($params["type"], $params["start"], $params["length"]);
        sendReport($params["recipient"], $report);
        ZendJobQueue::setCurrentJobStatus(ZendJobQueue::OK);
    }
    catch (Exception $e) {
        ZendJobQueue::setCurrentJobStatus(ZendJobQueue::STATUS_LOGICALLY_FAILED, $e->getMessage());
    }
}
```

`runReport()`函数最终根据提供的参数准备并发送报告。完成后，作业状态设置为成功(如果有错误，则设置为逻辑失败)。

## 可供选择的事物

当然，还有作业队列的替代方案。像 cron、 [pcntl_fork](http://php.net/manual/en/function.pcntl-fork.php) 这样的解决方案，甚至是通过 [PHP/Java Bridge](http://php-java-bridge.sourceforge.net/pjb/) 基于 Java 的解决方案，可能值得也可能不值得研究，这取决于您的需求。更有趣的工具也存在，比如 [Gearman](http://gearman.org/) 、 [node.js](http://nodejs.org/) 和 [RabbitMQ](http://www.rabbitmq.com/) 。

## 摘要

虽然 Zend Server 的 Job Queue 不是 PHP 中处理队列和并行处理的唯一方法，但它是一个由“PHP 公司”支持的非常直接的解决方案，并且非常容易开始使用。随着 Zend 的 PHPCloud 越来越成功，作业队列的应用将会更加广泛。

如果您想完整地查看本文中的[示例代码，您可以在 GitHub 上找到它。](https://github.com/phpmasterdotcom/ZendJobQueue)

图片经由[瓦里娜和杰伊·帕特尔](http://www.shutterstock.com/gallery-6203p1.html) / [摄影爱好者](http://www.shutterstock.com)

## 分享这篇文章