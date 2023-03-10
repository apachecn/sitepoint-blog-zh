# Crunz 让框架无关的 PHP Cronjobs 变得简单！

> 原文：<https://www.sitepoint.com/framework-agnostic-php-cronjobs-made-easy-with-crunz/>

在本文中，我们将了解一个相对较新的作业调度库，名为 [Crunz](https://github.com/lavary/crunz) 。Crunz 是一个框架无关的库，受 [Laravel 的任务调度器](https://laravel.com/docs/master/scheduling)的启发，但在许多方面有所改进。完全披露:我是作者，欢迎对如何进一步改进它的贡献和评论！

![Clock photo](img/2dcda03deddfdf59b431673a8bd139f1.png)

在开始之前，你应该对 cronjobs 有一个牢固的了解，所以如果你不熟悉它们是如何工作的，请阅读我们的深入的[演练](https://www.sitepoint.com/a-comprehensive-crash-course-into-cronjobs/)。

## 装置

要安装它，我们像往常一样使用 Composer:

```
composer require lavary/crunz 
```

一个名为 **crunz** 的命令行实用程序将被符号链接到我们项目的`vendor/bin`。这个命令行实用程序提供了一组有用的命令，我们将很快对此进行讨论。

## 它是如何工作的？

我们没有在 crontab 文件中安装 cron 作业，而是使用 Crunz 接口在一个或多个 PHP 文件中定义它们。

这里有一个基本的例子:

```
<?php
// tasks/backupTasks.php

use Crunz\Schedule;

$schedule = new Schedule();
$schedule->run('cp project project-bk')       
         ->daily();

return $schedule; 
```

为了运行这些任务，我们安装了一个普通的 cron 作业(一个 crontab 条目),它每分钟运行**,并将责任委托给 Crunz 的事件运行器:**

```
* * * * * /project/vendor/bin/crunz schedule:run 
```

命令`schedule:run`负责收集所有的 PHP 任务文件，并运行到期的任务。

## 任务文件

任务文件类似于 crontab 文件。就像 crontab 文件一样，它们可以包含一个或多个任务。

为了创建一个任务文件，我们需要决定将它们保存在哪里。只要我们让克鲁兹知道地点，地点并不重要。通常，我们在项目根目录下的`tasks/`中创建任务文件。然而，如果有理由的话，我们可以把它们放在项目目录之外。

默认情况下，Crunz 假设所有任务文件都位于项目根目录下的`tasks/`目录中。

有两种方法可以指定源目录:配置文件(下面将详细介绍)和作为事件运行器命令的参数:

```
* * * * * /project/vendor/bin/crunz schedule:run /path/to/tasks/directory 
```

### 创建简单的任务

让我们创建我们的第一个任务。

```
<?php
// tasks/FirstTasks.php

use Crunz\Schedule;

$schedule = new Schedule();

$schedule->run('cp project project-bk')       
         ->daily()
         ->description('Create a backup of the project directory.');

// ...

// IMPORTANT: You must return the schedule object
return $schedule; 
```

在前面的代码中，我们首先创建了一个`Schedule`类的实例。`run()`指定要执行的**命令**。`daily()`每天运行该命令，`description()`向任务添加描述(描述有助于识别日志文件中的任务)。

创建任务文件有一些惯例。文件名应该以`Tasks.php`结尾，除非我们通过配置设置改变它。此外，我们**必须**在每个文件的末尾返回`Schedule`类的实例，否则，文件中的所有任务都将被事件运行器跳过。

由于 Crunz 递归地扫描任务目录，我们可以将所有的任务放在一个文件中，或者根据它们的用途放在不同的文件(或目录)中。这种行为帮助我们有一个组织良好的任务目录。

## 命令

我们可以通过使用`run()`运行**任何**命令或 PHP 闭包。该方法接受两个参数:**要执行的命令或闭包**，如果第一个参数是命令，**命令选项**(作为关联数组)。

```
<?php
use Crunz\Schedule;

$schedule = new Schedule();

$schedule->run('/usr/bin/php backup.php', ['--destination' => 'path/to/destination'])       
         ->everyMinute()
         ->description('Copying the project directory');

return $schedule; 
```

在上面的例子中，`--destination`是一个受`backup.php`脚本支持的选项。

## 关闭

除了命令之外，还可以通过将 PHP 闭包传递给`run()`方法来调度它。

```
<?php
use Crunz\Schedule;

$schedule = new Schedule();

$x = 'Some data';
$schedule->run(function() use ($x) { 
   echo 'Doing some cool stuff in here with ' . $x;
})       
->everyMinute()
->description('Cool stuff');

return $schedule; 
```

如果有任何级别的 PHP 错误，它将被捕获、记录和报告。

## 更改目录

也可以在运行任务前更改当前工作目录——通过使用`in()`方法:

```
<?php

// ...

$schedule->run('./deploy.sh')
         ->in('/home')
         ->daily();
// ...

return $schedule; 
```

## 执行频率

有多种方法可以指定任务应该运行的时间和频率分别为**和**。我们可以结合这些方法来获得我们想要的频率。****

### 时间单位

频率法通常以`ly`结尾，如`hourly()`、`daily()`、`weekly()`、`monthly()`、`quarterly()`和`yearly()`。

```
<?php
// ...
$schedule->run('/usr/bin/php backup.php')       
         ->daily();
// ... 
```

上述任务将在每天午夜运行**。**

这里还有一个，在每月的**第一天运行。**

```
<?php
// ...
$schedule->run('/usr/bin/php email.php')       
         ->monthly();
// ... 
```

用这组方法安排的所有事件都发生在该时间单位的**开始**处。例如，`weekly()`将在周日运行事件，而`monthly()`将在每月的第一天运行事件。

### 动态方法

动态方法为我们提供了各种各样的动态频率选择。我们只需要遵循这个模式:

```
every[NumberInCamelCaseWords]Minute|Hour|Day|Months? 
```

该方法应该以单词`every`开头，后面跟着一个数字，以下列时间单位之一结尾:**分钟、小时、日和月**。

结尾的`s`是可选的，只是为了语法的缘故。

也就是说，以下方法是有效的:

*   `everyFiveMinutes()`
*   `everyMinute()`
*   `everyTwelveHours()`
*   `everyMonth()`
*   `everySixMonths()`
*   `everyFifteenDays()`
*   `everyFiveHundredThirtySevenMinutes()`
*   `everyThreeThousandAndFiveHundredFiftyNineMinutes()`
*   …

这是我们在任务文件中使用它的方式:

```
<?php
// ...

$schedule->run('/usr/bin/php email.php')       
         ->everyTenDays();

$schedule->run('/usr/bin/php some_other_stuff.php')       
         ->everyThirteenMinutes();
// ...

return $schedule; 
```

### 在特定时间运行事件

要安排一次性任务，我们可以像这样使用`on()`方法:

```
<?php
// ...
$schedule->run('/usr/bin/php email.php')       
         ->on('13:30 2016-03-01');
// ... 
```

上述任务将在 2016 年 3 月 1 日下午 01:30 运行。

`on()`接受 PHP 的 [strtotime](http://php.net/manual/en/function.strtotime.php) 函数解析的任何日期格式。

为了仅指定时间，我们使用`at()`:

```
<?php
// ...
$schedule->run('/usr/bin/php script.php')       
         ->daily()
         ->at('13:30');
// ... 
```

我们可以使用`dailyAt()`得到相同的结果:

```
<?php
// ...
$schedule->run('/usr/bin/php script.php')       
         ->dailyAt('13:30');
// ... 
```

如果我们只把时间传递给`on()`，和使用`at()`效果一样

```
<?php
// ...
$schedule->run('/usr/bin/php email.php')       
         ->mondays()
         ->on('13:30');

// is the sames as
$schedule->run('/usr/bin/php email.php')       
         ->mondays()
         ->at('13:30');
// ... 
```

### 平日

Crunz 还提供了一组指定一周中某一天的方法。这些方法旨在用作约束，不应单独使用。原因是 weekday 方法只是修改 cron 作业表达式的`Day of Week`字段。

考虑下面的例子:

```
<?php
// Cron equivalent:  * * * * 1
$schedule->run('/usr/bin/php email.php')       
         ->mondays(); 
```

乍一看，任务似乎在每周一运行**，但是因为它只修改了 cron 作业表达式的“星期几”字段，所以任务在每周一**每分钟运行**。**

这是使用工作日方法的正确方式:

```
<?php
// ...
$schedule->run('/usr/bin/php email.php')       
         ->everyThreeHours()
         ->mondays();
// ... 
```

在上面的任务中，我们使用`mondays()`作为约束，在星期一每三个小时运行一次任务**。**

### 设置单个字段

克鲁兹的方法并不局限于所解释的现成方法。我们还可以设置单独的字段来组成我们的自定义频率。这些方法要么接受一组值，要么列出用逗号分隔的参数:

```
<?php
// ...
$schedule->run('/usr/bin/php email.php')       
         ->minute(['1-30', 45, 55])
         ->hour('1-5', 7, 8)
         ->dayOfMonth(12, 15)
         ->month(1); 
```

或者:

```
<?php
// ...
$schedule->run('/usr/bin/php email.php')       
         ->minute('30')
         ->hour('13')
         ->month([1,2])
         ->dayofWeek('Mon', 'Fri', 'Sat');

// ... 
```

### 经典的方式

我们也可以用老方法进行调度，就像我们在 crontab 文件中做的那样:

```
<?php
$schedule->run('/usr/bin/php email.php')
         ->cron('30 12 * 5-6,9 Mon,Fri'); 
```

## 任务生存期

在 crontab 条目中，我们不容易指定任务的生存期(任务活动的时间段)。有了 Crunz，就有可能:

```
<?php
//
$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->from('12:30 2016-03-04')
         ->to('04:55 2016-03-10');
 // 
```

或者，我们可以使用`between()`方法得到相同的结果:

```
<?php
//
$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->between('12:30 2016-03-04', '04:55 2016-03-10');

 // 
```

如果我们不指定日期部分，任务将在每天激活**，但仅在指定的持续时间内有效:**

```
<?php
//
$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->between('12:30', '04:55');

 // 
```

上述任务在每天**下午 12:30**到**下午 4:55**之间每五分钟运行一次**。**

## 工作条件

另一件我们在传统的 crontab 文件中不容易做到的事情是为运行任务创造条件。输入`when()`和`skip()`方法。

考虑以下代码:

```
<?php
//
$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->between('12:30 2016-03-04', '04:55 2016-03-10')
         ->when(function() {
           if ($some_condition_here) { return true; }
         });

 // 
```

`when()`接受回调，该回调必须返回`TRUE`才能运行任务。例如，当我们需要在执行一个需要大量资源的任务之前检查我们的资源时，这非常有用。我们也可以通过使用`skip()`方法在特定条件下跳过一个任务。如果传递的回调返回`TRUE`，任务将被跳过。

```
<?php
//
$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->between('12:30 2016-03-04', '04:55 2016-03-10')
         ->skip(function() {
             if ($some_condition_here) { return true; }  
    });

 // 
```

我们可以在一个任务中多次使用这些方法。它们是按顺序评估的。

## 配置

YAML 格式提供了一些配置选项。强烈建议您拥有自己的配置文件副本，而不是修改原始文件。

要创建配置文件的副本，首先我们需要发布配置文件:

```
crunz publish:config
The configuration file was generated successfully 
```

因此，将在我们项目的根目录中创建配置文件的副本。

配置文件如下所示:

```
# Crunz Configuration Settings

# This option defines where the task files and
# directories reside.
# The path is relative to the project's root directory,
# where the Crunz is installed (Trailing slashes will be ignored).
source: tasks

# The suffix is meant to target the task files inside the ":source" directory.
# Please note if you change this value, you need
# to make sure all the existing tasks files are renamed accordingly.
suffix: Tasks.php

# By default the errors are not logged by Crunz
# You may set the value to true for logging the errors
log_errors: false

# This is the absolute path to the errors' log file
# You need to make sure you have the required permission to write to this file though.
errors_log_file:

# By default the output is not logged as they are redirected to the
# null output.
# Set this to true if you want to keep the outputs
log_output: false

# This is the absolute path to the global output log file
# The events which have dedicated log files (defined with them), won't be
# logged to this file though.
output_log_file:

# This option determines whether the output should be emailed or not.
email_output: false

# This option determines whether the error messages should be emailed or not.
email_errors: false

# Global Swift Mailer settings
#
mailer:
    # Possible values: smtp, mail, and sendmail
    transport: smtp
    recipients:
    sender_name:
    sender_email:

# SMTP settings
#
smtp:
    host:
    port:
    username:
    password:
    encryption: 
```

每次我们运行 Crunz 命令时，它都会查看项目的根目录，看看是否有用户修改过的配置文件。如果配置文件不存在，它将使用默认文件。

## 平行度和锁定机构

Crunz 并行运行预定的事件(在单独的进程中)，因此所有具有相同执行频率的事件将同时异步运行。为了实现这一点，Crunz 利用 [symfony/Process](http://symfony.com/doc/current/components/process.html) 来运行子进程中的任务。

如果一个任务的执行持续到下一个时间间隔，或者甚至超过这个时间间隔，我们说任务的相同实例是重叠的。在某些情况下，这不是问题，但是当这些任务修改数据库数据或文件时，可能会导致意外行为，甚至数据丢失。

为了防止关键任务重叠，Crunz 通过`preventOverlapping()`提供了一个锁定机制，确保如果前一个实例已经在运行，没有任务运行。在后台，Crunz 为每个任务创建一个特殊的文件，存储最后一个运行实例的进程 ID。每次任务将要运行时，它都会读取相应任务的进程 ID，并检查它是否仍在运行。如果不是，就该运行新的实例了。

```
<?php
//
$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->preventOverlapping();
 // 
```

## 保持产量

Cron 作业通常有输出，通常通过电子邮件发送给 crontab 文件的所有者，或者由 crontab 文件中的`MAILTO`环境变量设置的一个或多个用户。

我们还可以使用`>>`操作符将标准输出重定向到一个物理文件:

```
* * * * * /command/to/run >> /var/log/crons/cron.log 
```

这在 Crunz 已经实现了自动化。为了自动将每个事件的输出发送到日志文件，我们可以在配置文件中这样设置`log_output`和`output_log_file`:

```
# Configuration settings

## ...
log_output:      true
output_log_file: /var/log/crunz.log
## ... 
```

这将把事件的输出(如果成功执行)发送到`/var/log/crunz.log`。但是，我们需要确保我们被允许写入各自的目录。

如果我们需要在每个事件的基础上记录输出，我们可以像这样使用`appendOutputTo()`或`sendOutputTo()`方法:

```
<?php
//
$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->appendOutputTo('/var/log/crunz/emails.log');

 // 
```

方法`appendOutputTo()` **追加**输出到指定的文件。为了在每次运行后用新数据覆盖日志文件，我们使用了`saveOutputTo()`。

通过在配置文件中设置`email_output`和`mailer`设置，也可以将输出作为电子邮件发送给一组收件人。

## 错误处理

使用 Crunz 处理错误非常容易。我们可以配置 Crunz 来记录错误或通过电子邮件通知我们。此外，我们可以定义几个回调函数，以便在出错时调用。

为了记录每次运行期间可能出现的错误，我们可以使用配置文件中的`log_error`和`error_log_file`设置，如下所示:

```
# Configuration settings

# ...
log_errors:      true
errors_log_file: /var/log/error.log
# ... 
```

因此，如果由于某种原因事件的执行不成功，错误消息将被附加到指定的错误日志文件中。每个条目都提供有用的信息，包括事件发生的时间、事件的描述、导致错误的已执行命令以及错误消息本身。

就像事件的输出一样，也可以通过电子邮件将错误发送给一组接收者——通过在配置文件中设置`email_error`和`mailer`设置。

### 错误回调

我们可以根据需要设置尽可能多的回调，以便在出错时运行。它们将按顺序被调用:

```
<?php

use Crunz\Schedule;
$schedule = new Schedule();

$schedule->add('command/to/run')
         ->everyFiveMinutes();

$schedule
->onError(function(){
   // Send mail
})
->onError(function(){
   // Do something else
});

return $schedule; 
```

## 预处理和后处理挂钩

有些时候我们想在一个事件前后做一些操作。这可以通过使用`before()`和`after()`方法将预处理和后处理回调附加到各自的事件或时间表对象，并将回调函数传递给它们来实现。

我们可以在事件和调度级别上进行预处理和后处理回调。调度级别的预处理回调将在任何事件(在调度对象中)开始之前被调用。所有事件完成后，将调用调度级别的后处理回调——无论是成功还是有问题。

```
<?php
// ...

$schedule = new Schedule();

$schedule->run('/usr/bin/php email.php')
         ->everyFiveMinutes()
         ->before(function() { 
             // Do something before the task runs
         })
         ->before(function() { 
                 // Do something else
         })
         ->after(function() {
             // After the task is run
         });

$schedule

->before(function () {
   // Do something before all events
})
->after(function () {
   // Do something after all events are finished
}
->before(function () {
   // Do something before all events
});

//  ... 
```

通过链接，我们可以根据需要多次使用这些方法。只有当事件执行成功时，才会调用基于事件的执行后回调。

## 其他有用的命令

我们已经使用了一些`crunz`命令，比如`schedule:run`和`publish:config`。

要查看`crunz`的所有有效选项和参数，我们可以运行以下命令:

```
vendor/bin/crunz --help 
```

### 列出任务

其中一个命令是`crunz schedule:list`，它以表格形式列出定义的任务(在收集的`*.Tasks.php`文件中)。

```
vendor/bin/crunz schedule:list

+---+---------------+-------------+--------------------+
| # | Task          | Expression  | Command to Run     |
+---+---------------+-------------+--------------------+
| 1 | Sample Task   | * * * * 1 * | command/to/execute |
+---+---------------+-------------+--------------------+ 
```

### 生成任务

还有一个有用的命令`make:task`，它用所有的默认值生成一个任务文件框架，所以你不必从头开始写。

例如，要创建一个在星期一每小时运行`/var/www/script.php`的任务，我们运行以下命令:

```
vendor/bin/crunz make:task exampleOne --run scripts.php --in /var/www --frequency everyHour --constraint mondays
Where do you want to save the file? (Press enter for the current directory) 
```

结果，事件被定义在指定任务目录内的`exampleOneTasks.php`中。

要查看事件是否已成功创建，我们可以列出这些事件:

```
crunz schedule:list

+---+------------------+-------------+----------------+
| # | Task             | Expression  | Command to Run |
+---+------------------+-------------+----------------+
| 1 | Task description | 0 * * * 1 * | scripts.php    |
+---+------------------+-------------+----------------+ 
```

为了查看`make:task`的所有默认选项，我们用`help`运行它:

```
vendor/bin/crunz make:task --help 
```

## 在基于 Web 的界面中使用 Crunz

为 Crunz 创建一个基于网络的界面非常容易。我们只需要将事件的属性放在一个数据库表中。然后，我们可以通过 API 获取所有记录。最后，我们迭代获取的记录，创建事件对象。无论事件是动态创建的还是静态创建的，只要它位于有效的任务文件中，Crunz 就会运行它。

如果你需要尝试一些现成的东西，并根据你的需求进行扩展，你可以使用这个用 Laravel 创建的 web 界面: [lavary/crunz-ui](http://github.com/lavary/crunz-ui) 。您可以阅读软件包的`README.md`文件中的安装说明。为了让用户实现他们自己的身份验证机制，这个基于 web 的界面没有提供任何现成的身份验证系统。

## 结论

通过在代码中包含我们的任务，我们可以将它们置于版本控制之下，因此其他开发人员可以添加或修改项目，而不必访问服务器。

这样，我们可以用我们需要的任何方式来控制任务，并且要一次性永久停止所有任务，我们只需卸载主 cron 作业。

如果您有任何问题或意见，请发表在下面，我们将尽最大努力及时回复您——并且不要犹豫，在这里您可以提出一些关于套餐本身的建议和意见！

## 分享这篇文章**