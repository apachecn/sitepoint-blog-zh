# PHP 应用程序的水平扩展，第 2 部分

> 原文：<https://www.sitepoint.com/horizontal-scaling-php-apps-part-2/>

在第 1 部分的[中，我们讨论了应用层的水平扩展——通过负载平衡器和其他方式向外扩展服务器以同时处理 PHP 代码。我们讨论了本地数据的共享，谈到了潜在的优化，并解释了负载平衡器的基础知识。](https://www.sitepoint.com/horizontal-scaling-php-apps)

然而，应用层并不是唯一需要扩展的东西。随着对我们的应用程序的更大需求，对数据库的更高读/写操作的需求也浮出了水面。在这一部分中，我们将研究如何扩展数据库，解释复制，并涵盖一些您可能希望避免的常见陷阱。

## 最佳化

和第 1 部分一样，我们需要首先提到优化。正确地索引数据库，确保表格包含最少的重要数据，并在其他表格中保留次要信息(`users_basic` + `users_additional` + `users_alt_contacts`等——被称为[数据库分片](http://en.wikipedia.org/wiki/Shard_%28database_architecture%29)——这是一个复杂的主题，肯定会有自己的文章)，进行小型的原子查询，而不是大型的即时计算——所有这些方法都可以帮助您加快数据库的速度，避免瓶颈。不过，有一个方面会更有帮助，那就是 [*查询缓存*](http://dev.mysql.com/doc/refman/5.1/en/query-cache.html) 。

数据库服务器通常会缓存最后执行的结果和已编译的 SELECT 查询。这允许客户端(我们的应用程序)从缓存中接收数据，而不是等待再次执行。这种方法节省了 CPU 周期，更快地产生结果，并且将服务器从不必要的计算中解放出来。但是查询缓存的大小是有限的，而且有些数据集比其他数据集更改得更频繁。对我们所有的数据使用缓存是荒谬的，尤其是当一些信息比其他信息变化得更快时。虽然缓存可以根据数据库供应商进行微调，但是有一种方法非常适合查询缓存解决方案——服务器的上下文分组。

当您对服务器进行分组时，您将拥有多组服务器，每组服务器专用于应用程序的特定部分。假设我正在制作一个在线赌博网站——我可以有一个游戏聊天组，一个正在进行的游戏组，一个用户帐户组。用户帐户组不需要那么多的读取或写入，至少不像聊天或游戏功能那样多，所以我们可以把它做得更小—有 5 台服务器。在聊天组中，我们可以有 10 台服务器，因为数百万用户的实时通信对于高质量的赌博体验非常重要。但是没有一个方面像真正的游戏——我们的面包和黄油——一样重要，所以他们有一个特别大的团队，可能有 20 台服务器。

这确保了您在一个组中进行所有与用户相关的读写，而不接触其他组。游戏需要快速，所以最大的群体被用来确保他们得到快速服务(包括读写操作)，而用户账户页面对已经登录并准备改变的人来说并不重要——我们已经吸引了他们的注意力，不需要在我们应用程序的非关键业务部分给他们留下超快的反应印象。

所有组共享相同的总数据(见下面的 MSR)，所以你可以将服务器从一个组移动到另一个组——假设有一个大型扑克锦标赛，你有数百万用户观看游戏——游戏组可以受益于从聊天组中取出的 4-5 个服务器，以确保它可以处理负载。也就是说，集群数据库有内置的解决方案，其中一个就是主从复制。

## 主从复制

主从复制(MSR)是现代数据库中非常常见的特性，通常是内置的。搜索您的特定数据库供应商的文档，以获得关于它是否受本机支持的信息。MSR 是将所有数据库写操作从一个服务器(主服务器)发送到一个或多个从服务器的过程。然后，从设备重放查询，从而复制主设备的数据。与 web 应用程序集成，这是它的工作方式，一步一步来:

*   访问者在数据库上执行写操作。例如，他更改了一些个人资料信息。
*   应用程序照常将查询发送到主数据库服务器
*   主设备执行该查询，并将其转发给所有从设备
*   从机执行查询，主机和从机现在都有相同的数据
*   对从设备执行进一步的读取操作

最后一步才是最重要的——我们在从机上执行读操作。这本身就是机器之间的分工——主服务器可以只进行写操作(一般来说，web 应用程序中的写操作远远少于读操作，因此一个主服务器通常就足够了),而大量的从服务器可以用来读取数据，因此没有特定的服务器负担过重。

在今天的 MariaDB 和 MySQL 安装中，MSR 通常是默认激活的。

### 分离读取和写入

这是需要对你的架构进行轻微修改的地方，它的复杂性与你的代码质量和使用一个框架或者一个好的项目结构是相互的。当需要分离读取和写入时，您需要建立单独的连接。对于写操作，您需要连接到一个“写数据库”(主数据库)，对于读操作，您需要连接到一个从数据库。当然，这可以手动完成。对于编写，您将连接到一个特别定义的主配置:

```
<?php
$config = $this->getService('configProvider');

$master = new PDO(
    'mysql:dbname='.$config->db->master->name.';host='.$config->db->master->host, 
    $config->db->master->user, 
    $config->db->master->password
);
$statement = $master->prepare('SOME QUERY FOR UPDATING');
//...
```

而对于读取，需要通过一组不同的配置值建立新的连接:

```
<?php
$config = $this->getService('configProvider');

$slave = new PDO(
    'mysql:dbname='.$config->db->slave->name.';host='.$config->db->slave->host, 
    $config->db->slave->user, 
    $config->db->slave->password
);
$results = $slave->query('SOME QUERY FOR READING');
//...
```

如果我们有多个从机，并且我们知道它们的主机地址，我们可以做一些随机化处理:

```
<?php
$config = $this->getService('configProvider');

// Pick random slave from list of possible slaves
$slaveConfig = $config->db->slaves[array_rand($config->db->slaves)];

$slave = new PDO(
    'mysql:dbname='.$slaveConfig->name.';host='.$slaveConfig->host, 
    $slaveConfig->user, 
    $slaveConfig->password
);
$results = $slave->query('SOME QUERY FOR READING');
//...
```

意识到这一点并不容易，每次你需要读或写的时候都要这么做。更好的解决方案是在某种服务容器中预初始化一个主数据库连接，然后只访问该连接进行写入，而不需要在每次需要写入时重新建立连接。对于从服务器，同样的方法也适用——将随机化函数构建到一个“从”数据库适配器中，只需调用类似于`$this->getService('db')->slave`的东西，它会自动返回随机选择的一个。这样，您再也不用担心手动选择它们了，并且当您向集群添加更多的从属服务器时，只需将它们的主机地址包含到配置文件中。

想更进一步吗？让从抓取服务只选择那些此刻没有被加载或停机的从——让从重复报告它们在某个地方的 CPU/RAM 使用情况，并确保从抓取类总是选择最少被占用的那个。这个抽象的选择器类还应该确保它连接到另一个从属服务器，如果它最初试图连接的那个从属服务器关闭了的话——您需要数据库读取对最终用户保持透明，但是您仍然希望管理员知道其中一个从属服务器有问题:

```
<?php

// ... some class, some connect() method

$config = $this->getService('configProvider');
$mailer = $this->getService('mailer');
$validSlaves = $config->db->slaves;
$bConnectionSuccessful = false;

while (!empty($validSlaves) && !$bConnectionSuccessful) {
    $randomSlaveKey = array_rand($validSlaves);
    $randomSlave = $validSlaves[$randomSlaveKey];
    try {
        $slave = new PDO(
            'mysql:dbname='.$randomSlave->name.';host='.$randomSlave->host, 
            $randomSlave->user, 
            $randomSlave->password
        );
        $bConnectionSuccessful = true;
    } catch (\PDOException $e) {
        unset($validSlaves[$randomSlaveKey]);
        $mailer->reportError( ... ); // Some kind of email sent to the admins, or the master log, etc
    } catch (\Exception $e) {
        unset($validSlaves[$randomSlaveKey]);
        $mailer->reportError( ... ); // Some kind of different email sent to the admins, or the master log, etc
    }
}

if ($bConnectionSuccessful) {
    return $slave;
} 

$mailer->reportError( ... ); // Report that the entire attempt to connect to any slave failed
throw new \Exception( ... ); // or report via an exception with details in the message
```

这只是伪代码，但您已经明白了要点——尝试连接随机选择的从机，如果失败，从有效从机数组中删除该从机，报告问题，并在另一个从机上再次尝试，直到有更多的从机可供尝试。

### 读/写同步延迟

可能造成数据一致性威胁的一件事是主设备和从设备之间的同步延迟。对于较小的写操作，复制到从服务器几乎是即时的，而较大的查询自然需要更长的时间来执行——这种延迟也会随着从服务器的增长而增加，因为主服务器有更多的查询副本要分发——一些从服务器会比其他从服务器更快准备好。然而，无论集群网络的质量如何，单个机器的速度如何，或者查询的大小如何，世界上没有一种设置能够准确地完成以下任务:

```
<?php

$db = $this->getService('db');
$master = $db->get('master');
$slave = $db->get('slave');

// value is currently 1
$master->exec('UPDATE `some_table` SET `value` += 1 WHERE `id` = 1'); // executes
$slave->query('SELECT `value` FROM `some_table` WHERE `id` = 1'); // value is still 1
```

这些语句彼此执行得太近，从机根本没有办法及时获得更新以准确反映它。

有各种各样的解决方法，尽管没有一个是万无一失的。这种不一致是你必须接受的。在大多数情况下，写入的数据不需要立即读取。如果是这样，最好只取一个近似值——如果您希望值增加 1，那么只需从从机读取原始值，将结果加 1，然后显示给用户。主服务器仍然可以在后台传播实际的更新。

### 主控失败

但是，如果主控失败了呢？整个系统会停止运转吗？嗯，不应该。主设备故障转移有解决方案，如果主设备出现故障，它们通常通过将从设备变成主设备来解决问题。然而，要做到这一点，还需要额外的架构改变。该过程如下:

*   主设备出现故障，无法再接受写入
*   我们的主获取脚本识别到这一点，报告一个错误，并选择一个随机可用的从
*   这个从设备被给予成为主设备的信号
*   所有其他从设备都被给予切换到新的主设备的信号
*   如果主服务器在写入过程中死亡，没有时间将数据更改传播给所有从服务器，或者如果从服务器在变成主服务器之前没有时间执行查询，那么少量数据丢失是可能的
*   当主服务器重新联机时，它应该被销毁并从头开始设置为新主服务器的从服务器——做任何其他事情都是没有意义的，因为在停机期间赶上错过的查询将是徒劳的。如果失败的主机是最强大的机器，并且您真的希望它继续作为主机，则需要采取额外的安全措施，这可能会保证短暂的停机时间，直到重新启动的主机赶上一切。

特别是在 MySQL 和 MariaDB 中，您可以使用[将主设备更改为](http://dev.mysql.com/doc/refman/5.7/en/change-master-to.html)命令让从设备切换到主设备，而从从设备升级到主设备是通过[停止从设备](http://dev.mysql.com/doc/refman/5.7/en/stop-slave.html)和[重置主设备](http://dev.mysql.com/doc/refman/5.7/en/reset-master.html)完成的。有关主机切换的更多信息，请参见[文档](http://dev.mysql.com/doc/refman/5.7/en/replication-solutions-switch.html)。

## 结论

在第 2 部分中，我们介绍了数据库复制和数据库集群。现在这两个部分都已经完成了，希望您已经收集了足够的初始知识，可以自己着手构建一个优秀的可伸缩环境。你认为在横向扩展中有什么重要的建议吗？您想看看更复杂的第 3 部分吗？我们是否错过了一些关键的方面？请在下面的评论中告诉我们！

## 分享这篇文章