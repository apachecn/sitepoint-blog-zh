# 在 PHP 中使用 SSH 和 SFTP

> 原文：<https://www.sitepoint.com/using-ssh-and-sftp-with-php/>

当今世界有如此多的第三方集成和内容共享，理解和利用 SCP 和 SFTP 等协议非常重要。PHP 的 SSH2 扩展是实现 ssh2 协议的 libssh2 的包装器，它提供了几个可以用来安全传输文件的函数。

要开始利用这些功能，显然需要安装 SSH2 包。由于它是 PECL 扩展，安装过程将取决于您选择的操作系统。遵循 php.net 的[指导方针。](http://php.net/manual/en/install.pecl.php "PHP: Installation of PECL Extensions - Manual")

## 建立连接

让我们从连接到 SSH 服务开始。建立连接非常简单:

```
<?php
$conn = ssh2_connect('example.com', 22);
ssh2_auth_password($conn, 'username', 'password');
```

一些管理员更喜欢使用公钥和私钥来验证登录。如果服务已配置，并且您希望以这种方式连接，您可以使用以下方式:

```
<?php
$conn = ssh2_connect('example.com', 22);
ssh2_auth_pubkey_file(
    $conn,
    'username',
    '/home/username/.ssh/id_rsa.pub',
    '/home/username/.ssh/id_rsa'
);
```

无论您使用用户名/密码还是公钥/私钥认证，`ssh2_auth_password()`和`ssh2_auth_pubkey_file()`都返回一个布尔值，指示认证是否成功。

## 执行基本命令

一旦您成功通过服务器的身份验证，您就可以执行文件传输操作。SCP 功能允许您发送或接收文件，如下所示:

```
<?php
// send a file
ssh2_scp_send($conn, '/local/filename', '/remote/filename', 0644);

// fetch file
ssh2_scp_recv($conn, '/remote/filename', '/local/filename');
```

`ssh2_scp_send()`有一个额外的参数，当文件被复制时，你可以指定在远程服务器上的文件权限。

SFTP 函数提供了更多的功能；您可以更改文件或目录权限、获取文件信息、创建目录、重命名项目、删除项目等。它们的工作方式与上述 SCP 功能非常相似，但在使用这些功能之前，必须通过`ssh2_sftp()`进行额外的连接:

```
<?php
$sftp = ssh2_sftp($conn);

// Create a new folder
ssh2_sftp_mkdir($sftp, '/home/username/newdir');

// Rename the folder
ssh2_sftp_rename($sftp, '/home/username/newdir', '/home/username/newnamedir');

// Remove the new folder
ssh2_sftp_rmdir($sftp, '/home/username/newnamedir');

// Create a symbolic link
ssh2_sftp_symlink($sftp, '/home/username/myfile', '/var/www/myfile');

// Remove a file
ssh2_sftp_unlink($sftp, '/home/username/myfile');
```

`ssh2_sftp()`接受连接资源并返回一个 SFTP 资源，该资源在将来的`ssh2_sftp_` *调用中使用。然后调用返回一个布尔值，让您可以确定操作是否成功。

## 使用包装函数

当 SFTP 或 SCP 不存在特定的文件管理功能时，通常核心文件系统功能将使用流包装器工作。以下是几个例子:

```
<?php
// Create a new folder
mkdir('ssh2.sftp://' . $sftp . '/home/username/newdir');

// Remove the new folder
rmdir('ssh2.sftp://' . $sftp . '/home/username/newdir');

// Retrieve a list of files
$files = scandir('ssh2.sftp://' . $sftp . '/home/username');
```

在执行这些调用之前，必须连接到 SSH 和 SFTP 服务器，因为它使用之前创建的`$sftp`变量。

## 把所有的东西放在一起

现在您已经能够在 SSH 服务器上连接、验证和运行命令，我们可以创建几个助手类来简化执行这些命令的过程:一个用于执行 SCP 调用，一个用于 SFTP 调用，一个用于公共功能的父类，以及两个用于封装验证信息(密码和密钥)的类。

让我们首先创建身份验证类，因为它们将用于其他类。

```
<?php
class SSH2Authentication
{
}
```

```
<?php
class SSH2Password extends SSH2Authentication
{
    protected $username;
    protected $password;

    public function __construct($username, $password) {
        $this->username = $username;
        $this->password = $password;
    }

    public function getUsername() {
        return $this->username;
    }

    public function getPassword() {
        return $this->password;
    }
}
```

```
<?
class SSH2Key extends SSH2Authentication
{
    protected $username;
    protected $publicKey;
    protected $privateKey;

    public function __construct($username, $publicKey, $privateKey) {
        $this->username = $username;
        $this->password = $password;
    }

    public function getUsername() {
        return $this->username;
    }

    public function getPublicKey() {
        return $this->publicKey;
    }

    public function getPrivateKey() {
        return $this->privateKey;
    }
}
```

`SSH2Password`和`SSH2Key`只是简单的包装了各自的认证信息。它们共享一个公共基类，所以当我们向它们的消费者传递实例时，我们可以利用 PHP 的类型提示。

接下来，让我们创建一个`SSH2`来连接和认证 SSH 服务器。

```
<?php
class SSH2
{
    protected $conn;

    public function __construct($host, SSH2Authentication $auth, $port = 22) {
        $this->conn = ssh2_connect($host, $port);
        switch(get_class($auth)) {
            case 'SSH2Password':
                $username = $auth->getUsername();
                $password = $auth->getPassword();
                if (ssh2_auth_password($this->conn, $username, $password) === false) {
                    throw new Exception('SSH2 login is invalid');
                }
                break;
            case 'SSH2Key':
                $username = $auth->getUsername();
                $publicKey = $auth->getPublicKey();
                $privateKey = $auth->getPrivateKey();
                if (ssh2_auth_pubkey_file($this->conn, $username, $publicKey, $privateKey) === false) {
                    throw new Exception('SSH2 login is invalid');
                }
                break;
            default:
                throw new Exception('Unknown SSH2 login type');
        }
    }
}
```

将创建一个非常简单的 SCP 类，它扩展了`SSH2`，并将利用神奇的方法`__call()`。这允许我们做两件重要的事情:自动在函数调用前添加“ssh_scp_ ”,并为调用提供连接变量。

```
<?php
class SSH2SCP extends SSH2
{
    public function __call($func, $args) {
        $func = 'ssh2_scp_' . $func;
        if (function_exists($func)) {
            array_unshift($args, $this->conn);
            return call_user_func_array($func, $args);
        }
        else {
            throw new Exception(
                $func . ' is not a valid SCP function');
        }
    }
}
```

SFTP 类非常相似，尽管它的构造函数被重载来执行`ssh2_sftp()`函数。结果存储在一个受保护的变量中，并自动添加到所有 SFTP 函数调用的前面。

```
<?php
class SSH2SFTP extends SSH2 {
    protected $sftp;

    public function __construct($host, ISSH2Authentication $auth, $port = 22) {
        parent::__construct($host, $auth, $port);
        $this->sftp = ssh2_ftp($this->conn);
    }
    public function __call($func, $args) {
        $func = 'ssh2_sftp_' . $func;
        if (function_exists($func)) {
            array_unshift($args, $this->sftp);
            return call_user_func_array($func, $args);
        }
        else {
            throw new Exception(
                $func . ' is not a valid SFTP function');
        }
    }
}
```

一旦创建了这些类，就可以用它们来执行 SCP 和 SFTP 函数调用。感谢这两个类中有用的`__call`方法，我们不需要传递打开的连接或者在每次调用时重复键入“ssh2_scp_”或“ssh2_ftp_”。

```
<?php
// Create SCP connection using a username and password
$scp = new SCP(
    'example.com',
    new SSH2Password('username', 'password')
);
// Receive a file via SCP
if ($scp->recv('remote/file', 'local/file')) {
    echo 'Successfully received file';
}

// Create SFTP connection using a public/private key
$sftp = new SSH2SFTP(
    'example.com',
    new SSH2Key('username', 'public_key', 'private_key')
);
// Create a directory via SFTP
if ($sftp->mkdir('directory/name')) {
    echo 'Successfully created directory';
}
```

## 摘要

安装 SSH2 PHP 扩展，它为您的脚本提供了连接到 SSH2 服务器的能力。您可以利用方便的类来简化执行 SFTP 或 SCP 调用的代码，或者如果库没有提供特定的功能，则可以通过利用 SSH2 包装器功能来使用大多数核心文件系统操作。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章