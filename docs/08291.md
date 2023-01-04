# 使用 Predis 介绍 PHP 中的 Redis

> 原文：<https://www.sitepoint.com/an-introduction-to-redis-in-php-using-predis/>

Redis 是一个开放源码的数据结构服务器，具有内存中的数据集，由于其内置的数据类型，它不仅仅是简单的键/值存储。

它于 2009 年由 Salvatore Sanfilippo 创立，由于其知名度迅速增长，被 VMware(后来他们聘请 Sanfilippo 全职从事该项目)、GitHub、Craigslist、Disqus、Digg、暴雪、Instagram 等大公司选中(见 redis.io/topics/whos-using-redis[)。](http://redis.io/topics/whos-using-redis)

您可以使用 Redis 作为会话处理程序，如果您在负载平衡器后面使用多服务器架构，这将非常有用。Redis 还有一个发布/订阅系统，这对于创建在线聊天或现场预订系统非常有用。关于 Redis 及其所有命令的文档和更多信息可以在该项目的网站上找到。

关于 Redis 和 Memcache 孰优孰劣还有很多争论，尽管基准测试显示它们在基本操作上表现相当。Redis 比 Memcache 拥有更多的特性，比如内存和磁盘持久性、原子命令和事务，并且不是将每次更改都记录到磁盘，而是记录到服务器端数据结构。

在本文中，我们将看看 Redis 必须使用 Predis 库提供的一些基本但强大的命令。

## 易于安装

Redis 易于安装，产品的[下载页面](http://redis.io/download)上公布了简短的安装说明。从我自己的经验来看，如果你运行 Ubuntu，那么如果你没有安装 TCL，你会得到一个错误(只需运行 sudo apt-get install tcl)。安装 Redis 后，您可以运行服务器:

```
gafitescu@ubun2:~$ /usr/local/bin/redis-server
* The server is now ready to accept connections on port 6379
```

Redis 有许多语言的客户端库，[在 Redis 网站](http://redis.io/clients)上列出，通常每种语言都有几个可用的！对于 PHP，有五个。在这篇文章中，我将使用[的 Predis 库](https://github.com/nrk/predis)，但是你可能也想看看[的 phpredis](https://github.com/nicolasff/phpredis) ，它被编译和安装为一个 PHP 模块。

如果您像我一样在机器上安装了 Git，那么您需要做的就是克隆 Predis 存储库。否则，您需要下载 ZIP 存档并将其解压缩。

```
gafitescu@ubun2:~$ git clone git://github.com/nrk/predis.git
```

为了测试一切，创建包含以下内容的文件`test.php`,以测试您是否可以成功地连接到运行 Redis 的 Predis 服务器:

```
<?php
require "predis/autoload.php";
PredisAutoloader::register();

// since we connect to default setting localhost
// and 6379 port there is no need for extra
// configuration. If not then you can specify the
// scheme, host and port to connect as an array
// to the constructor.
try {
    $redis = new PredisClient();
/*
    $redis = new PredisClient(array(
        "scheme" => "tcp",
        "host" => "127.0.0.1",
        "port" => 6379));
*/
    echo "Successfully connected to Redis";
}
catch (Exception $e) {
    echo "Couldn't connected to Redis";
    echo $e->getMessage();
}
```

当您运行它时，您应该希望看到消息“成功连接到 Redis”。

## 使用 Redis

在这一节中，您将获得 Redis 提供的最常用命令的概述。Memcache 对它们中的大多数都有等价的，所以如果您熟悉 Memcache，那么这个清单对您来说会很熟悉。

### 设置、获取和存在

Redis 使用的最重要的命令是`SET`、`GET`和`EXISTS`。您可以使用这些命令来存储和检查将要被多次访问的临时信息，通常是以键/值的方式。例如:

```
<?php
$redis->set("hello_world", "Hi from php!");
$value = $redis->get("hello_world");
var_dump($value);

echo ($redis->exists("Santa Claus")) ? "true" : "false";
```

`set()`方法用于为特定的键设置一个值，在本例中，键是“hello_world”，值是“Hi from php！”`get()`方法检索键的值，在本例中也是“hello_world”。`exists()`方法报告所提供的密钥是否在 Redis 的存储中。

密钥不限于字母数字字符和下划线。以下内容同样适用:

```
<?php
$redis->set("I 2 love Php!", "Also Redis now!");
$value = $redis->get("I 2 love Php!");
```

### INCR(英格兰)和 DECR(英格兰)

`INCR`和`DECR`命令用于增加和减少数值，是维护计数器的好方法。INCR 和 DECR 将其值递增/递减 1；您也可以使用`INCRBY`和`DECRBY`以更大的间隔进行调整。这里有一个例子:

```
<?php
// increment the number of views by 1 for an article
// with id 234
$redis->incr("article_views_234");

// increment views for article 237 by 5
$redis->incrby("article_views_237", 5);

// decrement views for article 237
$redis->decr("article_views_237");

// decrement views for article 237 by 3
$redis->decrby("article_views_237", 3);
```

### 重定向数据类型

正如我前面提到的，Redis 有内置的数据类型。您可能认为在 Redis 这样的 NoSQL 键值存储系统中使用数据类型很奇怪，但是这对于开发人员以更有意义的方式组织信息和执行特定的操作是很有用的，当数据被键入时，执行特定的操作通常要快得多。Redis 的数据类型有:

*   **String**–Redis 中使用的基本数据类型，可以存储从几个字符到整个文件的内容。
*   **List**–一个简单的字符串列表，按照元素的插入顺序排列。您可以在列表的头部和尾部添加和删除元素，因此可以使用这种数据类型来实现队列。
*   **Hash**–字符串键和字符串值的映射。通过这种方式，您可以表示对象(把它想象成一个一级深度 JSON 对象)。
*   Set–一个无序的字符串集合，您可以在其中添加、删除和测试成员的存在。一个约束是不允许有重复的成员。
*   **有序集合**–集合数据类型的一个特例。不同之处在于，每个成员都有一个相关的分数，用于从最小到最大的分数对集合进行排序。

到目前为止，我只演示了字符串，但是有一些命令可以使处理其他数据类型的数据变得同样简单。

### HSET、HGET 和 hgetall、HINCRBY 和 HDEL

这些命令用于处理 Redis 的散列数据类型:

*   **HSET**–为散列对象上的一个键设置值。
*   **HGET**–获取散列对象上某个键的值。
*   **HINCRBY**–用指定的值递增散列对象的键值。
*   **HDEL**–从对象中移除一个键。
*   获取一个对象的所有键和数据。

下面的例子演示了它们的用法:

```
<?php
$redis->hset("taxi_car", "brand", "Toyota");
$redis->hset("taxi_car", "model", "Yaris");
$redis->hset("taxi_car", "license number", "RO-01-PHP");
$redis->hset("taxi_car", "year of fabrication", 2010);
$redis->hset("taxi_car", "nr_starts", 0);
/*
$redis->hmset("taxi_car", array(
    "brand" => "Toyota",
    "model" => "Yaris",
    "license number" => "RO-01-PHP",
    "year of fabrication" => 2010,
    "nr_stats" => 0)
);
*/
echo "License number: " . 
    $redis->hget("taxi_car", "license number") . "<br>";

// remove license number
$redis->hdel("taxi_car", "license number");

// increment number of starts
$redis->hincrby("taxi_car", "nr_starts", 1);

$taxi_car = $redis->hgetall("taxi_car");
echo "All info about taxi car";
echo "<pre>";
var_dump($taxi_car);
echo "</pre>";
```

### -伊甸园字幕组=-翻译

这些是在 Redis 中使用列表类型的重要命令。Redis 列表类似于 PHP 中的数组，为实现队列、堆栈或一定数量元素的有限集合提供了强大的支持。

*   **LPUSH**–将元素添加到列表中。
*   **r push**–将元素追加到列表中。
*   **LPOP**–移除并获取列表的第一个元素。
*   **RPOP**–移除并获取列表中的最后一个元素。
*   LLEN–获取列表的长度。
*   从列表中获取元素。

```
<?php
$list = "PHP Frameworks List";
$redis->rpush($list, "Symfony 2");
$redis->rpush($list, "Symfony 1.4");
$redis->lpush($list, "Zend Framework");

echo "Number of frameworks in list: " . $redis->llen($list) . "<br>";

$arList = $redis->lrange($list, 0, -1);
echo "<pre>";
print_r($arList);
echo "</pre>";

// the last entry in the list
echo $redis->rpop($list) . "<br>";

// the first entry in the list
echo $redis->lpop($list) . "<br>";
```

### 过期、过期、TTL 和持续

最有可能的是，当你设置一个键时，你不希望它被永久保存，因为经过一段时间后，它可能不再相关。您需要更新它的值或删除它，以减少内存使用，从而获得更好的性能。Redis 提供了四个命令，让您可以轻松地处理数据持久性。

*   **EXPIRE**–设置密钥的过期超时(以秒为单位),过期后密钥及其值将被删除。
*   **expire at**–使用 unix 时间戳设置到期时间，该时间戳表示何时删除密钥和值。
*   **TTL**–获取过期密钥的剩余生存时间。
*   **PERSIST**–删除给定密钥的到期时间。

```
<?php
// set the expiration for next week
$redis->set("expire in 1 week", "I have data for a week");
$redis->expireat("expire in 1 week", strtotime("+1 week"));
$ttl = $redis->ttl("expire in 1 week"); // will be 604800 seconds

// set the expiration for one hour
$redis->set("expire in 1 hour", "I have data for an hour");
$redis->expire("expire in 1 hour", 3600);
$ttl = $redis->ttl("expire in 1 hour"); // will be 3600 seconds

// never expires
$redis->set("never expire", "I want to leave forever!");
```

## 摘要

在本文中，我们只查看了 Redis 命令的简短列表，但是您可以在 Redis 网站上查看完整的命令列表。事实上，Redis 提供的不仅仅是 Memcache 的替代品。

Redis 在这里是为了长期发展；它有一个不断增长的社区，支持所有主要语言，并通过主从复制提供持久性和高可用性。Redit 是开源的，所以如果你是一个 C 大师，那么你可以从 [GitHub](http://github.com/antirez/redis) 获得它的源代码，成为一名贡献者。

如果你想在项目网站之外寻找更多的信息，你可能想看看两本很棒的 Redis 书籍， [Redis 食谱](http://shop.oreilly.com/product/0636920020127.do)和 [Redis:权威指南](http://shop.oreilly.com/product/0636920014294.do)。

## 分享这篇文章