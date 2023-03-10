# 用命令行 PHP 维护你的服务器

> 原文：<https://www.sitepoint.com/maintaining-your-server-with-command-line-php/>

在信息技术领域，我们越来越多地被要求在日常工作中扮演各种角色。我们中的许多人不仅被赋予了创建和维护网站的任务，还被赋予了维护服务器的任务。通常在维护服务器时，你需要知道如何编写脚本和小程序来自动完成任务。许多脚本都是用 Perl、Bash 或其他脚本语言编写的。现在我一点也不反对学习新的编程语言，但是您知道吗，您不仅可以使用 PHP 编写优秀的 web 应用程序，还可以从命令行使用 PHP。您可以使用您已经了解并喜欢的 PHP 来保持服务器的良好运行，这一切都可以从命令行完成！

在本文中，我们将看看使用 PHP CLI 的优势。我将向您展示如何在您的服务器上测试 PHP 的命令行界面/解释器(CLI ),然后我们将了解 PHP CLI 的一些可用选项，包括交互式 shell 以及如何创建可执行脚本。最后，我将给出几个用 PHP 编写的用于维护服务器的脚本示例。

使用 PHP CLI 而不是其他脚本语言的最大优势之一是能够在脚本中重用以前项目中的代码。无论是你创建的一个数据库类或函数，还是一个文件解析程序，使用 PHP CLI 你都不需要重写任何代码。

您还可以使用 CRON 自动化许多用 PHP CLI 编写的任务。如果你以前从未使用过 CRON，它是一个守护程序，在特定的时间在你的服务器上运行特定的脚本。需要在周一、周三和周五为销售部门生成报告？编写一个 PHP 脚本来完成它，将它投入到 CRON 作业中，当 PHP 和 CRON 为您工作时，您可以高枕无忧。

## 运行 PHP CLI 脚本

如果您运行的是最新版本的 PHP，那么您的系统上可能已经安装了 PHP CLI。如果本文中的例子对您不起作用，那么您可能需要使用`--enable-cli`选项重新编译 PHP，或者重新安装包含该选项的 PHP 包。测试是否一切正常的最简单的方法是打开一个新的终端窗口或 SSH 会话到您的服务器或计算机，并从命令行输入`php -v`。

```
Stephens-Laptop:~ sthorpe$ php -v
PHP 5.3.8 (cli) (built: Dec  5 2011 21:24:09)
Copyright (c) 1997-2011 The PHP Group
Zend Engine v2.3.0, Copyright (c) 1998-2011 Zend Technologies
```

让我们尝试一个简单的例子，我们将从命令行运行一个实际的 PHP 文件。使用您喜欢的编辑器，用下面的代码创建一个名为`test.php`的文件:

```
<?php
$str = "SitePoint is the best!";
echo $str . "n";
```

要执行这个脚本，你所要做的就是输入`php test.php`，你将在命令行上看到输出。

```
Stephens-Laptop:~ sthorpe$ php test.php
SitePoint is the best!
```

在命令行上运行 PHP 脚本的一个更好的方法是改变脚本的权限模式，将`#!/usr/bin/php`放在文件的顶部，在开始的`<?php`标记之前(根据系统的配置，PHP 的路径可能会有所不同)。为此，您将在基于 UNIX 的系统上使用`chmod`命令。

```
#!/usr/bin/php
<?php
$filepath = exec("pwd");
echo "You are in the $filepath directory.n";
```

```
Stephens-Laptop:~ sthorpe$ chmod +x pwd.php
Stephens-Laptop:~ sthorpe$ ./pwd.php
You are in the ~ directory.
```

## CLI 选项和交互式 Shell

我之前已经向您展示了 PHP 的命令行选项之一，`-v`,它给出了您系统上安装的 PHP 的版本号，但是还有更多选项可用。例如，如果您想将自己编写的脚本代码作为网页与他人共享，并突出显示其语法以获得更好的可读性，您可以使用`-s`选项。PHP 将输出适当的 HTML 代码。

```
Stephens-Laptop:~ sthorpe$ php -s test.php < test.html
```

当在 web 浏览器中查看时，产生的`test.html`显示突出显示的代码:

![](img/17911ff25091b308d663c54d07d8e1c9.png "cli-php-thorpe01")

另一个有用的选项是`-l`来检查脚本中的语法错误。如果在文件中没有发现错误，PHP 将报告没有语法错误。否则 PHP 将报告它发现的错误以及错误发生在哪一行。

```
Stephens-Laptop:~ sthorpe$ php -l broken.php
PHP Parse error:  syntax error, unexpected T_VARIABLE in broken.php on line 3
Errors parsing broken.php
```

除了众多可用选项之外，使用 PHP CLI，您可以直接从 shell 中测试 PHP 代码中的想法，而无需创建任何文件！当您想要查看函数的结果时，这很有用。要运行 PHP 的交互式 shell，请使用`-a`。PHP 会回报“交互 shell”，提示变为`php >`。从现在开始，你只需要输入你的代码，PHP 就会立即执行它。要退出 shell，可以按 CTRL + D 或者调用`exit`。

```
Stephens-Laptop:~ sthorpe$ php -a
Interactive shell

php > echo 2 + 2;
4
php > exit;
```

## 获取输入

使用 CLI 编程，您有两种输入功能选项。第一种选择是直接从命令行向脚本传递参数。第二个选项是从运行脚本的用户的键盘上读取输入(标准输入)。

为了在脚本中接受参数，您将使用预定义的超全局变量`$_SERVER["argc"]`和`$_SERVER["arvc"]`。创建一个名为`arguments.php`的文件，包含以下代码:

```
#!/usr/bin/php
<?php
echo "There are " . $_SERVER["argc"] . " arguments:n";
foreach($_SERVER["argv"] $arv) {
   echo "t" . $arv . "n";
}
```

`$_SERVER["argc"]`包含传递给脚本的参数个数，`$_SERVER["argv"]`是一个数组，包含这些参数的所有值。因为文件名本身被认为是第一个参数，所以您总是至少有一个参数。

使文件可执行，并传入几个参数，每个参数之间留一个空格。该脚本将显示参数的总数，并列出每个参数。

```
Stephens-Laptop:~ sthorpe$ php arguments.php SitePoint Rocks
There are 3 arguments
   arguments.php
   SitePoint
   Rocks
```

接受来自标准输入的输入允许您创建交互式脚本，该脚本可以提示特定的信息。用以下代码创建一个名为`keyboard.php`的文件:

```
#!/usr/bin/php
<?php
echo "Hi There! what is your favorite web site?n";
$site = fread(STDIN, 80);
$site = trim($site);

if ($site != "SitePoint") {
   echo "Are you sure that one is your favorite?n";
}
else {
   echo "I knew it! Me too!n";
}
```

在这个例子中，我们使用了`fread()`函数，就像我们读取文件一样，但是在这个例子中，句柄是 PHP 预定义的常量`STDIN`。再次使该文件可执行并运行该文件；希望你的输出和我的一样。

```
Stephens-Laptop:~ sthorpe$ php keyboard.php
Hi There! what is your favorite web site?
SitePoint
I knew it! Me too!
```

## 一些 PHP CLI 示例

在监控服务器时，有一件事值得关注，那就是您的可用磁盘空间。现在的硬盘是如此之大，以至于你不会想到有一种方法可以填满所有的空间，但是相信我…它会在你最意想不到的时候发生。也许您的日志轮转过程中断了而您没有意识到，或者您的临时目录变得不受控制。这里有一个例子，您可以用它来监视服务器的磁盘空间:

```
#!/usr/bin/php
<?php
// config options
$disk = "/dev/disk0s2";
$threshold = 90;
$emailAddr = "you@example.com";
$emailName = "Your Name Here";

exec("df -h " . $disk, $output);
foreach($output as $line){
    $info = strstr($line, $disk);
    if ($info != "") {
        break;
    }
}
$pos = stripos($info, "%");
$pos = $pos - 3;
$used = substr($info, $pos, 3);

if ($used >= $threshold) {
    mail($emailAddr, "System HD Notification", "Main disk is at " . $used . "%" , "From: $emailName");
}
```

函数`exec()`在命令行中执行一个命令，并接受两个参数:第一个是实际的命令(在本例中是显示磁盘空间信息的`df`)，第二个是填充了命令输出的数组引用。`$disk`变量保存您想要监控的磁盘的路径(在我的例子中是`/dev/disk0s2`)。`df`命令输出各种其他数据，而不仅仅是使用的磁盘空间，所以我们将只从`$output`数组中寻找特定的信息。

代码继续遍历数组，并使用`strstr()`函数来查找所需的磁盘，一旦找到就退出循环。我使用`substr()`去掉字符串中包含剩余磁盘空间百分比的部分，并将其与预定值进行比较，在这种情况下，如果硬盘使用率达到 90%或更高。如果是这样，脚本会发送一封电子邮件提醒我这个信息。

您可以让这个快速脚本每 30 分钟左右作为 cron 作业运行一次，这会让您安心一些。

维护服务器时的另一个常见任务是确保您有文件和文件夹的持续备份。如果出于某种原因备份服务的某种版本控制不可用，那么下一个脚本可以用来备份一些文件夹和一个数据库，并将它们发送到一个异地 SFTP 服务器。

```
#!/usr/bin/php
<?php
// remote SFTP connection credentials
$sftpServerIP = "10.0.0.1";
$sftpUsername  = "sftpuser";
$sftpPassword = "hushhush";
$sftpTarget   = "/home/sftpuser";

// mail notification config
$emailAddr = "you@example.com";
$emailName = "Your Name Here";

// MySQL database connection credentials
$mysqlUsername = "dbuser";
$mysqlPassword = "secret";
$mysqlDatabase = "myDatabase";

// list of directories and files to back up
$files = array(
    "/home/username/important-file.txt",
    "/home/username/special-folder",
    "/var/spool/another-folder");

// create temporary directory and copy files/directories to it
$tmpFolder = "/tmp/" . uniqid();
mkdir($tmpFolder, 0700);
foreach ($files as $f) {
    exec("cp -r $f $tmpFolder/");
}

// dump the database content
exec("mysqldump -u $mysqlUsername -p$mysqlPassword $mysqlDatabase > $tmpFolder/backup.sql");

// compress the backup
exec("tar -czf $tmpFolder.tgz $tmpFolder/*");

// establish the sftp connection
$session = ssh2_connect($sftpServerIP, 22);
if ($session === false) {
    mail($emailAddr, "System Backup", "Could not connect to SFTP server", "From: $emailName");
    exit;
}
$result = ssh2_auth_password($session, $sftpUsername, $sftpPassword);
if ($result === false) {
    mail($emailAddr, "System Backup", "Could not authenticate to SFTP server", "From: $emailName");
    exit;
}
$sftp = ssh2_sftp($session);
if ($sftp === false) {
    mail($emailAddr, "System Backup", "Could not initialize SFTP subsystem", "From: $emailName");
    exit;
}

// transfer the backup file
$date = date("D");
$upload = file_put_contents(
    "ssh2.sftp://" . $sftp . $sftpTargetDir . "/backup-$date.tgz", 
    file_get_contents("$tmpFolder.tgz"));

if ($upload) {
    mail($emailAddr, "System Backup", "Your files has been backed up successfully", "From: $emailName");
}
else {
    mail($emailAddr, "System Backup", "Something went wrong with the upload of the backup", "From: $emailName");
}

// clean up the local temp backups
exec("rm -r $tmpFolder $tmpFolder.tgz");
```

我们再次使用`exec()`函数来执行 PHP 脚本中的其他程序和命令，例如`mysqldump`来转储数据库内容，以及`tar`来将备份文件夹压缩到归档文件中。我们之前定义的变量。连接到 SFTP 服务器使用 PHP 的 [SSH2 扩展](http://www.php.net/manual/en/book.ssh2.php)，所以你需要安装这个扩展。凯文·范·松内维尔德的博客中有很好的评论。如果您想使用像 [phpseclib](http://phpseclib.sourceforge.net/) 这样的纯解决方案，或者甚至是像 FTP 这样更老(也不安全)的协议，修改代码应该不会太难。无论您采用何种方法，该示例都强调了用 PHP 编写此类任务的一些好处。

## 摘要

在本文中，我们探索了从命令行使用 PHP 编写服务器维护脚本。我谈到了使用 PHP CLI 的优势，包括自动化脚本的能力。我们讨论了一些有用的选项，比如测试脚本语法错误的能力和使用交互模式。我们还了解了不同的可用输入功能，包括直接从键盘接受输入。然后，我给出了两个真实世界的脚本示例，用于维护您自己的服务器，其中包括一个硬盘监控脚本和一个备份脚本。

我希望本文为您提供了一些想法和知识，这些想法和知识将有助于您将来承担维护该服务器的任务，并且我希望您也可以使用这些 CLI 示例脚本作为基础来创建自己的维护脚本。

图片 via [程远扬](http://www.shutterstock.com/gallery-676222p1.html)/[Shutterstock](http://www.shutterstock.com/)

## 分享这篇文章