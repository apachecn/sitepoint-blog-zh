# 使用 IronWorker 在云中运行任务

> 原文：<https://www.sitepoint.com/running-tasks-in-the-cloud-with-ironworker/>

在本文中，我将向您展示如何使用 IronWorker 在云中运行代码，就像它在我们的 PHP 应用程序代码中运行一样。在云中运行任务有许多优势，例如:

*   处理器密集型任务可以从您的 web 服务器上卸载
*   更好的容错能力
*   代码的执行不会因为等待长时间运行的任务而被阻塞

以下是 IronWorker 对其服务的描述:

> 一个易于使用的可扩展任务队列，为云开发人员提供了一种简单的方法来卸载前端任务、运行计划作业以及在后台大规模处理任务。

## 术语表

在我们开始之前，让我们看看 IronWorker 和这篇文章中使用的一些术语，这些术语可能对您来说并不熟悉。

一个**工作者**是我们执行任务的代码。我们可以在本地编写和测试它，但最终我们会将它上传到 IronWorker 的服务器上，它将在那里执行，而不是在我们的 web 服务器上。

一个**任务**是代码的执行，要么按需执行(就像调用一个函数一样)，要么按计划执行(就像 cron 作业一样)。我们可以延迟执行，将它排队运行一定的次数，或者重复运行它，直到特定的时间和日期。

**有效负载**是用于执行任务的数据(使用函数类比，有效负载是传递给函数的参数)。

IronWorker **环境**本质上是云中的 Linux 沙箱，我们的任务在其上执行。

一个**项目**可以用来将工人分组在一起，也许是为了一个特殊的应用。

**HUD** 是 IronWorker 的网络界面，我们使用它来管理我们的项目和任务，查看结果，编辑和查看时间表，并跟踪我们的使用情况。

## 安装和配置

设置好一切需要几个步骤。

首先，我们需要用 IronWorker 创建一个帐户。它提供了一个免费层，每月有多达 200 小时的处理时间，这对于阅读本文来说应该绰绰有余。

我们还需要 IronWorker CLI 工具；[访问文档](http://dev.iron.io/worker/reference/cli/)获取完整的安装说明。但是如果您满足了安装它的先决条件——主要是您必须安装 Ruby 您可以用一行代码安装它:

```
gem install iron_worker_ng
```

接下来，我们需要从 GitHub 或 Composer 下载用于 IronWorker 的 PHP 客户端库。

```
{
    "require": {
        "iron-io/iron_worker": "dev-master"
    }
}
```

配置 IronWorker 最简单的方法是使用如下所示的 JSON 文件:

```
{
    "token":      "YOUR-TOKEN",
    "project_id": "YOUR-PROJECT-ID"
}
```

令牌标识您的帐户，项目 ID 可以设置为默认值，并由特定的工作人员覆盖。你可以在 HUD 中找到所有这些信息。

如果您将 JSON 文件命名为`.iron.json`(注意前面的点)并将它放在您的主目录中，它将用于为您的任何项目提供默认值。或者，您可以将其命名为`iron.json`(前面没有点)，并将其放在与 worker 相同的目录中。同一目录中的文件将覆盖主文件中的值，因此您可以轻松地为设置全局默认值，然后根据需要在本地为特定项目覆盖这些值。

或者，您可以使用环境变量设置这些参数:

```
IRON_TOKEN=MY_TOKEN
IRON_PROJECT_ID=MY_PROJECT_ID
```

## 一个 Hello World 示例

我们将从一个非常简单的例子开始，展示让任务在云中运行所涉及的内容。首先，创建一个名为`HelloWorld.php`的文件:

```
<?php
echo 'Hello World';
```

虽然这并没有做任何有用的事情，但是它将展示一个重要的观点，当我们运行它时，您将会看到——打印到 STDOUT 的任何内容(例如，使用`echo`、`print`等)。)由 IronWorker 记录并在 HUD 中启用。

然后用下面的代码创建一个名为`HelloWorld.worker`的文件:

```
runtime 'php'
exec 'HelloWorld.php'
```

正如您可能从第一行中推断的那样，我们不仅仅局限于 PHPIronWorker 支持多种语言。查看文档以获得受支持语言的列表。

现在我们需要将代码上传到 IronWorker 的服务器上。使用 CLI 工具，这轻而易举:

```
iron_worker upload HelloWorld
```

在幕后，CLI 工具创建一个包含我们代码的 ZIP 文件，将它上传到 IronWorker，并将其注册为我们项目的一个可用任务。每当您更改代码并再次运行 upload 命令时，它都会重新上传代码并创建一个新的修订版。你可以从 HUD 下载任何版本。

我们可以将任务排队以从我们的 PHP 应用程序中运行，如下所示:

```
<?php
require_once '../vendor/autoload.php';

$worker = new IronWorker();
$taskID = $worker->postTask('HelloWorld');
```

您可以在 HUD 中查看所有任务的当前状态。根据您的帐户设置，您可能还会收到电子邮件来通知您最新情况，尤其是在某个任务由于某种原因失败的情况下。

可选的第二个参数允许我们提供一个有效载荷，如果我们想发送一个的话。

```
$payload = array(
    'foo' => 'bar',
    'biz' => 'fuzz'
);

$taskID = $worker->postTask('MyTask', $payload);
```

有效载荷为我们编码并发送到钢铁工人服务器。在任务本身中，我们可以像这样简单地接收解码的有效载荷:

```
$payload = getPayload();
```

我们可以使用`postScheduleSimple()`方法，设置一个任务按计划运行，就像运行 cron 作业一样。简单的调度就像说“在 x 秒内运行这个”

```
// run the task in five minutes time (given in seconds)
$schedID = $worker->postScheduleSimple('HelloWorld', $payload, 5 * 60);
```

高级调度是通过`postScheduleAdvanced()`完成的，它允许我们设置第一次运行的时间、间隔，以及它应该停止的时间或运行它的次数。因为需要这么多参数，所以看一下方法的定义是有帮助的:

```
/**
 * Schedules task
 *
 * @param string        $name       Package name
 * @param array         $payload    Payload for task
 * @param int|DateTime  $start_at   Time of first run in unix timestamp format or as DateTime instance. Example: time()+2*60
 * @param int           $run_every  Time in seconds between runs. If omitted, task will only run once.
 * @param int|DateTime  $end_at     Time tasks will stop being enqueued in unix timestamp or as DateTime instance format.
 * @param int           $run_times  Number of times to run task.
 * @param int           $priority   Priority queue to run the job in (0, 1, 2). p0 is default.
 * @return string Created Schedule id
 */
public function postScheduleAdvanced($name, $payload = array(), $start_at, $run_every = null, $end_at = null, $run_times = null, $priority = null){
```

以下是它的一些用法示例:

```
// Run the task immediately, then every five minutes – a total of ten times 
$worker->postScheduleAdvanced('HelloWorld', $payload, time(), 5 * 60, null, 10);

// run task in 3 minutes time, every two minutes, five times 
$worker->postScheduleAdvanced('HelloWorld', $payload, time() + (3 * 60), 2 * 60, null, 5);

// run the task every hour for the next 24 hours
$worker->postScheduleAdvanced('HelloWorld', $payload, time(), 60 * 60, time() + (60 * 60 * 24));
```

## 一个更有用的例子——发送电子邮件

现在让我们构建一些稍微更有用的东西，并在这样做的过程中演示一种稍微不同的方法来构造或投影并上传我们的代码。我们将创建一个简单的上传脚本，而不是使用 CLI 工具上传 worker。

创建一个名为`worker`的新目录。将`iron.json`复制到其中，如果有必要，通过 HUD 创建一个新项目，并更新配置文件中的项目 ID。然后，[下载 PHPMailer](http://phpmailer.worxware.com/) ，放在 worker 目录下。

在`worker`目录中，创建文件`config.php`，该文件将存储 PHPMailer 的一些配置值。它应该是这样的:

```
<?php
return array(
    'smtp' => array(
        'host'        =>  'smtp.example.com',
        'port'        =>  465,
        'encryption'  =>  'ssl',
        'credentials' =>  array(
            'username' => 'username',
            'password' => 'password'
        )
    )
);
```

现在创建文件 Emailer.php:

```
<?php
require_once './phpmailer/class.phpmailer.php';

$payload = getPayload();
$config = require './config.php';

$mail = new PHPMailer();
$mail->IsSMTP();
$mail->Host = $config['smtp']['host'];
$mail->Port = $config['smtp']['port'];
$mail->SMTPAuth = true;
$mail->Username = $config['smtp']['credentials']['username'];
$mail->Password = $config['smtp']['credentials']['password'];              
$mail->SMTPSecure = $config['smtp']['encryption'];
$mail->From = 'no-reply@example.com';
$mail->FromName = 'SitePoint IronWorker Tutorial';
$mail->AddAddress($payload->recipient->email, $payload->recipient->name);
$mail->WordWrap = 100;
$mail->IsHTML(true);
$mail->Subject = $payload->subject;
$mail->Body    = $payload->body; 

if (!$mail->Send()) {
    print 'Could not send email: ' . $mail->ErrorInfo;                 
}
```

创建`Emailer.worker`:

```
runtime 'php'
exec 'Emailer.php'
```

并创建`upload.php`:

```
<?php
require_once '../vendor/autoload.php';

$worker = new IronWorker();
$worker->upload('worker/', 'Emailer.php', 'Emailer');
```

最后一行告诉 IronWorker 上传整个`worker`目录，其中当然不仅包括工作代码，还包括配置文件和支持库。第二个参数是要运行以执行任务本身的文件的名称，第三个参数只是工作者的标识符。

从命令行运行上传脚本:

```
php upload.php
```

我们可以像以前一样从 PHP 应用程序中运行该任务:

```
<?php
require_once '../vendor/autoload.php';

$worker = new IronWorker();

$payload = array(
    'recipient' => array(
        'name'  => 'Joe Bloggs',
        'email' => 'joe.bloggs@example.com'
    ),
    'subject'   => 'This is a test',
    'body'      => 'This is the body'
);

$taskID = $worker->postTask('Emailer', $payload);
```

如果您想要发送一批电子邮件，您可以执行一个简单的循环来将多个任务排队，或者等到前一个任务执行完毕，如下所示:

```
// loop...
$taskID = $worker->postTask('Emailer', $payload);
$worker->waitFor($taskID);
```

## 更多信息

你应该参考文档来理解你的代码将要运行的 [IronWorker 环境](http://dev.iron.io/worker/reference/environment)。重点包括(在撰写本文时):

*   操作系统为 Ubuntu Linux 12.04 x64。
*   PHP 的版本是 5.3.10。
*   安装了 php-curl、php-mysql、php-gdm 和 mongo 模块(尽管您需要调用 require_once('module-name ')来使用它们)。
*   [ImageMagick](http://www.imagemagick.org/) 、 [FreeImage](http://freeimage.sourceforge.net/) 、 [SoX](http://sox.sourceforge.net/) 和 [cURL](http://curl.haxx.se/) 可供选择。
*   任务可以访问多达 10Gb 的临时本地磁盘空间。
*   最大数据有效负载为 64Kb。
*   每个工作者有大约 320Mb 的可用内存，最大运行时间为 60 分钟。
*   默认情况下，每个项目最多可以有 100 个计划任务。

虽然超出了本文的范围，但是还有一些特性值得注意:

*   REST API–有一个可用的 API，您可以通过它查看或修改项目、任务和时间表。
*   **web hooks**–您可以设置在向指定端点发出 POST 请求时运行的任务。例如，您可以设置一个在向 GitHub 提交代码时运行的任务。
*   **状态**–您可以调用`getTaskDetails($taskID)`来获取任务的状态——“排队”、“完成”、“错误”等。
*   **进度**–您可以使用`setProgress($percent, $message)`在您的 worker 中设置任务的进度，这对于上面的`getTaskDetails()`是可用的。
*   **Logs**——如前所述，任何打印到 STDOUT 的东西都会被 IronWorker 记录下来，并且可以在 HUD 中看到。您也可以使用`getLog($taskID)`从 worker 访问它。

## 摘要

在本文中，我们研究了如何使用 IronWorker 在云中运行任务。我已经向您展示了如何上传 workers，如何对任务进行排队和调度，我还提到了一些可用的附加特性。如果你想进一步探索，我建议你花时间[阅读文档](http://dev.iron.io/worker/)并查看 GitHub 上的[范例库](https://github.com/iron-io/iron_worker_examples)。

## 分享这篇文章