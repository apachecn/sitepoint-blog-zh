# 使用 Whenever 计划 Cron 作业

> 原文：<https://www.sitepoint.com/schedule-cron-jobs-whenever-gem/>

![Reloj despertador FO](img/6252bdece18db92e798ec2c16d7255cc.png)

有时，我们必须在特定的时间间隔运行作业，比如备份日志、发送电子邮件等。在 Unix 系统中，我们可以使用内置的`cron`守护进程来调度这样的任务。Cron 是一个用于执行预定任务的[守护进程](http://en.wikipedia.org/wiki/Daemon_%28computing%29)。Unix 系统提供了一个名为 **crontab** 的命令，允许个人用户创建预定任务。

Cron 在其 spool 区域(`/var/spool/cron/crontabs`)中搜索 crontab 文件，并将它们加载到内存中。不应该直接访问该目录中的文件，应该使用 crontab 命令来访问和更新它们。使用以下命令编辑您自己的 crontab 文件:

```
$ crontab -e
```

假设您想在每天凌晨 1:10 备份日志目录。将以下行添加到 crontab 文件中:

```
10 1 * * * *  /path/to/your/backup/script.sh
```

Cron 使用`10 1 * * * *`来计算执行作业的时间表。为了理解这种格式，下面是每个字段的映射:

```
* * * * * *
| | | | | |
| | | | | +-- Year              (range: 1900-3000)
| | | | +---- Day of the Week   (range: 1-7, 1 standing for Monday)
| | | +------ Month of the Year (range: 1-12)
| | +-------- Day of the Month  (range: 1-31)
| +---------- Hour              (range: 0-23, 1 in our example)
+------------ Minute            (range: 0-59, 10 in our example)
```

星号(`*`)告诉 cron 对该范围内的每个条目运行该命令。在我们的例子中，有星号表示一月中的某一天、一年中的某一月、一周中的某一天和一年。这意味着该命令将在一年中的每一个月的每一天、一年中的每一周的每一天运行。

然而，直接编辑 crontab 有点困难，更新 crontab 也很痛苦。如果你在一个 Ruby/Rails 项目中，无论何时 都有一个叫做 **[的奇妙宝石，它可以用来向你的 crontab 添加和调度任务。](https://github.com/javan/whenever)**

让我们举一个给用户发送每日摘要邮件的例子。在这种情况下，我们可能有这样一个邮件:

```
# file: app/mailers/user_mailer.rb

class UserMailer < ActionMailer::Base
  def digest_email_update(options)
    # ... email sending logic goes here
  end
end
```

因为我们希望将电子邮件作为一个单独的进程发送，所以让我们创建一个 Rake 任务来发送电子邮件。将名为 **email_tasks.rake** 的新文件添加到 Rails 应用程序的 **lib/tasks** 目录中:

```
# file: lib/tasks/email_tasks.rake

desc 'send digest email'
task send_digest_email: :environment do
  # ... set options if any
  UserMailer.digest_email_update(options).deliver!
end
```

`send_digest_email: :environment`意味着在运行任务之前加载 Rails 环境，因此您可以访问任务中的应用程序类(如`UserMailer`)。

现在，运行命令`rake -T`将列出新创建的 Rake 任务。通过运行任务并检查电子邮件是否发送，测试一切正常。

```
rake send_digest_email
```

此时，我们有一个工作 rake 任务，可以使用 **crontab** 进行调度。

## 无论何时宝石

通过将下面一行添加到**gem 文件**中来安装 whenever gem:

```
# file: Gemfile

gem 'whenever', require: false
```

现在，穿好衣服:

```
$ bundle
```

转到项目目录，运行`wheneverize`命令创建一个调度文件:

```
$ wheneverize .
```

这将在 Rails 应用程序的**配置**目录下创建一个 **schedule.rb** 文件。编辑 **schedule.rb** 文件来调度任务。假设我们需要每天晚上 10 点发送摘要电子邮件:

```
# file: config/schedule.rb

every :day, at: '10pm' do
  # specify the task name as a string
  rake 'send_digest_email'
end
```

运行`whenever`命令将会以实际的 cron 格式输出生成的时间表的预览:

```
$ whenever

0 22 * * * /bin/bash -l -c 'cd /home/deepak/Work/testapp && RAILS_ENV=production bundle exec rake send_digest_email --silent'

## [message] Above is your schedule file converted to cron syntax; your crontab file was not updated.
## [message] Run `whenever --help' for more options.
```

输出的第一行是每天晚上 10 点运行的实际命令。一旦这个命令被写入 crontab，它将由 cron 守护进程运行。

您可以验证创建的计划，然后使用以下命令更新(写入)您的 crontab:

```
$ whenever -w

[write] crontab file written
```

这意味着您的 crontab 已更新，您可以通过打开 crontab 文件来检查它:

```
$ crontab -e
```

在这里，您将被要求选择一个您自己选择的编辑器，然后您将被拖放到该编辑器。您可以看到当前登录用户的所有现有 cronjobs。

运行`whenever --help`查看各种可用选项:

```
$ whenever --help
```

其中一个有用的选项是-f，它允许你使用不同的计划文件

```
$ whenever -f /path/to/schedule/file/myschedule.rb
```

要清除 crontab，请运行以下命令:

```
$ whenever -c

[write] crontab file
```

该命令将清除您的 crontab 文件。当您出现错误或想要中止 cron 作业时，这很有用。

## 自定义作业类型

除了耙子任务之外,“无论何时”gem 还提供了另外三种工作类型:**指挥官**、**跑垒员**和**耙子**。您也可以创建自己的工作类型。在下面的例子中，我使用了所有这三种作业类型。

**runner** 作业类型允许您输入一段可执行代码作为字符串，以特定的时间间隔运行。类似地，**命令**接受一个间隔运行的脚本，而。正如您已经知道的， **rake** 运行您的应用程序中定义的 rake 任务。

```
every 3.hours do
  runner 'User.expire_session_cache'
  rake 'rake_task_name'
  command '/usr/bin/command_name'
end
```

假设您想要创建一个定制的 job_type。首先，需要定义新的作业类型，如下所示:

```
job_type :foo, '/usr/local/bin/foo :task :filename'
```

定义后，在 **schedule.rb** 中使用这个新的作业类型:

```
every 2.hours do
  foo 'somecommand', filename: 'inputfilepath'
end
```

现在，`foo`命令将使用`/usr/local/bin/foo inputfilepath`每 2 小时运行一次。

## 使用 Capistrano 实现自动化

每当 gem 提供 Capistrano 方法来自动更新部署期间的 crontab 文件更新。在部署文件中需要插件很简单:

```
# file: config/deploy.rb

require 'whenever/capistrano'

set :whenever_environment, defer { stage }
set :whenever_command, 'bundle exec whenever'
```

就是这样。现在部署时，Capistrano 将更新您的 crontab。

## 包扎

使用 whenever gem 的主要优点是，您不必记住繁琐的 crontab 语法或自己更新 crontab，这可能会对系统中现有的 crontab 作业造成危害。此外，无论何时都使用一个脚本文件(如 **schedule.rb** )，这只是普通的 Ruby，所以你可以利用 Ruby 的友好和可读的语法。用 Capistrano 任务更新部署环境的 crontab 文件也非常好。

总而言之，如果您有需要按计划运行的作业，那么使用 whenever gem 可以让您利用 Unix 的强大功能和 Ruby 的便利。

快乐编码。

## 分享这篇文章