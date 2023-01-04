# 如何使用 Git 通过 SFTP 正确部署 Web 应用

> 原文：<https://www.sitepoint.com/how-to-properly-deploy-web-apps-via-sftp-with-git/>

*这篇文章由[海达·külekci̇](https://github.com/hkulekci)和[沃恩·安切塔](https://www.sitepoint.com/author/wancheta/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

上传文件是任何部署过程中不可或缺的一个方面，底层实现可能会因服务器的类型而异。

使用像 [Filezilla](https://filezilla-project.org/) 这样的开源桌面客户端，你可以很容易地将你的文件上传到 SFTP 服务器。使用过这种方法的人都知道，这种方法既麻烦又烦人，因为它不允许我们自动化部署过程，而且我们总是需要上传整个项目，即使我们只修改了代码库文件的一部分。

![Abstract image symbolizing upload - a cloud shape made from glowing teal pixels, and in front of it, or inside it, an arrow pointing upwards](img/f86af9278ecef924dd8e67e2ed23dd95.png)

[PHP sclib](https://www.sitepoint.com/phpseclib-securely-communicating-with-remote-servers-via-php/)(**PHP**Secure**C**communication S**Lib**rary)包有一个很棒的 API 用于日常的 SFTP 任务:它使用一些可选的 PHP 扩展(如果它们可用的话),否则就依靠内部 PHP 实现。你不需要任何额外的 PHP 扩展来使用这个包，用 PHP 打包的默认扩展就可以了。在本文中，我们将首先介绍[PHPSECLIB–SFTP](http://phpseclib.sourceforge.net/sftp/intro.html)的各种特性，包括但不限于上传或删除文件。然后，我们将看看如何结合使用 Git 和这个库来自动化我们的 SFTP 部署过程。

## PhpSecLib 安装

```
composer require phpseclib/phpseclib 
```

这将通过 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 安装库的最新稳定版本。

## 证明

默认情况下，密码验证用于连接到您的 SFTP 服务器。加密密钥对更加安全，因为私钥取代了密码，而密码通常更难以[暴力破解](https://www.sitepoint.com/watch-prevent-brute-force-attacks-on-a-login-page/)。使用 [phpseclib](http://phpseclib.sourceforge.net/sftp/intro.html) ，您可以使用以下任一认证方法连接到您的 SFTP 服务器:

1.  RSA 密钥
2.  密码保护的 RSA 密钥
3.  用户名和密码( ***不推荐*** )

### RSA 密钥

我们将假设您已经生成了一个安全的 **RSA** 密钥。如果你不熟悉生成一个安全的 RSA 密钥对，你可以浏览一下[这篇文章](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)。关于视频解释，你可以参考[为黑客从服务器上创建和使用 SSH 密钥](https://serversforhackers.com/video/creating-and-using-ssh-keys)。

使用 **RSA** 密钥认证登录到远程服务器:

```
namespace App;

use phpseclib\Crypt\RSA;
use phpseclib\Net\SFTP;

$key = new RSA();
$key->loadKey(file_get_contents('privatekey'));

//Remote server's ip address or hostname
$sftp = new SFTP('192.168.0.1');

if (!$sftp->login('username', $key)) {
    exit('Login Failed');
} 
```

### 密码保护的 RSA 密钥

如果您的 RSA 密钥有密码保护，请不要担心。PHPSECLIB 负责这个特殊的用例:

```
namespace App;

use phpseclib\Crypt\RSA;
use phpseclib\Net\SFTP;

$key = new RSA();
$key->setPassword('your-secure-password');
$key->loadKey(file_get_contents('privatekey'));

//Remote server's ip address or hostname
$sftp = new SFTP('192.168.0.1');

if (!$sftp->login('username', $key)) {
    exit('Login Failed');
} 
```

### 用户名和密码

或者，使用用户名和密码登录到您的远程服务器(我们不推荐这种做法):

```
namespace App;

use phpseclib\Net\SFTP;

//Remote server's ip address or hostname
$sftp = new SFTP('192.168.0.1');

if (!$sftp->login('username', 'password')) {
    exit('Login Failed');
} 
```

对于其他选项，如无认证或多因素认证，请[参考文档](http://phpseclib.sourceforge.net/ssh/auth.html#rsakey)。

## 上传和删除文件

部署过程的很大一部分包括将文件上传到服务器。上传文件实质上意味着将本地文件的内容传输到远程文件。下面的例子在服务器上创建了一个内容为**的`index.php`文件，这是一个虚拟文件**:

```
namespace App;

use phpseclib\Crypt\RSA;
use phpseclib\Net\SFTP;

$key = new RSA();
$key->loadKey(file_get_contents('privatekey'));

//Remote server's ip address or hostname
$sftp = new SFTP('192.168.0.1');

if (!$sftp->login('username', $key)) {
    exit('Login Failed');
}

$sftp->put('index.php', 'This is a dummy file'); 
```

默认情况下，`put`不读取本地文件系统。内容被直接转储到远程文件中。在现实世界中，您需要获取本地文件的内容，并将它们转储到远程文件中:

```
$contents = file_get_content('path/to/local/file');
$sftp->put('index.php', $contents); 
```

在我们的部署过程中，我们需要增加一个删除应用程序不再需要的文件和目录的步骤。您可以轻松地删除单个文件或递归删除特定目录中的所有文件和目录。

```
//Deleting a single file. This does not delete directories.
$sftp->delete('index.php');

//Deleting directories and files recursively
$sftp->delete('dir_name', true); 
```

## 使用 Git 自动化部署

Git 被广泛接受为版本控制工具的行业标准。使用 Git 的强大功能，我们可以通过只上传那些自上次上传以来发生更改的文件来节省一些时间和带宽。使用 PHPSECLIB 的 SFTP API，我们将尝试将我们的文件部署到我们的 SFTP 服务器上。

为了更好地解释每个步骤，我将整个部署过程分成了以下几个步骤。一旦我们经历了所有的步骤，我们将把它全部集中在一起。

### 一些 Git 基础知识

首先，我们需要理解一些特定的 Git 命令，这些命令将帮助我们完成部署过程。我们将创建一个帮助我们执行 Git 相关任务的类。Git 命令是从命令行执行的——我们将为此使用 [Process](http://symfony.com/doc/current/components/process.html) 组件。该组件为执行 shell 命令提供了一个抽象层，并处理不同平台之间的细微差异。你可以参考它的[文档](http://symfony.com/doc/current/components/process.html)了解更多信息。现在，我们将编写特定于获取在两次提交之间添加、修改或删除的文件的代码。

```
<?php

namespace App;

use Symfony\Component\Process\Process;
use Symfony\Component\Process\Exception\ProcessFailedException;

class Git 
{
    public function diffFiles($start_commit, $end_commit)
        {
        //Get new and modified files
        $process = new Process("git diff --name-only --diff-filter=AM $start_commit $end_commit");

        $process->setTimeout(3600);
        $process->setWorkingDirectory('/path/to/local/repository');
        $process->run();

        if (!$process->isSuccessful()) {
            throw new ProcessFailedException($process);
        }

    //Extract all file paths from the process output
        $files["added"] = array_unique(
        array_merge($files["added"], 
        array_filter(explode("\n", $process->getOutput()), 'strlen'))
    );

    //Get deleted files
        $process = new Process("git diff-tree --diff-filter=D --name-only -t $start_commit $end_commit");

        $process->setTimeout(3600);
        $process->setWorkingDirectory('/path/to/local/repository');
        $process->run();

        if (!$process->isSuccessful()) {
            throw new ProcessFailedException($process);
        }

        $files["deleted"] = array_filter(explode("\n", $process->getOutput()), 'strlen');

        return $files;
    }
} 
```

### 在特定提交时获取文件的内容

Git 将关于给定存储库的历史数据存储在隐藏的`.git`文件夹中。为了继续我们的部署过程，我们需要在指定的提交中获取 Git 记录的文件内容。我们将向`Git`类添加另一个方法:

```
public function getContent($commit, $file)
{
    //Get contents of a file at a specific commit
    $process = new Process("git show \"$commit:$file\"");

    $process->setTimeout(3600);
    $process->setWorkingDirectory('path/to/repository');
    $process->run();

    if (!$process->isSuccessful()) {
        throw new ProcessFailedException($process);
    }

    return $process->getOutput();
} 
```

### 部署文件

接下来，我们需要编写一段代码来执行部署过程。让我们首先列出需要注意的重要步骤:

1.  在两次提交之间获取添加、修改和删除的文件
2.  将新的和修改过的文件传输到 SFTP 服务器
3.  删除在两次提交之间删除的文件

既然我们已经清楚了我们需要实现的目标，那么让我们将它写入代码:

```
$files = $git->diffFiles($start_commit, $end_commit);

if(count($files['added']) > 0) {
    foreach($files['added'] as $file) {
        $content = $git->getContent($end_commit, $file);

        //Ensure a directory exists - if it doesn't create it recursively.
        if (!$sftp->file_exists(dirname($file))) {
            $sftp->mkdir(dirname($path), -1, true);
        }

        $sftp->put('/path/to/remote/file', $content);
    }
}

if(count($files['deleted']) > 0) {
    foreach($files['deleted'] as $file) {

        if ($sftp->file_exists($file)) {
            $sftp->delete($file, true);
        }

    }
} 
```

上述代码将传输所有更新的文件，并删除在两次指定提交之间删除的文件。它还负责自动创建目录。如果这些目录不存在，上面的代码将确保在这个过程中创建这些目录。

## 执行远程命令

在使用`exec`方法完成部署之前或之后，您可以在远程服务器上执行任何 shell 命令。

```
$sftp->exec("your-remote-command-here"); 
```

就像使用 [SSH](https://www.sitepoint.com/phpseclib-securely-communicating-with-remote-servers-via-php/) 一样，对`exec`方法的调用不会将状态转移到下一个`exec`调用。要执行多个命令而不丢失状态:

```
$sftp->exec(
    "php artisan up" . PHP_EOL
    "composer dump-autoload -o" . PHP_EOL
    "php artisan optimize"
); 
```

`phpseclib\Net\SFTP`类扩展了`phpseclib\Net\SSH`类。您可以利用`SSH`类的完整 API 来执行远程命令或收集输出。关于`SSH`的深入实现，请看我们的[之前的教程](https://www.sitepoint.com/phpseclib-securely-communicating-with-remote-servers-via-php/)。

## 管理权限

权限是文件的基本防御系统。我们需要为用户或用户组分配一些对服务器上文件的特定权限，以防止潜在的攻击。

对单个文件设置权限，如果成功，将返回新的文件权限；如果失败，将返回 false:

```
$sftp->chmod(0777, 'path/to/remote/file'); 
```

或者，您可以递归地设置目录中所有文件的权限:

```
$sftp->chmod(0777, 'path/to/remote/file', true); 
```

下面是关于`SFTP`类中文件权限的最重要方法的总结:

| 方法 | 用例 |
| --- | --- |
| `chgrp($filename, $gid, $recursive = false)` | 更改文件或目录组。 |
| `chown($filename, $uid, $recursive = false)` | 更改文件或目录所有者。 |
| `truncate($filename, $new_size)` | 将文件截断到给定长度。 |
| `touch($filename, $time = null, $atime = null)` | 设置文件的访问和修改时间。如果该文件不存在，将会创建它。 |

## 下载文件

使用 [PHPSECLIB](http://phpseclib.sourceforge.net/sftp/intro.html) ，我们还可以从服务器下载备份文件或用户上传的媒体:

```
//Gets the remote file's contents
$content = $sftp->get('path/to/remote/file');

//Downloads and saves to the local file
$sftp->get('path/to/remote/file', 'path/to/local/file'); 
```

如果本地没有该文件，将会自动为您创建。

## 可供选择的事物

1.  git-deploy-php-一个简单的基于 PHP 的工具，它将你修改过的文件部署到你的 SFTP 服务器上。对于指定的提交，它会自动为您传输文件。
2.  另一个基于 PHP 的部署工具，用于将文件传输到 SFTP 服务器。它有更多的特性，比如回滚、对多个服务器和子模块的支持等等。缺点是触发部署需要过多的手动配置。
3.  [Deploy——Tantra](https://deploytantra.com)—一旦你推到指定的分支，自动将你的库部署到 SFTP 服务器。好的一面是一个更好管理的工作流程。缺点是它是商业化的。

## 结论

在本文中，我们介绍了一种帮助自动化 SFTP 服务器部署过程的特殊方法，并介绍了开始部署所需的配置选项。

您如何部署您的 SFTP 服务器？你能想到一些高级的部署方式吗？它们是什么？请在评论中告诉我们！

## 分享这篇文章