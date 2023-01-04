# 在 Redis 中保存 PHP 会话

> 原文：<https://www.sitepoint.com/saving-php-sessions-in-redis/>

会话允许基于 web 的应用程序跨多个 HTTP 请求维护状态。您可以将任意数量的变量注册为会话变量，这些变量通常存储在服务器上的临时目录中，或者通过 cookies 传递给客户端浏览器。随后的请求可以访问保存的信息，并且应用程序的状态在多个请求中保持不变。

PHP 对会话数据的默认处理对于大多数应用程序来说可能已经足够了，但是有时一个项目需要不同的存储方法。幸运的是，会话处理例程可以被一系列函数(在 PHP 的旧版本中)或者一个具有处理会话管理各个方面的方法的类(在新版本中)覆盖。

在本文中，我们将学习如何创建一个定制的会话处理程序，它实现 PHP 的`SessionHandlerInterface`接口并将会话数据存储在 Redis 数据库中。

## 为什么选择定制储物？

您可能想知道为什么有人想要(或需要)经历编写自定义处理程序的麻烦。如果是这样，考虑以下因素可能是有益的。

当应用程序驻留在每个主机的服务器群中来处理负载平衡和冗余请求时，这是很常见的。存储在临时文件中的会话数据只能由特定的主机访问。由于处理请求的服务器可能与处理前一个请求的服务器不同，如果没有替代的处理机制，保证对状态信息的访问是不可能的。会话数据可以存储在中央存储机制中，并可供集群中的所有机器使用。

在共享的主机服务器上，所有的临时会话文件很可能都存储在同一个目录中。在 web 服务帐户下启动的任何进程都可以访问该目录及其内容，这可能会带来严重的安全风险。可以访问其他人的会话数据的恶意用户有能力冒充合法用户。

自定义会话处理还提供了更大程度的灵活性。因为您为信息指定了一种新的存储和访问方法，所以您可以根据需要以任何方式操作数据。也许您想要保留信息以用于安全审计或故障排除。自定义处理程序可以作为钩子，以多种方式扩展应用程序的大小和功能。

## 会话管理操作

由于我们将管理自己的会话数据，而不是让 PHP 为我们处理它，所以我们编写的任何代码都需要处理六个会话管理任务中的每一个。

*   **open**–向用于保存会话信息的存储机制打开一个资源。
*   **关闭**—关闭存储资源并启动任何其他必要的清理活动。
*   **read**–从存储机制中检索之前保存的会话数据。
*   **写入**–存储应用程序需要记住的新会话数据。
*   **销毁**–重置会话并丢弃其中的任何信息。
*   **GC**–在数据变得陈旧且不再需要时，从存储机制中清除数据。

在编写自定义会话处理程序时，必须解决所有这些任务；例如，PHP 不会让我们覆盖 open 进程，但是会忘记覆盖垃圾收集进程。要么全有，要么全无。

在 PHP 5.4 之前的版本中，我们需要按照上面的顺序指定六个可调用函数(一个函数或方法处理一个任务)作为`session_set_save_handler()`函数的参数。在 5.4 和更高版本中，我们可以创建一个实现`SessionHandlerInterface`接口的类，并传递一个实例。该接口公开了六个方法，这些方法具有与 5.4 版之前的方法中的函数相同的签名。

## 存储机制

您对存储机制的选择取决于您的需求。它可以是任何东西——远程临时文件、MySQL 数据库、LDAP 服务器、共享内存段、XML-RPC 服务、IMAP 收件箱等等。在这篇文章中，我将演示在 Redis 中存储会话数据。

PHP 自动生成一个惟一的标识符来跟踪会话，并将它链接到一个特定的客户机。因为这个标记对于每个会话都是惟一的，所以这个标识符非常适合用作键(事实上，这个标记通常在 PHP 默认的基于磁盘的处理方法中用作文件名)。

PHP 还会自动序列化和取消序列化会话数据。也就是说，接收用于存储的数据的方法被传递已经序列化的数据，而检索数据的方法被期望返回序列化的数据。如果我们出于任何原因需要函数`session_encode()`和`session_decode()`，它们也是可用的，但是通常我们可以简单地存储和检索所提供的会话数据。

当然，清理不再需要的陈旧会话是很重要的。例如，如果我们将会话数据存储在一个 MySQL 数据库中，我们希望每条记录都包含一个时间戳。时间戳将由我们覆盖垃圾收集行为的方法来检查。但是有了 Redis，我们可以利用它的`EXPIRE`命令。`EXPIRE`在密钥上设置超时或 TTL(生存时间)，超时过期后密钥自动删除。

## 给我看看代码！

我们现在知道了`SessionHandlerInterface`接口承诺了什么功能，并且对这些方法应该如何与 Redis 交互有了一个粗略的想法，我们可以编写我们的代码了。事不宜迟，下面是这个类:

```
<?php
class RedisSessionHandler implements SessionHandlerInterface
{
    public $ttl = 1800; // 30 minutes default
    protected $db;
    protected $prefix;

    public function __construct(PredisClient $db, $prefix = 'PHPSESSID:') {
        $this->db = $db;
        $this->prefix = $prefix;
    }

    public function open($savePath, $sessionName) {
        // No action necessary because connection is injected
        // in constructor and arguments are not applicable.
    }

    public function close() {
        $this->db = null;
        unset($this->db);
    }

    public function read($id) {
        $id = $this->prefix . $id;
        $sessData = $this->db->get($id);
        $this->db->expire($id, $this->ttl);
        return $sessData;
    }

    public function write($id, $data) {
        $id = $this->prefix . $id;
        $this->db->set($id, $data);
        $this->db->expire($id, $this->ttl);
    }

    public function destroy($id) {
        $this->db->del($this->prefix . $id);
    }

    public function gc($maxLifetime) {
        // no action necessary because using EXPIRE
    }
}
```

您可能注意到的第一件事是`open()`和`gc()`方法是空的。我已经利用了依赖注入来提供 Redis 连接，它将类开放给单元测试，所以在`open()`中不需要做任何事情。在`gc()`中不需要做任何事情，因为 Redis 会为我们处理过期的密钥。

除了 Redis 连接，构造函数还接受前缀。PHP 生成的前缀和会话 ID 组合在一起，用作存储和检索值的键。这主要是一种防止名称冲突的方法，但也提供了一个好处，如果我们在 Redis 客户机中发出`KEYS *`，我们将知道我们在看什么。

当 PHP 调用`write()`方法时，它传递两个值:会话 ID 和会话数据的序列化字符串。一个`SET`命令用于将数据存储在 Redis 中，我们触摸密钥的 TTL。放置在超级全局`$_SESSION`数组中的任何条目现在都被存储。

当 PHP 调用`read()`方法时，它传递会话 ID。一个`GET`命令用于检索数据，我们也再次接触 TTL 毕竟，如果我们正在访问会话，那么认为它仍然新鲜是有意义的。请注意，会话数据以序列化形式直接从存储中返回。PHP 接收字符串，将其解序列化，并填充`$_SESSION`数组。

使用我们的处理程序就像创建一个实例并将该实例传递给`session_set_save_handler()`一样简单。

```
<?php
$db = new PredisClient();
$sessHandler = new RedisSessionHandler($db);
session_set_save_handler($sessHandler);
session_start();
```

当调用`session_start()`时，PHP 将使用我们的定制处理程序来管理会话，而不是它的默认方法；不需要对我们的代码进行其他修改。

如果您停留在 than 5.4 之前的版本，您仍然可以使用上面的类(尽管您必须模仿`SessionHandlerInterface`或者完全移除`implements`)。它的注册如下所示:

```
<?php
$db = new PredisClient();
$sessHandler = new RedisSessionHandler($db);
session_set_save_handler(
    array($sessHandler, 'open'),
    array($sessHandler, 'close'),
    array($sessHandler, 'read'),
    array($sessHandler, 'write'),
    array($sessHandler, 'destroy'),
    array($sessHandler, 'gc')
);
session_start();
```

## 结论

在本文中，我们看到了实现`SessionHandlerInterface`接口并提供 PHP 在 Redis 数据库中存储会话数据所需的逻辑是多么容易。自定义会话处理在代码级别是透明的，其结果是一种令人兴奋的方式，可以不费吹灰之力增加应用程序的安全性和灵活性！要了解更多信息，请阅读 PHP 手册关于[自定义会话处理](http://www.php.net/manual/en/session.customhandler.php "PHP: Custom Session Handlers")的内容，并在 GitHub 上探索本文的[附带代码。](https://github.com/phpmasterdotcom/SavingPHPSessionsInRedis "phpmasterdotcom/SavingPHPSessionsInRedis - GitHub")

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章