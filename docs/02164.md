# Phpseclib:通过 PHP 安全地与远程服务器通信

> 原文：<https://www.sitepoint.com/phpseclib-securely-communicating-with-remote-servers-via-php/>

PHP 有一个 [SSH2](http://php.net/manual/en/book.ssh2.php) 库，它使用安全的加密传输提供对远程机器上的资源(shell、远程执行、隧道、文件传输)的访问。客观地说，对于开发人员来说，实现它是一项乏味且非常令人沮丧的任务，因为它有大量的配置选项和复杂的 API，而且几乎没有文档。

![Connection between client and server](img/01490f54e3994f03387eefacbfd67199.png)

这个[PHP sclib](http://phpseclib.sourceforge.net/ssh/intro.html)(**PHP****S**secure**C**communication S**Lib**rary)包有一个开发者友好的 API。如果可以的话，它使用一些可选的 PHP 扩展，否则就依靠内部 PHP 实现。要使用这个包，你不需要安装任何非默认的 PHP 扩展。

## 装置

```
composer require phpseclib/phpseclib 
```

这将通过 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 安装库的最新稳定版本。

## 使用案例

在盲目深入之前，我想列出一些适合使用这个库的用例:

1.  在远程服务器上执行部署脚本
2.  通过 SFTP 下载和上传文件
3.  在应用程序中动态生成 SSH 密钥
4.  显示在服务器上执行的远程命令的实时输出
5.  测试 SSH 或 SFTP 连接

## 连接到远程服务器

使用 [phpseclib](http://phpseclib.sourceforge.net/ssh/intro.html) ，您可以使用以下任何一种认证方法连接到您的远程服务器:

1.  RSA 密钥
2.  密码保护的 RSA 密钥
3.  用户名和密码( ***不推荐*** )

### RSA 密钥

我们将假设您已经生成了一个安全的 **RSA** 密钥。如果你不熟悉生成一个安全的 RSA 密钥对，你可以浏览一下[这篇文章](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)。关于视频解释，你可以参考[为黑客从服务器上创建和使用 SSH 密钥](https://serversforhackers.com/video/creating-and-using-ssh-keys)。

使用 **RSA** 密钥认证登录到远程服务器:

```
namespace App;

use phpseclib\Crypt\RSA;
use phpseclib\Net\SSH2;

$key = new RSA();
$key->loadKey(file_get_contents('privatekey'));

//Remote server's ip address or hostname
$ssh = new SSH2('192.168.0.1');

if (!$ssh->login('username', $key)) {
    exit('Login Failed');
} 
```

### 密码保护的 RSA 密钥

如果您的 RSA 密钥有密码保护，请不要担心。Phpseclib 负责这个特殊的用例:

```
namespace App;

use phpseclib\Crypt\RSA;
use phpseclib\Net\SSH2;

$key = new RSA();
$key->setPassword('your-secure-password');
$key->loadKey(file_get_contents('privatekey'));

//Remote server's ip address or hostname
$ssh = new SSH2('192.168.0.1');

if (!$ssh->login('username', $key)) {
    exit('Login Failed');
} 
```

### 用户名和密码

或者，使用用户名和密码登录到您的远程服务器(我们不推荐这种做法):

```
namespace App;

use phpseclib\Net\SSH2;

//Remote server's ip address or hostname
$ssh = new SSH2('192.168.0.1');

if (!$ssh->login('username', 'password')) {
    exit('Login Failed');
} 
```

对于其他选项，如无身份验证或多因素身份验证，请[参考文档](http://phpseclib.sourceforge.net/ssh/auth.html#rsakey)

## 在远程服务器上执行命令

在远程服务器上执行命令的代码非常简单。您用要执行的命令作为参数来调用`$ssh->exec($cmd)`方法。

```
namespace App;

use phpseclib\Crypt\RSA;
use phpseclib\Net\SSH2;

$key = new RSA();
$key->loadKey(file_get_contents('privatekey'));

//Remote server's ip address or hostname
$ssh = new SSH2('192.168.0.1');

if (!$ssh->login('username', $key)) {
    exit('Login Failed');
}

$ssh->exec('ls -la'); 
```

### 在远程服务器上执行多个命令

在实际应用中，我们很少执行一个命令。我们经常需要使用`cd`遍历服务器并执行许多其他命令。如果您尝试在远程服务器上执行多个命令，如下所示，它不会给出您想要的输出:

```
$ssh->exec('pwd'); //outputs /home/username

$ssh->exec('cd mysite.com');

$ssh->exec('pwd'); //outputs /home/username 
```

上面的原因是对`exec`方法的调用不会将状态转移到下一个`exec`调用。要执行多个命令而不丢失状态:

```
$ssh->exec('cd /home/username; ls -la'); //Lists all files at /home/username 
```

您可以使用分号或新行字符`PHP_EOL`添加任意数量的命令。

例如，如果您想为 Laravel 运行一个完整的部署脚本:

```
$ssh->exec(
      "git pull origin master" . PHP_EOL
        . "composer install --no-interaction --no-dev --prefer-dist" . PHP_EOL
        . "composer dump-autoload -o" . PHP_EOL
        . "php artisan optimize" . PHP_EOL
        . "php artisan migrate --force"
); 
```

### 出现第一个错误时退出

在上面的脚本中，整个命令集作为一个 shell 脚本执行。每个命令都将被执行，即使其中一些命令失败，就像在常规的 shell 脚本中一样。默认情况下，这很好，但是如果我们需要在第一个错误时退出，我们必须修改 bash 脚本。这不是特定于 [phpseclib](http://phpseclib.sourceforge.net/ssh/intro.html) 的东西，它与 bash 脚本相关。

如果您在脚本的开头放置了一个`set -e`选项，那么只要链中的任何命令返回一个非零值，脚本就会终止。

例如，上述部署脚本的修改版本应该是

```
$ssh->exec(
    "set -e" . PHP_EOL
        . "git pull origin master" . PHP_EOL 
        . "composer install --no-interaction --no-dev --prefer-dist" . PHP_EOL
        . "composer dump-autoload -o" . PHP_EOL
        . "php artisan optimize" . PHP_EOL
        . "php artisan migrate --force"
); 
```

如果任何命令导致错误，上述脚本将终止。

## 收集输出

`exec`方法返回远程脚本的输出:

```
$output = $ssh->exec('ls -la');
echo $output; 
```

但是，有时它不会返回全部输出。您可以通过将闭包作为第二个参数传递给`exec`方法来克服这个问题，以确保任何未捕获的输出也将被返回。

```
$ssh->exec(
    $deployment_script, function ($str) {
        $output .= $str;
});

echo $output; 
```

*注意:错误输出(如果有的话)也将由`exec`方法或底层闭包返回。*

### 显示实时输出

如果您想通过控制台命令执行脚本并显示实时输出，可以通过在底层闭包中回显输出来实现。

```
$ssh->exec($deployment_script, function ($str) {
    echo $str;
}); 
```

如果想在 web 浏览器中显示，需要用`ob_flush()`刷新(发送)输出缓冲区。

```
$ssh->exec(
    $deployment_script, function ($str) {
        echo $str;
        flush();
        ob_flush();
}); 
```

## 其他配置选项

还可以设置许多其他可用的配置选项。你可以称他们为`$ssh->{option}`。

比如:`$ssh->setTimeout(100)`。

下表列出了我们尚未涉及的所有选项:

| [计]选项 | 用例 |
| --- | --- |
| `setTimeout($seconds)` | `$ssh->exec('ping 127.0.0.1');`在 Linux 主机上永远不会返回，并将无限期运行。`setTimeout()`使它超时。将`$timeout`设置为假或 0 意味着没有超时。 |
| `write($cmd)` | 在交互式外壳中输入命令。 |
| `read()` | 返回交互式 shell 的输出 |
| `isTimeout()` | 如果最后一个`$ssh->read()`或`$ssh->exec()`的结果是由于超时造成的，则返回 true。否则将返回 false。 |
| `isConnected()` | 如果连接仍处于活动状态，则返回 true |
| `enableQuietMode()` | 取消 stderr，这样就不会返回任何错误 |
| `disableQuietMode()` | 在输出中包含 stderr |
| `isQuiteModeEnabled()` | 如果启用了安静模式，则返回 true |
| `enablePTY()` | 使用`exec()`时启用请求-pty |
| `disablePty()` | 使用`exec()`时禁用请求-pty |
| `isPTYEnabled()` | 如果请求 pty 已启用，则返回 true |
| `getLog()` | 返回已发送和接收的数据包的日志。 |
| `getServerPublicHostKey()` | 返回服务器公共主机密钥。如果服务器签名没有用公共主机密钥正确签名，则返回 false。 |
| `getExitStatus()` | 返回 SSH 命令的退出状态或 false。 |
| `getLanguagesClient2Server()` | 当从客户端接收数据时，返回服务器支持的语言列表。 |
| `getLanguagesServer2Client()` | 向客户端发送数据时，返回服务器支持的语言列表。 |
| `getCompressionAlgorithmsServer2Client()` | 向客户端发送数据时，返回服务器支持的压缩算法列表。 |
| `getCompressionAlgorithmsClient2Server()` | 当从客户端接收数据时，返回服务器支持的压缩算法列表。 |
| `getMACAlgorithmsServer2Client()` | 向客户端发送数据时，返回服务器支持的 MAC 算法列表。 |
| `getMACAlgorithmsClient2Server()` | 当从客户端接收数据时，返回服务器支持的 MAC 算法列表。 |
| `getEncryptionAlgorithmsServer2Client()` | 向客户端发送数据时，返回服务器支持的(对称密钥)加密算法列表。 |
| `getEncryptionAlgorithmsClient2Server()` | 当从客户端接收数据时，返回服务器支持的(对称密钥)加密算法列表。 |
| `getServerHostKeyAlgorithms()` | 返回服务器支持的主机密钥(公钥)算法列表。 |
| `getKexAlgorithms()` | 返回服务器支持的密钥交换算法列表。 |

## 可供选择的事物

1.  lib SSH 2–SSH 库–该库提供了类似的功能，但使用起来不太直观，并且它要求您在服务器上安装 [libssh2](https://pecl.php.net/package/ssh2) ，而大多数共享主机都没有。
2.  Process component-Symfony 的组件，用于编写您自己的脚本来连接和执行命令-就像在普通终端中一样。这限制了我们可能需要设置的配置选项。实现上述配置方法为我们提供的相同功能需要深入的 bash 知识。然而，如果您的用例只涉及运行一个本地脚本，这可能会被证明是一个有用的组件。

## 摘要

在本文中，我们介绍了 [phpseclib](http://phpseclib.sourceforge.net/ssh/intro.html) ，这是一个为 [SSH2](http://php.net/manual/en/book.ssh2.php) 提供替代方案的包。我们已经介绍了入门所需的配置选项，上表应该可以帮助您填补空白，并概述了其他可用的配置选项。

关于基于密钥的通信的深入实现，请参见我们的[之前的教程](https://www.sitepoint.com/encrypt-large-messages-asymmetric-keys-phpseclib/)。

你如何执行远程命令？您能想到这个库的任何高级用例吗？它们是什么？请在评论中告诉我们！

## 分享这篇文章