# 用 Flysystem 抽象文件系统

> 原文：<https://www.sitepoint.com/abstract-file-systems-flysystem/>

读写文件是任何编程语言不可或缺的一部分，但是底层的实现可能会有很大的不同。例如，与通过 FTP 上传相比，将数据写入本地文件系统的细节非常不同——但从概念上讲，这是非常相似的。

除了 FTP 这样的老掉牙的工具，在线存储也越来越普遍和便宜——有几十种服务可用，如 Dropbox、亚马逊的 S3 和 Rackspace 的云文件——但这些都使用略有不同的读写方法。

这就是[飞行系统](https://github.com/thephpleague/flysystem)的用武之地。它在多个文件系统上提供了一个抽象层，这意味着您不需要担心文件*在哪里*，它们是如何存储的，或者需要关心低级 I/O 操作。你所需要担心的只是高级操作，比如读、写和组织到目录中。

这种抽象也使得从一个系统切换到另一个系统变得更加简单，而不必重写大量的应用程序代码。它还提供了将数据从一个存储系统移动或复制到另一个存储系统的逻辑方法，而无需担心底层的实现。

你可以像在本地一样使用 Dropbox、S3、云文件、FTP 或者 FTP 无论是在本地保存还是通过网络传输，保存文件的过程都是一样的。您可以将 zip 存档视为一堆文件夹，而不必担心创建和压缩存档本身的细节。

## 安装和基本使用

一如既往，Composer 是安装的最佳方式:

```
"league/flysystem": "0.2.*" 
```

现在，通过传入适当的**适配器**，您可以简单地创建一个或多个`League\Flysystem\Filesystem`实例。

例如，要使用本地目录:

```
use League\Flysystem\Filesystem;
use League\Flysystem\Adapter\Local as Adapter;

$filesystem = new Filesystem(new Adapter('/path/to/directory')); 
```

要使用亚马逊 S3 桶，需要稍微多一点的配置:

```
use Aws\S3\S3Client;
use League\Flysystem\Adapter\AwsS3 as Adapter;

$client = S3Client::factory(array(
    'key'    => '[your key]',
    'secret' => '[your secret]',
));

$filesystem = new Filesystem(new Adapter($client, 'bucket-name', 'optional-prefix')); 
```

要使用 Dropbox:

```
use Dropbox\Client;
use League\Flysystem\Adapter\Dropbox as Adapter;

$client = new Client($token, $appName);
$filesystem = new Filesystem(new Adapter($client, 'optional/path/prefix')); 
```

(要获得令牌和应用程序名称，使用 [Dropbox 的应用程序控制台](https://www.dropbox.com/developers/apps)创建一个应用程序。)

这里有一个 SFTP 的例子，您可能不需要这里列出的所有选项:

```
use League\Flysystem\Adapter\Sftp as Adapter;

$filesystem = new Filesystem(new Adapter(array(
    'host' => 'example.com',
    'port' => 21,
'username' => 'username',
'password' => 'password',
'privateKey' => 'path/to/or/contents/of/privatekey',
'root' => '/path/to/root',
'timeout' => 10,
))); 
```

对于普通 FTP、Predis 或 WebDAV 等其他适配器，[请参考文档](https://github.com/thephpleague/flysystem)。

## 读取和写入文件系统

就您的应用程序代码而言，您只需将诸如`file_exists()`、`fopen()` / `fclose()`、`fread` / `fwrite`和`mkdir()`之类的调用替换为它们的 flysystem 等价物。

例如，以下面的遗留代码为例，它将本地文件复制到 S3 存储桶:

```
 $filename = "/usr/local/something.txt";
    if (file_exists($filename)) {
        $handle = fopen($filename, "r");
        $contents = fread($handle, filesize($filename));
        fclose($handle);

        $aws = Aws::factory('/path/to/config.php');
        $s3 = $aws->get('s3');

        $s3->putObject(array(
            'Bucket' => 'my-bucket',
            'Key'    => 'data.txt',
            'Body'   => $content,
            'ACL'    => 'public-read',
        )); 
    }
```

使用 flysystem，它可能看起来有点像这样:

```
 $local = new Filesystem(new Adapter('/usr/local/'));
    $remote = new Filesystem(
        S3Client::factory(array(
            'key'    => '[your key]',
            'secret' => '[your secret]',
        )),
        'my-bucket'
    );

    if ($local->has('something.txt')) {
        $contents = $local->read('something.txt');
        $remote->write('data.txt', $contents, ['visibility' : 'public']);
    }
```

请注意我们是如何使用术语的，比如*读*和*写*、*本地*和*远程*——高级抽象，不用担心创建和销毁文件句柄之类的事情。

下面是对`League\Flysystem\Filesystem`类中最重要的方法的总结:

| 方法 | 例子 |
| --- | --- |
| 阅读 | `$filesystem->read('filename.txt')` |
| 写作 | `$filesystem->write('filename.txt', $contents)` |
| 更新 | `$filesystem->update('filename.txt')` |
| 编写或更新 | `$filesystem->put('filename.txt')` |
| 检查存在 | `$filesystem->has('filename.txt')` |
| 删除 | `$filesystem->delete('filename.txt')` |
| 重新命名 | `$filesystem->rename('old.txt', 'new.txt')` |
| 读取文件 | `$filesystem->read('filename.txt')` |
| 获取文件信息 | `$filesystem->getMimetype('filename.txt')` |
|  | `$filesystem->getSize('filename.txt')` |
|  | `$filesystem->getTimestamp('filename.txt')` |
| 创建目录 | `$filesystem->createDir('path/to/directory')` |
| 删除目录 | `$filesystem->deleteDir('path/to/directory')` |

## 自动创建目录

当你调用`$filesystem->write()`时，它确保你试图写入的目录存在——如果不存在，它递归地为你创建。

所以这个…

```
$filesystem->write('/path/to/a/directory/somewhere/somefile.txt', $contents); 
```

…基本上相当于:

```
$path = '/path/to/a/directory/somewhere/somefile.txt';
if (!file_exists(dirname($path))) {
    mkdir(dirname($path), 0755, true);
}
file_put_contents($path, $contents); 
```

## 管理可见性

可见性(即权限)在不同的存储机制中可能在实现或语义上有所不同，但 flysystem 将它们抽象为“私有”或“公共”。因此，您不需要担心`chmod`、S3 ACL 或某个特定机制碰巧使用的任何术语的细节。

您可以在调用`write()`时设置可见性:

```
$filesystem->write('filename.txt', $data, ['visibility' : 'private']); 
```

5.4 之前，或根据偏好:

```
$filesystem->write('filename.txt', $data, array('visibility' => 'private')); 
```

或者，您可以使用`setVisibility`设置现有对象的可见性:

```
$filesystem->setVisibility('filename.txt', 'private'); 
```

您可以在给定实例的构造函数中为其设置默认可见性，而不是逐个文件地设置它:

```
$filesystem = new League\Flysystem\Filesystem($adapter, $cache, [
'visibility' => AdapterInterface::VISIBILITY_PRIVATE
]); 
```

您还可以使用`getVisibility`来确定文件的权限:

```
if ($filesystem->getVisibility === 'private') {
    // file is secret
} 
```

## 列出文件和目录

您可以获得给定目录中所有文件和目录的列表，如下所示:

```
$filesystem->listContents(); 
```

输出看起来有点像这样；

```
[0] =>
  array(8) {
  'dirname' =>
    string(0) ""
    'basename' =>
    string(11) "filters.php"
    'extension' =>
    string(3) "php"
    'filename' =>
    string(7) "filters"
    'path' =>
    string(11) "filters.php"
    'type' =>
    string(4) "file"
    'timestamp' =>
    int(1393246029)
    'size' =>
    int(2089)
}
[1] =>
array(5) {
    'dirname' =>
    string(0) ""
    'basename' =>
    string(4) "lang"
    'filename' =>
    string(4) "lang"
    'path' =>
    string(4) "lang"
    'type' =>
    string(3) "dir"
} 
```

如果您希望将额外的属性合并到返回的数组中，您可以使用`listWith()`:

```
$flysystem->listWith(['mimetype', 'size', 'timestamp']); 
```

要获得递归目录列表，第二个参数应该设置为 TRUE:

```
$filesystem->listContents(null, true); 
```

`null`仅仅意味着我们从根目录开始。

要仅获取路径:

```
$filesystem->listPaths(); 
```

## 挂载文件系统

挂载文件系统是一个传统上用于操作系统的概念，但它也可以应用于您的应用程序。本质上，这就像使用某种标识符创建对文件系统的引用——某种意义上的快捷方式。

Flysystem 为此提供了挂载管理器。您可以在实例化时传递一个或多个适配器，使用字符串作为键:

```
$ftp = new League\Flysystem\Filesystem($ftpAdapter);
$s3 = new League\Flysystem\Filesystem($s3Adapter);

$manager = new League\Flysystem\MountManager(array(
    'ftp' => $ftp,
    's3' => $s3,
)); 
```

您也可以在以后装载文件系统:

```
$local = new League\Flysystem\Filesystem($localAdapter);
$manager->mountFilesystem('local', $local); 
```

现在，您可以使用标识符，就像它们是 URI 的协议一样:

```
// Get the contents of a local file…
$contents = $manager->read('local://path/to/file.txt');

// …and upload to S3
$manager->write('s3://path/goes/here/filename.txt', $contents); 
```

使用本质上通用的标识符可能更有用，例如:

```
$s3 = new League\Flysystem\Filesystem($s3Adapter);
$manager = new League\Flysystem\MountManager(array(
    'remote' => new League\Flysystem\Filesystem($s3Adapter),
));

// Save some data to remote storage
$manager->write('remote://path/to/filename', $data); 
```

## 贮藏

Flysystem 还支持许多缓存文件元数据的技术。默认情况下，它使用内存缓存，但是您也可以使用 Redis 或 Memcached。

这里有一个使用 Memcached 的例子；缓存适配器作为可选的第二个参数传递给`Filesystem`构造函数:

```
use League\Flysystem\Adapter\Local as Adapter;
use League\Flysystem\Cache\Memcached as Cache;

$memcached = new Memcached;
$memcached->addServer('localhost', 11211);
$filesystem = new Filesystem(new Adapter(__DIR__.'/path/to/root'), new Cache($memcached, 'storageKey', 300));
// Storage Key and expire time are optional 
```

与文件系统适配器一样，如果您愿意，您可以创建自己的文件系统适配器——只需扩展`League\Flysystem\Cache\AbstractCache`。

## 摘要

在本文中，我介绍了 flysystem，这是一个包，它为各种形式的文件存储提供了一个抽象层。虽然我没有涵盖所有可用的适配器，[文档](https://github.com/thephpleague/flysystem)应该有助于填补空白，引入一些其他特性，并记录未来的任何新适配器。

## 分享这篇文章