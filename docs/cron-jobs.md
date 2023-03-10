# 克隆工作:综合指南

> 原文：<https://www.sitepoint.com/cron-jobs/>

您是否需要定期运行脚本，但不想记得手动启动它？或者，您可能需要在特定的时间或间隔执行一个命令，但不希望该进程独占您的 CPU 或内存。无论是哪种情况，cron 作业都非常适合这项任务。让我们看看它们是什么，如何设置它们，以及您可以用它们做的一些事情。

在未来的特定时间，有时需要自动运行一组任务。这些任务通常是管理性的，但也可能是任何事情——从制作数据库备份到在所有人都睡着时下载电子邮件。

Cron 是类 Unix 操作系统中基于时间的作业调度器，它在未来触发某些任务。这个名字来源于希腊语 *χρόνος* (chronos)，意思是时间。

Cron 最常用的版本是 Vixie Cron。保罗·维谢最初是在 1987 年开发的。

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## Cron 工作术语

*   **工作:** 一个工作单位，做某事的一系列步骤。例如，向一组用户发送电子邮件。本文将使用 *任务* 、 *作业* 、 *cron 作业、*或*事件*互换使用。
*   **守护程序:**在后台运行的计算机程序，服务于不同的目的。守护进程通常在引导时启动。web 服务器是一个服务于 HTTP 请求的守护进程。Cron 是一个用于运行预定任务的守护进程。
*   Cron 作业:Cron 作业是一个预定的作业。守护进程在任务到期时运行任务。
*   Webcron :一个基于时间的作业调度器，运行在服务器环境中。它是标准 Cron 的替代方案，通常在不提供 shell 访问的共享 web 主机上使用。

## Cron 作业入门

如果我们看一下`/etc`目录，我们可以看到类似于`cron.hourly`、`cron.daily`、`cron.weekly`和`cron.monthly`的目录，每个目录对应于一定的执行频率。

安排任务的一种方法是将脚本放在适当的目录中。例如，为了每天运行`db_backup.php`，我们将它放在`cron.daily`中。如果给定频率的文件夹丢失，我们需要首先创建它。

***注意:**这种方法使用`run-parts`脚本，这个命令运行它在指定目录中找到的每个可执行文件。*

这是安排任务最简单的方法。但是，如果我们需要更多的灵活性，我们应该使用 Crontab。

## Crontab Files

Cron 使用称为`crontab`文件的特殊配置文件，其中包含要完成的任务列表。Crontab 代表 **Cron 表**。crontab 文件中的每一行都称为一个 cron 作业，它类似于一组由空格字符分隔的列。每一行都指定了**何时**和**多久** Cron 应该执行一次特定的命令或脚本。

在 crontab 文件中，空行或以`#`开头的行、空格或制表符将被忽略。以`#`开头的行被认为是注释。

crontab 中的活动行要么是环境变量的声明，要么是 cron 作业。Crontab 不允许在活动行上添加注释。

下面是一个只有一个条目的 crontab 文件的示例:

```
0 0 * * *  /var/www/sites/db_backup.sh 
```

第一部分`0 0 * * *`是 cron 表达式，它指定了执行的频率。上述 cron 作业将每天运行一次。

用户可以拥有自己的 crontab 文件，这些文件以他们在`/etc/passwd`文件中注册的用户名命名。所有用户级 crontab 文件都驻留在 Cron 的 spool 区域。你应该*而不是*直接编辑这些文件。相反，我们应该使用`crontab`命令行实用程序来编辑它们。

***注意:**spool 目录在不同的 Linux 发行版中有所不同。在 Ubuntu 上是`/var/spool/cron/crontabs`，而在 CentOS 上是`/var/spool/cron`。*

要编辑我们的**自己的** crontab 文件:

```
crontab -e 
```

上面的命令将自动打开属于我们用户的 crontab 文件。如果您以前没有为 crontab 选择默认编辑器，您将看到一个已安装编辑器的选择。我们还可以显式地选择或更改编辑 crontab 文件所需的编辑器:

```
export VISUAL=nano; crontab -e 
```

在我们保存文件并退出编辑器后，将检查 crontab 的准确性。如果一切设置正确，文件将保存到假脱机目录。

**注意:***crontab 文件中的每个命令都是从拥有 crontab 的用户的角度执行的。如果您的命令以 root (sudo)身份运行，您将无法从您自己的用户帐户定义这个 crontab，除非您以 root 身份登录。*

要列出属于我们自己用户的已安装 cron 作业:

```
crontab -l 
```

我们还可以将 cron 作业写入一个文件，并将其内容发送到 crontab 文件，如下所示:

```
crontab /path/to/the/file/containing/cronjobs.txt 
```

前面的命令将用`/path/to/the/file/containing/cronjobs.txt`覆盖现有的 crontab 文件。

要删除 crontab，我们使用`-r`选项:

```
crontab -r 
```

### Crontab 条目的剖析

用户级 crontab 条目的结构如下所示:

```
 # ┌───────────── min (0 - 59) 
 # │ ┌────────────── hour (0 - 23)
 # │ │ ┌─────────────── day of month (1 - 31)
 # │ │ │ ┌──────────────── month (1 - 12)
 # │ │ │ │ ┌───────────────── day of week (0 - 6) (0 to 6 are Sunday to Saturday, or use names; 7 is Sunday, the same as 0)
 # │ │ │ │ │
 # │ │ │ │ │
 # * * * * *  command to execute 
```

前两个字段指定任务运行的时间(**分钟**和**小时**)。接下来的两个字段指定每月的**日**和**月**。第五个字段指定了一周的第**天**。

当分钟、小时、月以及一个月中的**天**或一周中的**天**与当前时间匹配时，Cron 将执行该命令。

如果一周中的第**天**和一月中的第**天**都有特定的值，当**或**字段与当前时间匹配时，事件将运行。考虑下面的表达式:

```
0 0 5-20/5 Feb 2 /path/to/command 
```

前面的 cron 作业将每五天运行一次，从 2 月 5 日到 20 日**加上 2 月的所有星期二**。

***重要**:当**中的某一天**和**中的某一周的某一天**都有一定的值(非星号)时，会创建一个`OR`条件，表示这两天匹配。*

系统 crontabs ( `/etc/crontab`)中的语法与用户级 crontabs 略有不同。不同之处在于，第六个字段是**而不是**命令，但它是我们想要运行作业的**用户**。

```
* * * * * testuser /path/to/command 
```

不建议将系统范围的 cron 作业放在`/etc/crontab`中，因为该文件可能会在未来的系统更新中被修改。相反，我们将这些 cron 作业放在`/etc/cron.d`目录中。

### 编辑其他用户的 Crontab

我们可能需要编辑其他用户的 crontab 文件。为此，我们使用如下的`-u`选项:

```
crontab -u username -e 
```

***注意**我们只能以 root 用户，或者具有管理权限的用户的身份编辑其他用户的 crontab 文件。*

有些任务需要超级管理员权限。您应该将它们添加到根用户的 crontab 文件中:

```
sudo crontab -e 
```

***注意:**请注意，将`sudo`与`crontab -e`一起使用会编辑 root 用户的 crontab 文件。如果我们在使用`sudo`时需要编辑另一个用户的 crontab，我们应该使用`-u`选项来指定 crontab 的所有者。*

要了解更多关于`crontab`命令的信息:

```
man crontab 
```

### 标准和非标准 Crontab 值

Crontab 字段接受数字作为值。然而，我们也可以在这些字段中放置其他数据结构。

#### 范围

我们可以传入一系列数字:

```
0 6-18 1-15 * * /path/to/command 
```

上述 cron 作业将在一年中每个月的 1 号到 15 号从早上 6 点到下午 6 点运行。请注意，指定的范围是包含在内的，因此 1-5 表示 1、2、3、4、5。

#### 列表

列表是一组逗号分隔的值。我们可以将列表作为字段值:

```
0 1,4,5,7 * * * /path/to/command 
```

上面的语法将在每天凌晨 1 点、4 点、5 点和 7 点运行 cron 作业。

#### 步伐

步骤可与范围或星号字符`(*)`一起使用。当它们与范围一起使用时，它们指定在范围结束时**跳过**的值的数量。它们在范围后用一个`/`字符定义，后跟一个数字。考虑以下语法:

```
0 6-18/2 * * * /path/to/command 
```

上面的 cron 作业将从早上 6 点到下午 6 点每两个小时运行一次。

当步骤与星号一起使用时，它们只是指定了特定字段的频率。举例来说，如果我们将分钟设置为`*/5`，这仅仅意味着每五分钟一次**。**

我们可以将列表、范围和步骤组合在一起，以便更灵活地安排事件:

```
0 0-10/5,14,15,18-23/3 1 1 * /path/to/command 
```

上述活动将于一月一日午夜至上午十时、下午二时、下午三时，每五小时举行一次，下午六时至十一时亦每三小时举行一次。

#### 名称

对于**月份**和**星期几**字段，我们可以使用特定日期或月份的前三个字母，如`Sat`、`sun`、`Feb`、`Sep`等。

```
* * * Feb,mar sat,sun /path/to/command 
```

前面的 cron 作业将只在二月和三月的星期六和星期天运行。

请注意，名称**不**区分大小写。使用名称时不允许范围。

#### 预定义的定义

一些 cron 实现可能支持一些特殊的字符串。这些字符串用于代替前五个字段，每个字段指定一个特定的频率:

*   **@yearly，@annually** 每年 1 月 1 日午夜运行一次`(0 0 1 1 *)`
*   **@monthly** 每月运行一次，在每月第一天的午夜`(0 0 1 * *)`
*   **@weekly** 每周周日午夜运行一次`(0 0 * * 0)`
*   **@每日**每天午夜运行一次`(0 0 * * *)`
*   **@每小时**每小时开始运行`(0 * * * *)`
*   **@reboot** 启动时运行一次

### 同一 Cron 作业中的多个命令

我们可以在同一个 cron 作业中运行几个命令，用分号(`;`)分隔它们。

```
* * * * * /path/to/command-1; /path/to/command-2 
```

如果运行的命令相互依赖，我们可以在它们之间使用双&符号`(&&)`。因此，如果第一个命令失败，第二个命令将不会运行。

```
* * * * * /path/to/command-1 && /path/to/command-2 
```

### 环境变量

crontab 文件中的环境变量采用`VARIABLE_NAME = VALUE`的形式(等号两边的空格是可选的)。Cron 不从用户的主目录获取任何启动文件(当它运行用户级 cron 时)。这意味着我们应该手动设置任务所需的任何特定于用户的设置。

Cron 守护进程在启动时会自动设置一些环境变量。`HOME`和`LOGNAME`是从`/etc/passwd`中的 crontab 所有者信息中设置的。但是，如果需要的话，我们可以在 crontab 文件中覆盖这些值。

还有一些类似于`SHELL`的变量，指定运行命令的 shell。默认是`/bin/sh`。我们也可以设置`PATH`来查找节目。

```
PATH = /usr/bin;/usr/local/bin 
```

**重要提示** : *当值中有空格时，我们应该用引号将值括起来。请注意，值是普通的字符串。它们不会以任何方式被**而不是**解释或解析。*

### 不同的时区

Cron 在评估 crontab 条目时使用系统的时区设置。这可能会给用户位于不同时区的多用户系统带来问题。为了解决这个问题，我们可以在 crontab 文件中添加一个名为`CRON_TZ`的环境变量。因此，所有 crontab 条目都将基于指定的时区进行解析。

## Cron 如何解释 Crontab 文件

Cron 启动后，它会搜索其假脱机区域，以找到 crontab 文件并将其加载到内存中。它还检查系统 crontabs 的`/etc/crontab`和/或`/etc/cron.d`目录。

在将 crontabs 加载到内存中之后，Cron 会每分钟检查一次加载的 crontabs，运行到期的事件。

除此之外，Cron 定期(每分钟)检查假脱机目录的`modtime`(修改时间)是否已经改变。如果是，它检查所有已加载的 crontabss 的`modetime`,并重新加载那些已更改的 crontab。这就是为什么我们在安装一个新的 cron 作业时不需要重启守护进程。

## Cron 权限

我们可以指定哪个用户可以使用 Cron，哪个用户不可以。对于 cron 权限，有两个文件起着重要的作用:`/etc/cron.allow`和`/etc/cron.deny`。

如果`/etc/cron.allow`存在，那么我们的用户名必须在这个文件中列出，以便使用`crontab`。如果`/etc/cron.deny`存在，它不应该包含我们的用户名。如果这两个文件都不存在，那么根据站点相关的配置参数，**超级用户**或**所有用户**将能够使用`crontab`命令。例如，在 Ubuntu 中，如果两个文件都不存在，默认情况下所有用户都可以使用 crontab。

我们可以将`ALL`放在`/etc/cron.deny`文件中，防止所有用户使用 cron:

```
echo ALL > /etc/cron.deny 
```

***注意**:如果我们创建一个`/etc/cron.allow`文件，那么就不需要创建一个`/etc/cron.deny`文件，因为它和创建一个`/etc/cron.deny`文件有着相同的效果。*

## 重定向输出

如果命令(或脚本)有任何输出，我们可以将 cron 作业的输出重定向到一个文件:

```
* * * * * /path/to/php /path/to/the/command >> /var/log/cron.log 
```

我们可以将标准输出重定向到 dev null 以不接收电子邮件，但仍然发送标准错误电子邮件:

```
* * * * * /path/to/php /path/to/the/command > /dev/null 
```

为了防止 Cron 向我们发送任何电子邮件，我们将相应的 crontab 条目更改如下:

```
* * * * * /path/to/php /path/to/the/command > /dev/null 2>&1 
```

这意味着“将标准输出和错误输出都遗忘”

## 通过电子邮件发送输出

输出被邮寄给 crontab 的所有者或在`MAILTO`环境变量中指定的电子邮件(如果标准输出或标准错误没有如上重定向)。

如果`MAILTO`设置为空，则 cron 作业不会发送任何电子邮件。

我们可以设置多个电子邮件，用逗号分隔它们:

```
MAILTO=admin@example.com,dev@example.com
* * * * * /path/to/command 
```

## Cron 和 PHP

我们通常使用 PHP 可执行文件运行我们的 PHP 命令行脚本。

```
php script.php 
```

或者，我们可以在脚本的开头使用 [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) ，并指向 PHP 可执行文件:

```
#! /usr/bin/php

<?php

// PHP code here 
```

因此，我们可以通过按名称调用文件来执行它。然而，我们需要确保我们有权限执行它。

为了拥有更健壮的 PHP 命令行脚本，我们可以使用第三方组件来创建控制台应用程序，如 [Symfony 控制台组件](http://symfony.com/doc/current/components/console/introduction.html)或[Laravel](https://laravel.com/docs/5.1/artisan)Artisan。[本文](https://www.sitepoint.com/re-introducing-symfony-console-cli-php-uninitiated/)是使用 Symfony 控制台组件的良好开端。

了解更多关于[使用 Laravel Artisan](https://www.sitepoint.com/managing-cronjobs-with-laravel/) 创建控制台命令的信息。如果你更愿意使用 PHP 的另一个命令行工具，我们这里有一个比较。

## 任务重叠

有时，计划任务比预期时间长得多。这将导致重叠，意味着一些任务可能会同时运行。在某些情况下，这可能不会造成问题，但是当他们修改数据库中的相同数据时，我们就会遇到问题。我们可以通过增加任务的执行频率来克服这个问题。尽管如此，也不能保证这些重叠不会再次发生。

我们有几个选项来防止 cron 作业重叠。

### 使用植绒

Flock 是一个很好的工具，可以在 shell 脚本或命令行中管理锁文件。这些锁文件对于了解脚本是否正在运行非常有用。

当与 cron 结合使用时，如果锁文件存在，相应的 Cron 作业不会启动。您可以使用`apt-get`或`yum`安装 Flock，这取决于 Linux 发行版。

```
apt-get install flock 
```

或者:

```
yum install flock 
```

考虑以下 crontab 条目:

```
* * * * * /usr/bin/flock --timeout=1 /path/to/cron.lock /usr/bin/php /path/to/scripts.php 
```

在前面的示例中，`flock`查找`/path/to/cron.lock`。如果在一秒钟内获得锁，它将运行脚本。否则，它将失败，退出代码为 1。

### 在脚本中使用锁定机制

如果 cron 作业执行一个脚本，我们可以在脚本中实现一个锁定机制。考虑下面的 PHP 脚本:

```
<?php
$lockfile = sys_get_temp_dir() . '/' md5(__FILE__) . '.lock';
$pid      = file_exists($lockfile) ? trim(file_get_contents($lockfile)) : null;

if (is_null($pid) || posix_getsid($pid) === false) {

    // Do something here

    // And then create/update the lock file
    file_put_contents($lockfile, getmypid());

} else {
    exit('Another instance of the script is already running.');
} 
```

在前面的代码中，我们将当前 PHP 进程的`pid`保存在一个文件中，该文件位于系统的`temp`目录中。每个 PHP 脚本都有自己的锁文件，它是脚本文件名的 MD5 散列。

首先，我们检查锁文件是否存在，然后获取它的内容，这是脚本最后一个运行实例的进程 ID。然后我们将`pid`传递给 [posix_getsid](http://php.net/manual/en/function.posix-getsid.php) PHP 函数，该函数返回进程的会话 id。如果`posix_getsid`返回`false`，这意味着进程不再运行，我们可以安全地启动一个新实例。

## 阿纳克朗

Cron 的一个问题是它假设系统是连续运行的(一天 24 小时)。这给不是全天运行的机器(如个人电脑)带来了问题。如果系统在预定的任务时间内离线，Cron 将不会追溯运行该任务。

Anacron 不是 cron 的替代品，但是它解决了这个问题。它每天、每周或每月运行一次命令，但不像 Cron 那样每分钟或每小时运行一次。但是，这保证了即使系统意外关闭一段时间，任务也会运行。

只有 root 用户或具有管理权限的用户才能管理 Anacron 任务。Anacron 不像守护进程那样在后台运行，而是只运行一次，执行到期的任务。

Anacron 使用一个名为`anacrontabs`的配置文件(就像 crontab 一样)。这个文件位于`/etc`目录中。

该文件的内容如下所示:

```
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1       5       cron.daily              nice run-parts /etc/cron.daily
7       25      cron.weekly             nice run-parts /etc/cron.weekly
@monthly 45     cron.monthly            nice run-parts /etc/cron.monthly 
```

在一个`anacrontab`文件中，我们只能设置周期为`n`天的频率，后面是以分钟为单位的延迟时间。这个延迟时间只是为了确保任务不会同时运行。

第三列是惟一的名称，它标识 Anacron 日志文件中的任务。

第四列是要运行的实际命令。

考虑以下条目:

```
1       5       cron.daily              nice run-parts /etc/cron.daily 
```

这些任务每天运行，在 Anacron 运行五分钟后运行。它使用`run-parts`来执行`/etc/cron.daily`内的所有脚本。

上面列表中的第二个条目每 7 天(每周)运行一次，延迟 25 分钟。

### Cron 和 Anacron 的冲突

您可能已经注意到，Cron 也被设置为执行`/etc/cron.*`目录中的脚本。不同风格的 Linux 处理这种可能与 Anacron 冲突的方式不同。在 Ubuntu 中，Cron 检查 Anacron 是否存在于系统中，如果存在，它将不会执行`/etc/cron.*`目录中的脚本。

在其他版本的 Linux 中，Cron 在运行任务时会更新 Anacron 时间戳。如果 cron 已经运行了它们，Anacron 不会执行它们。

## 快速故障排除

### 命令的绝对路径

在 crontab 文件中使用所有可执行文件的绝对路径是一个好习惯。

```
* * * * * /usr/local/bin/php /absolute/path/to/the/command 
```

### 确保 Cron 守护程序正在运行

如果我们的任务根本没有运行，首先我们需要确保 Cron 守护进程正在运行:

```
ps aux | grep crond 
```

输出应该如下所示:

```
root      7481  0.0  0.0 116860  1180 ?        Ss    2015   0:49 crond 
```

### 检查`/etc/cron.allow`和`/etc/cron.deny`文件

当 cron 作业没有运行时，我们需要检查`/etc/cron.allow`是否存在。如果是这样，我们需要确保在这个文件中列出我们的用户名。如果`/etc/cron.deny`存在，我们需要确保我们的用户名不在这个文件中。

如果我们编辑一个用户的 crontab 文件，而该用户在`/etc/cron.allow`文件中不存在，那么在我们重新编辑 crontab 文件之前，包含在`/etc/cron.allow`中的用户不会运行 cron。

### 执行权限

我们需要确保 crontab 的所有者拥有 crontab 文件中所有命令和脚本的执行权限。否则，cron 将不起作用。您可以通过以下方式向任何文件夹或文件添加执行权限:

```
chmod +x /some/file.php
```

### 新行字符

crontab 中的每个条目都应该以新的一行结束。这意味着在最后一个 crontab 条目之后必须有一个空行，否则最后一个 cron 作业将永远不会运行。

## 包扎

Cron 是一个守护进程，运行一系列计划在未来发生的事件。我们在称为 crontab 文件的特殊配置文件中定义这些作业。基于`/etc/cron.allow`或`/etc/cron.deny files`，如果允许用户使用 Cron，他们可以拥有自己的 crontab 文件。除了用户级 cron 作业之外，cron 还加载语法略有不同的系统级 Cron 作业。

我们的任务通常是 PHP 脚本或命令行实用程序。在不是一直运行的系统中，我们可以使用 Anacron 来运行在`n`天内发生的事件。

在使用 Cron 时，我们还应该意识到任务之间的重叠，以防止数据丢失。cron 作业完成后，输出将被发送给 crontab 的所有者和/或在`MAILTO`环境变量中指定的电子邮件。

你从这篇文章中学到什么新东西了吗？我们错过了什么吗？或者你只是喜欢这篇文章，想告诉我们它是多么的全面？请在下面的评论中告诉我们！

## 分享这篇文章