# 比较 Beanstalkd、IronMQ 和亚马逊 SQS

> 原文：<https://www.sitepoint.com/message-queues-comparing-beanstalkd-ironmq-amazon-sqs/>

## 介绍

本文介绍了消息队列的概念，并讨论了三种特定消息队列服务的优缺点:Beanstalkd、IronMQ 和亚马逊 SQS。

本文中描述的任何信息在撰写时都是正确的，可能会有所更改。

## 什么是消息队列？

队列允许您存储元数据，以便日后处理作业。它们可以通过提供将任务委托给独立流程的灵活性来帮助 SOA(面向服务的架构)的开发。如果应用正确，队列可以通过减少加载时间来显著提高网站的用户体验。

### 消息队列的优势:

*   异步:现在排队，以后运行。
*   解耦:分离应用程序逻辑。
*   弹性:即使部分应用程序失败，也不会导致整个应用程序崩溃。
*   冗余:如果作业失败，可以重试。
*   保证:确保作业得到处理。
*   可伸缩性:许多工作人员可以处理队列中的单个作业。
*   分析:可以帮助识别性能问题。

### 消息队列的缺点:

*   异步:你必须等到一个任务完成。
*   Load:队列中的每个作业在被处理之前都必须等待。如果一个作业溢出，它会影响后续的每个作业。
*   架构:应用程序的设计需要考虑队列。

### 消息队列的用例:

任何耗时的过程都可以放在队列中:

*   从第三方 API 发送/接收数据
*   发送电子邮件
*   生成报告
*   运行劳动密集型流程

您还可以创造性地使用队列——锁定作业，以便一次只有一个用户可以访问信息

## 服务

有许多服务可以用来实现消息队列，本文概述了 Beanstalkd、IronMQ 和亚马逊 SQS 之间的区别。

### Beanstalkd

Beanstalkd 是“…一个简单、快速的工作队列”。它是在麻省理工学院许可下作为开源软件发布的。它有很好的文档记录，经过单元测试，可以免费下载在你自己的服务器上运行。这个架构是从 [memcached](http://memcached.org/) 借鉴来的，它被专门设计成一个消息队列。

作者 Dave Kennedy 在 SitePoint 上发表了一篇名为《T2》的文章，介绍了如何在 Ruby 上使用 Beanstalkd。

### IronMQ

IronMQ 是一个托管的 RESTful web 服务。有一个面向开发者的[免费层](http://www.iron.io/pricing)和许多其他商业应用的订阅层。

### SQS

[亚马逊 SQS](http://aws.amazon.com/sqs/) 是一个[廉价的](http://calculator.s3.amazonaws.com/calc5.html)托管解决方案，用于实现消息队列。它是亚马逊网络服务(AWS)的一部分。亚马逊提供了一个[免费等级](http://aws.amazon.com/free/)来评估他们的网络服务，其中包括 SQS。

## 服务器设置

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 自托管 | 远程托管 | 远程托管 |

### Beanstalkd

在 Linux 和 Mac OS X 上运行。阅读 Beanstalkd 网站上的[安装说明，了解如何在您的系统上运行它的详细信息。Beanstalkd 服务器在 Windows 上不工作。](http://kr.github.io/beanstalkd/download.html)

### 铁姆克和 SQS

IronMQ 和亚马逊 SQS 是基于云的网络服务。不需要在你的服务器上安装任何应用程序，你只需要注册一个账户并建立一个队列。

## 服务水平协议

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 没有人 | 每月 99.95% | 没有人 |

### Beanstalkd

由于 Beanstalkd 是您托管的服务器，因此您有责任确保其可用性。

### IronMQ

[铁。IO 签订了服务水平协议](http://www.iron.io/sla)，在任何月度计费周期内，正常运行时间百分比至少为 99.95%。他们的*白金*套餐(2450 美元/月)有定制合同条款，其中包括服务水平协议。他们提供服务积分退款。

### SQS

亚马逊没有针对 SQS 的特定服务水平协议。他们确实有可用的支持服务，可以覆盖 SQS，但需要额外付费。

## 体系结构

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 推动(插座) | HTTP Web 服务 | HTTP Web 服务 |

### Beanstalkd

通过推送套接字进行通信，在提供商和工作人员之间提供即时通信。

当提供程序将作业排入队列时，如果作业已连接并准备就绪，工作人员可以立即保留它。作业被保留，直到工人发送响应(删除、掩埋等)。)

### IronMQ

SQS 是一个托管的 RESTful web 服务。

对 IronMQ 有推送式的支持。每当提供者将作业排入队列时，就可以调用[订阅者。通常，您会希望使用标准的 RESTful 服务来对作业进行入队和出队，而不是使用推送方法。](http://dev.iron.io/mq/reference/push_queues/)

### SQS

SQS 是一个托管的网络服务。

没有人支持 SQS。您必须定期轮询以检查队列中是否有作业。

SQS 可以使用称为`Message Receive Wait Time`(缺省值:0 秒，最大值:20 秒)的长轮询来在工人等待作业时保持连接打开。这意味着更少的请求和更长的套接字打开时间。

## 客户端库

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 开放源码 | 正式的 | 正式的 |

### Beanstalkd

有许多开源的 [Beanstalkd 客户端库](https://github.com/kr/beanstalkd/wiki/client-libraries)可以用各种编程语言获得。这些都是来自 Beanstalkd 的独立项目。

### IronMQ

IronMQ 客户端库由 Iron 提供。IO，可以从开发中心下载。

如果您希望在两种服务之间灵活切换，也可以使用带有 IronMQ 的 Beanstalkd 客户端库；然而，一些命令(例如:踢，埋)是不支持的。您可能还需要手动执行 *oauth* 命令来连接到服务。

### SQS

AWS 客户端库包括 SQS 客户端库。这些都是由 Amazon 提供的，并且可以在许多编程语言中使用。

## 管理界面

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 开放源码 | 仪表盘 | 安慰 |

### Beanstalkd

默认情况下，不会分发任何图形管理界面。在 [Beanstalkd 工具页面](https://github.com/kr/beanstalkd/wiki/Tools)上可以找到一些帮助调试和管理的开源项目。

### IronMQ

IronMQ 仪表板管理队列。它包含一个有用的教程，描述了如何设置队列，并向您展示了如何通过 cURL 向队列添加作业(IronMQ: messages)。

该接口允许您在 AJAX 驱动的网站中管理队列。您可以从仪表板视图创建、读取和删除作业、查看历史信息以及管理队列配置。

### SQS

AWS 管理控制台允许你管理 SQS。该界面建立在无状态协议之上，因此您需要按刷新按钮来获取最新信息。

您可以创建、读取和删除作业(SQS:消息)并管理队列配置。

## 裁员

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 客户端 | 基于云的 | 基于云的 |

### Beanstalkd

冗余是在客户端处理的，如果一台服务器宕机，您将会失去工作。

Beanstalkd 包含一个在二进制日志中存储作业的选项。您必须使用`-b`选项启动 Beanstalkd，但是恢复队列是一项手动任务，需要访问服务器磁盘。

### IronMQ

IronMQ 是基于云的服务，具有高持久性、可用性和冗余性。

### SQS

作业存储在托管区域中的多台服务器上。这种方法确保了服务的可用性，并且永远不会丢失工作。

## 安全性

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 没有人 | 代币 | 密钥和秘密 |

### Beanstalkd

连接到 Beanstalkd 不需要身份验证。提供商能够将作业排队，工人能够保留作业，而无需通过安全模型。因此，强烈建议创建一个防火墙来阻止与运行 Beanstalkd 的端口的外部连接。

### IronMQ

您可以通过项目设置邀请协作者使用您的消息队列。对应用的认证是通过熨斗来完成的。IO 令牌和项目 ID。

### SQS

对 SQS 的认证是通过 Amazon API 密钥和秘密实现的。可以通过 AWS 管理控制台授予和撤销其他 AWS 帐户访问您的队列的权限。

## 速度

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 快的 | 互联网延迟 | 互联网延迟 |

### Beanstalkd

Beanstalkd 非常快，因为它应该与它的提供商和工作人员在同一个网络上。Beanstalkd 有时会非常快，如果提供者将一个作业放入队列中，然后调用 MySQL，工作人员可能会在 MySQL 完成执行之前取走您的作业。

### IronMQ

当请求通过 HTTP 发送到 IronMQ RESTful web 服务时，延迟会增加。

### SQS

当请求通过 HTTP 发送到 SQS web 服务时，它们的延迟会增加。

工作可能不会马上被取走，因为它们需要分布在不同的服务器和数据中心。如果应用程序、提供者或工作者托管在 EC2 实例上，那么这种延迟应该可以忽略。

当您将作业排入 SQS 队列时，它可能不会立即可用。作业必须传播到其他服务器。通常最多等待一秒钟。

## 忠诚

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 先进先出。比较 LIFO | 先进先出。比较 LIFO | 没有保证 |
| 可优先排序 | 没有优先权 | 没有优先权 |

### Beanstalkd

队列是先进先出的。具有较高重要性的作业可以被区分优先级，这将影响作业出列的顺序。

### IronMQ

队列是先进先出的。工作不能被优先化。

### SQS

作业不会按照它们进入队列的顺序出现。因为 SQS 是一种分布式服务，所以作业将在不同的时间在每台服务器上可用。在为 SQS 做设计时，这一点是非常重要的。

## 一次性提货

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 担保 | 担保 | 不保证 |

一次性拾取描述了这样一种限制，即除非一个工作线程超时，否则两个或更多工作线程永远不会并行运行同一作业。

### Beanstalkd

Beanstalkd 基于套接字的架构确保了一次性拾取。

### IronMQ

IronMQ 保证一次性取货。

### SQS

因为 SQS 是分布式服务，所以不能保证一次性取件(但这不太可能)。

## 自动防故障

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 僵尸插座 | 超时 | 超时 |

### Beanstalkd

如果工作线程在设定的时间内没有响应 Beanstalkd，或者套接字在没有响应作业的情况下关闭，则作业会自动返回到队列中。

然后，下一个发出请求的工人就可以立即取走它了(不需要踢它)。

### 铁姆克& SQS

工人连接到一个队列并预订一个工作。从这一刻起，在作业被释放并可供工人再次预订之前，工人有固定的时间从队列中删除作业。

## 创建新队列

| Beanstalkd | IronMQ | 亚马逊 SQS |
| --- | --- | --- |
| 自动的 | 自动和手动 | 指南 |

### Beanstalkd

当作业排队时，会自动创建队列(Beanstalkd: tubes)。它们不需要手动创建。

### IronMQ

要求您在仪表板中创建一个*项目*。一个项目包含许多队列。队列可以在作业排队时自动创建，也可以通过仪表板中的配置手动创建。

### SQS

必须从 SQS 的 AWS 管理控制台手动设置队列。每个队列将生成一个唯一的 URL 作为队列名。

记下地区(例如:美国-西方-1、欧盟-西方-1 等。)是队列所属的，因为它需要连接到 SQS。

## 框架集成

### 拉勒韦尔

Laravel 框架有一个优秀的内置包装器，封装了 Beanstalkd、IronMQ 和亚马逊 SQS 的 T2 消息队列。您可以通过配置来更改服务器，而无需更改任何应用程序。

## PHP 代码示例

这些代码示例向您展示了如何连接到服务器，如何从队列中对作业进行入队、保留和出队。如果抛出异常，它将埋葬作业(如果服务器支持的话)。

尝试在作业入队后停止执行，并使用管理工具调试队列。

### Beanstalkd

`composer.json`

```
{
    "require": {
        "pda/pheanstalk": "dev-master"
    }
}
```

`beanstalkd.php`

```
<?php

/* 1\. Setup & connect */

// Include composer libraries
require 'vendor/autoload.php';

$queue_name = 'default';
$job_data   = 'lorem ipsum';

$beanstalkd = new Pheanstalk_Pheanstalk('127.0.0.1:11300');

/* 2\. Provider */

// Enqueue a job
$beanstalkd
  ->useTube($queue_name)
  ->put($job_data);

/* 3\. Worker */

// Loop through all jobs
while ($job = $beanstalkd->watch($queue_name)->reserve(5)) {
    try {
        $job_data = $job->getData();

        echo $job_data . PHP_EOL;

        // Dequeue a job
        $beanstalkd->delete($job);
    } catch (Exception $e) {
        // Bury a job
        $beanstalkd->bury($job);
        echo $e->getMessage();
    }
}
```

### IronMQ

`composer.json`

```
{
    "require": {
        "iron-io/iron_mq": "dev-master"
    }
}
```

`iron_mq.php`

```
<?php

/* 1\. Setup & connect */

// Include composer libraries
require 'vendor/autoload.php';

$queue_name = 'default';
$job_data   = 'lorem ipsum';

$iron_mq = new IronMQ(array(
    'token'      => '{token}',
    'project_id' => '{project_id}'
));

/* 2\. Provider */

// Enqueue a job
$iron_mq->postMessage($queue_name, $job_data);

/* 3\. Worker */

// Loop through all jobs
while ($job = $iron_mq->getMessage($queue_name)) {
    try {
        $job_data = $job->body;

        echo $job_data . PHP_EOL;

        // Dequeue a job
        $iron_mq->deleteMessage($queue_name, $job->id);
    } catch (Exception $e) {
        // Bury a job
        // There is no bury in IronMQ
        echo $e->getMessage();
    }
}
```

### SQS

`composer.json`

```
{
    "require": {
        "aws/aws-sdk-php": "2.4.*@dev"
    }
}
```

`sqs.php`

```
<?php

/* 1\. Setup & connect */

// Include composer libraries
require 'vendor/autoload.php';

$queue_name = 'https://sqs.{region}.amazonaws.com/{id}/{queue_name}';
$job_data   = 'lorem ipsum';

$aws = \Aws\Common\Aws::factory(array(
    'key'    => '{key}',
    'secret' => '{secret}',
    'region' => '{region}'
));

$sqs = $aws->get('sqs');

/* 2\. Provider */

// Enqueue a job
$sqs->sendMessage(array(
    'QueueUrl'    => $queue_name,
    'MessageBody' => $job_data
));

/* 3\. Worker */

// Handle one job
$result = $sqs->receiveMessage(array(
    'QueueUrl' => $queue_name
));

if (!$result) {
    // No jobs
    exit;
}

$messages = $result->getPath('Messages');
if (!$messages) {
    // No jobs
    exit;
}

foreach ($messages as $message) {
    try {
        $job_data = $message['Body'];

        echo $job_data . PHP_EOL;

        // Dequeue a job
        $sqs->deleteMessage(array(
            'QueueUrl'      => $queue_name,
            'ReceiptHandle' => $message['ReceiptHandle']
        ));
    } catch (Exception $e) {
        // Bury a job
        // There is no bury in SQS
        echo $e->getMessage();
    }
}
```

## 邮件队列提示

无论您选择哪种服务，以下是一些保持队列健壮的提示:

### 元数据序列化

只要不超出服务器作业数据大小的限制，您的作业可以包含您喜欢的任何数据。在您的作业主体中使用 JSON，使元数据易于传输。

```
// Encode for enqueuing:
$raw_data = (object) array('id' => 100);
$job_data = json_encode($raw_data);

// Decode from dequeuing:
$raw_data = '{"id":100}';
$job_data = json_decode($raw_data);
```

### 限制您的作业数据大小

尽量不要用太多的元数据来拥挤工作。如果您可以在数据库中存储一些信息，并且只对一个 ID 进行排队以供以后处理，那么您的队列将会更加健壮并且更容易调试。

```
// Good:
$raw_data = (object) array('id' => 100);

// Not as good ...
// But sometimes necessary when there is
// no database access on a worker:
$raw_data = (object) array(
    'id' => 100,
    'color' => 'black',
    'background' => 'white'
);
```

### 跟踪作业状态

如果由于某种原因，一个已经处理过的项目重新进入队列，您可能不希望它被重新处理。不幸的是，作业数据并不是唯一的，在数据库中跟踪作业的状态是很重要的。

这可以简单到在 jobs 表中有一列将一个项目标记为已处理。如果作业已经被处理，您可以从队列中删除它。

## 术语

一些单词在 Beanstalkd 和亚马逊 SQS 的用法不同。这里有一个简短的翻译列表:

| Beanstalkd | 亚马逊 SQS | IronMQ |
| --- | --- | --- |
| 管 | 长队 | 长队 |
| 职位 | 消息 | 消息 |
| 作业数据 | 消息正文 | 消息正文 |
| 放 | 发送消息 | 邮政 |
| 保留 | 接收消息 | 得到 |
| 删除 | 删除邮件 | 删除 |
| 运行时间 | 可见性超时 | 超时 |
| 耽搁 | 延期交付 | 耽搁 |
| – | 保存期 | 过期时间 |

## 词汇表

使用队列时，您可能会遇到这些术语:

`Bury (a job)`–将作业置于失败状态。作业不能被重新处理，直到它被手动*踢回*队列。不被 IronMQ 和 SQS 支持。

`Consumer`–见工人。

`Delay`–将任务推迟一段预定的时间发送给工人。

`Delete (a job)`–参见出列。

`Dequeue`–将任务标记为已完成，并将其从队列中删除。

`Enqueue`–将工作添加到等待工人的队列中。

`FIFO`–描述队列中作业的处理方式，即先进先出。这是最常见的消息队列类型。

`FILO`–描述队列中作业的处理方式，即先进后出。

`Job`–包含元数据的队列中的延迟任务，用于标识等待处理的任务。类似于数据库行。

`Kick (a job)`–将先前被掩埋的工作返回到队列中，以备工人拾取。不被 IronMQ 和 SQS 支持。

`Provider`–连接到消息服务器以创建作业的客户端。

`Queue`–一种将相似工作分组到一个队列中的方法。类似于数据库表。

`Reserve (a job)`–将一项工作交付给一个工人，并锁定该工作，防止其被交付给另一个工人。

`Worker`–连接到消息服务器以保留、删除和隐藏作业的客户端。这些完成了加工中的劳动密集型部分。

## 结论

消息队列服务没有灵丹妙药。Beanstalkd、IronMQ 和亚马逊 SQS 都有自己的优势和劣势，可以为你所用。本文将为您提供足够的信息，帮助您做出明智的决定，选择最适合您的技能水平和项目需求的服务。

您将使用哪种消息队列服务？如果您目前使用队列，您会考虑转换吗？你有没有以一种非常规的方式使用消息队列来帮助别人？留下评论让大家知道。

## 分享这篇文章