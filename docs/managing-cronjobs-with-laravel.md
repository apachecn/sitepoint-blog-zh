# 如何在您的 Laravel 应用程序中设置 Cron 作业

> 原文：<https://www.sitepoint.com/managing-cronjobs-with-laravel/>

有时，您的应用程序需要在服务器上定期运行管理任务。无论您是想向用户发送电子邮件还是在一天结束时清理数据库表，您都需要一个任务调度机制来处理这些任务。

Cron 是类 unix 系统中的任务调度器，它以一定的时间间隔运行 shell 命令。这篇文章不是为了介绍 Cron，但是因为它是我们教程中的一个关键概念，我们将描述它是如何工作的基础。

![A clock](img/bc3de72c4130c0335f361a5b6fd8dbf0.png)

## Cron 基础

Cron 是一个任务调度守护进程，它以一定的时间间隔运行调度的任务。Cron 使用一个名为 crontab 的配置文件来管理调度过程。

Crontab 包含 cron 作业，每个作业都与特定的任务相关。Cron 作业由两部分组成，cron 表达式和要运行的 shell 命令:

```
* * * * * command/to/run
```

上述表达式(`* * * * *`)中的每个字段是用于设置调度频率的选项。它按照位置顺序由分钟、小时、月中的日、月和星期几组成。星号表示相应字段的所有可能值。因此，上述 cron 作业将在一天中的每一分钟运行。

每天在`12:30`执行以下 cron 作业:

```
30 12 * * * command/to/run
```

这只是 Cron 的冰山一角；要了解更多，你可能想访问维基百科的页面。

在 PHP 驱动的应用程序中，管理任务通常是在 CLI 模式下运行的独立 PHP 脚本。这些脚本是为在特定时间执行不同的工作而编写的。

然而，如果没有其他 PHP 库和框架的帮助，我们也无能为力。在本文中，您将学习如何使用 Laravel 框架为命令行创建健壮的 PHP 脚本，并从源代码中调度它们。

## 在 Laravel 中创建命令

在 PHP 中创建一个命令就像创建一个 PHP 脚本一样简单，然后在命令行中运行它，使用`php`命令:

```
php somefile.php
```

如您所见，该文件作为一个**参数**传递给命令`php`。

无论您的应用程序是用 Laravel 开发的，还是您只想使用它的外观和助手来创建您的脚本，您都需要在使用它之前引导 Laravel。然而，有一个更好的替代方法:创建一个 [Laravel Artisan](http://laravel.com/docs/5.1/artisan) 命令。

使用 Artisan 命令时，我们将可以访问 Laravel 的所有功能，包括助手、外观和其他 Artisan 命令，等等。

我们不打算在这里深入讨论 Artisan 命令的细节，因为它们超出了本教程的范围，但是让我们讨论一下基础知识，作为开始。我们将使用 Laravel 的最新版本，也就是在撰写本文时的版本。

我们使用`make:console` Artisan 命令来生成一个命令类框架。例如，我们将创建一个命令，在用户生日时向他们发送生日快乐短信。

```
$ php artisan make:console HappyBirthday --command=sms:birthday
```

上面的命令将在`app/Console/Commands`目录中的同名文件中创建一个名为`HappyBirthday`的类。我们还通过`command`选项为命令选择了一个名字。这是我们调用命令时将使用的名称。

现在让我们打开文件，看看目前为止我们有什么。该类中有几个构建命令后端的属性和方法:

```
<?php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class HappyBirthday extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'sms:birthday';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Command description.';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        //
    }
}
```

对于`$description`，我们可以简单描述一下该命令在执行时会做什么。

```
protected $description = 'Sends a Happy birthday message to users via SMS';
```

在 Laravel `5.1`中，每当在终端中执行一个命令时，就会调用 command 类的`handle`方法。这就是我们需要放置逻辑的地方。

在我们的例子中，要在用户生日时向他们发送生日消息，我们可以像这样修改`handle`方法:

```
<?php
// ...
public function handle()
{
  User::whereBirthDate(date('m/d'))->get(); 

  foreach( $users as $user ) {
    if($user->has('cellphone')) {
    SMS::to($user->cellphone)
       ->msg('Dear ' . $user->fname . ', I wish you a happy birthday!')
       ->send();
    }   
}  

$this->info('The happy birthday messages were sent successfully!');

}
```

命令完成后，我们需要向 Artisan 注册它，这样它就可以在终端中使用了。为此，我们只需要将命令的类名添加到位于`app/Console/Kernel.php`的`Kernel`类的`commands`数组中:

```
<?php
class Kernel extends ConsoleKernel
{
    /**
     * The Artisan commands provided by your application.
     *
     * @var array
     */
    protected $commands = [
        // ...
        'App\Console\Command\HappyBirthday'
    ];

    // ...
```

如果我们在终端中运行`php artisan list`,我们将看到我们命令的描述:

```
$ php artisan list

...
sms
  sms:birthday        Sends a Happy birthday to users via SMS
...
```

命名命令名是一个好习惯。这将有助于我们根据它们的用途对它们进行分类。

因此，每当我们在终端中运行该命令时，就会向匹配的用户组发送生日快乐消息:

```
$ php artisan sms:birthday

The happy birthday messages were sent successfully!
```

这只是一个假设的用例。更具体的例子，见 [Younes Rafie 关于缩小命令的帖子](https://www.sitepoint.com/create-laravel-css-minify-command/)。

## 调度命令

有趣的部分来了。让我们安排一个任务来运行我们刚刚构建的命令。为此，我们将使用一个您肯定会喜欢的特性:Laravel 任务调度器。

任务是在`Kernel`类的`schedule`方法中定义的。通过使用`command`方法，我们可以添加任意多的 Artisan 命令。

根据它的用法，我们的任务应该每天运行一次。所以我们可以像这样使用`daily()`方法:

```
<?php

// ...

protected function schedule(Schedule $schedule)
{   
        $schedule->command('sms:birthday')->daily();
}
```

我们可以通过`schedule`方法来调度所有的命令。正如文档所述，我们可以为任务分配多种调度频率。我们将在这里列出其中的一些，但是您可能希望阅读文档来查看完整的列表，并决定哪一个最适合您的环境。

每天每小时运行一项任务:

```
<?php
$schedule->command('myTask')
         ->hourly(); 
```

要在每天午夜运行任务:

```
<?php
$schedule->command('myTask')
         ->daily(); 
```

要在每天 9:30 运行任务:

```
<?php
$schedule->command('myTask')
         ->dailyAt('09:30'); 
```

要每周运行一项任务:

```
<?php
$schedule->command('myTask')
         ->weekly(); 
```

每月运行一次:

```
<?php
$schedule->command('myTask')
         ->monthly(); 
```

我们还可以使用定制的 cron 调度，就像普通的 cron 作业表达式一样:

```
$schedule->command('myTask')
         ->cron('* * * * *');
```

上述任务将每分钟运行一次。

要查看选项的完整列表，请参考文档的第[节，计划频率选项](http://laravel.com/docs/5.1/scheduling)。

Laravel 还提供了一组进度约束，可以与上述方法结合使用。例如，我们可以计划每周运行一次任务，但是将其限制在某一天和某一小时。

```
<?php
$schedule->command('theTask')
         ->weekly()
         ->mondays()
         ->at(12:30); 
```

或者

```
<?php
$schedule->command('theTask')
         ->weekly()
         ->sundays() 
```

我们可以更进一步，使用接受闭包的`when`方法，将任务的执行限制在某个条件下。如果闭包返回**真**，任务将只执行**。**

```
<?php
$schedule->command('table:clean')
          ->daily()
          ->when(function() {
             return SMS::isWorkingFine();
          });
```

### 启动 Laravel 调度程序

要启动调度程序本身，我们只需要在服务器上添加一个 cron 作业(使用`crontab -e`命令)，它在一天中的每分钟执行`php /path/to/artisan schedule:run` **:**

```
* * * * * php /path/to/artisan schedule:run 1>> /dev/null 2>&1
```

请注意，我们需要向 Laravel 安装的 Artisan 命令提供**完整路径**。

为了丢弃 cron 输出，我们将`/dev/null 2>&1`放在 cronjob 表达式的末尾。

要了解关于调度器的更多信息，请参见文档中的[调度](http://laravel.com/docs/master/scheduling)章节。

## 结论

在本教程中，我们使用 Laravel Artisan 命令来创建终端命令。我们没有在服务器上为每个任务添加许多 cron 作业条目，而是只创建了一个每分钟执行一次的**任务，并将责任委托给 Laravel 的任务调度器。**

通过使用定制的 Artisan 命令和 Laravel 的任务调度器，我们可以专注于创建命令，Laravel 会处理其余的事情。除此之外，其他团队成员也可以管理调度频率，因为它现在由版本控制系统跟踪。

如果您有任何问题或意见，请在下面发表，我们将尽最大努力及时回复。

## 分享这篇文章**