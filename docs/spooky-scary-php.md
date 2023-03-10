# 怪异恐怖的 PHP

> 原文：<https://www.sitepoint.com/spooky-scary-php/>

拿出玉米糖和苹果酒；又到了一年的这个时候了！世界其他地方可能不会像美国那样疯狂地庆祝万圣节，但我认为分享一些可怕的 PHP 东西来庆祝这个节日会很有趣。这是一篇有趣的文章，与你分享一些在 PHP 本身中发现的可怕(但合乎逻辑)的行为，以及一些人扭曲 PHP 来完成他们的命令的怪异(可能相当不合逻辑)的方式。把这当成我对你的款待，一点极客的“精神糖果”——因为为什么所有的不给糖就捣蛋的人都应该有所有的好东西？

## 闹鬼的阵列

很久以前，在不远处的一个开发车间里，Arthur 熬夜编写代码。他一点也不知道他将要使用的阵列闹鬼！当他一键一键地打出他的陈述时，他感到一阵寒意顺着他的脊椎流下，但他愚蠢地无视了自己微妙的预感。

```
<?php
$spell = array("double", "toil", "trouble", "cauldron", "bubble");
foreach ($spell as &$word) {
    $word = ucfirst($word);
}
foreach ($spell as $word) {
    echo $word . "n";
}
```

好吧，所以阵列并没有真的闹鬼，但输出肯定是出乎意料的:

```
Double
Toil
Trouble
Cauldron
Cauldron
```

这种怪异行为的原因在于 PHP 如何在第一个`foreach`循环之外持久化引用。`$word`在第二次循环开始时仍然是指向数组最后一个元素的引用。第二个循环的第一次迭代将“double”赋给了`$word`，覆盖了最后一个元素。第二次迭代将“toil”分配给`$word`，再次覆盖最后一个元素。当循环读取最后一个元素的值时，它已经被践踏了几次。

对于这种行为的深入解释，我推荐阅读 Johannes Schlüter 关于这个主题的博客文章， [References 和 foreach](http://schlueters.de/blog/archives/141-References-and-foreach.html "References and foreach - Johannes Schlüter") 。您还可以运行这个稍加修改的版本，并检查它的输出，以便更好地了解 PHP 正在做什么:

```
<?php
$spell = array("double", "toil", "trouble", "cauldron", "bubble");
foreach ($spell as &$word) {
    $word = ucfirst($word);
}
var_dump($spell);
foreach ($spell as $word) {
    echo join(" ", $spell) . "n";
}
```

那天晚上，亚瑟学到了非常重要的一课，他用数组的键将字符串赋值回来，从而修正了他的代码。

```
<?php
foreach ($spell as $key => $word) {
    $spell[$key] = ucfirst($word);
}
```

## 幻影数据库连接

越来越多的人要求 PHP 做的不仅仅是每天生成网页。用 PHP 编写的 shell 脚本的数量正在增加，这些脚本执行的任务也越来越复杂，因为开发人员看到了整合开发语言的好处。通常情况下，这些脚本的性能是可以接受的，为了方便而做出的牺牲是合理的。

因此，Susan 编写了一个类似以下代码的并行处理任务:

```
#! /usr/bin/env php
<?php
$pids = array();
foreach (range(0, 4) as $i) {
    $pid = pcntl_fork();
    if ($pid > 0) {
        echo "Fork child $pid.n";
        // record PIDs in reverse lookup array
        $pids[$pid] = true;
    }
    else if ($pid == 0) {
        echo "Child " . posix_getpid() . " working...n";
        sleep(5);
        exit;
    }
}
// wait for children to finish
while (count($pids)) {
    $pid = pcntl_wait($status);
    echo "Child $pid finished.n";
    unset($pids[$pid]);
}
echo "Tasks complete.n";
```

她的代码让子进程并行执行一些长时间运行的工作，同时父进程继续监控子进程，并在所有子进程都终止时进行报告。

```
Fork child 1634\. 
Fork child 1635\. 
Fork child 1636\. 
Child 1634 working... 
Fork child 1637\. 
Child 1635 working... 
Child 1636 working... 
Fork child 1638\. 
Child 1637 working... 
Child 1638 working... 
Child 1637 finished. 
Child 1636 finished. 
Child 1638 finished. 
Child 1635 finished. 
Child 1634 finished. 
Tasks complete.
```

Susan 的主管没有将状态消息输出到 stdout，而是要求她记录处理开始的时间和所有子进程完成的时间。苏珊使用已经是公司代码库一部分的单例化 PDO 数据库连接机制来扩展她的代码。

```
#! /usr/bin/env php
<?php
$db = Db::connection(); 
$db->query("UPDATE timings SET tstamp=NOW() WHERE name='start time'"); 

$pids = array();
foreach (range(0, 4) as $i) {
    ...
}
while (count($pids)) {
    ...
}

$db->query("UPDATE timings SET tstamp=NOW() WHERE name='stop time'"); 

class Db 
{ 
    protected static $db; 

    public static function connection() { 
        if (!isset(self::$db)) { 
            self::$db = new PDO("mysql:host=localhost;dbname=test",
                "dbuser", "dbpass"); 
            self::$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION); 
        } 
        return self::$db; 
    } 
}
```

Susan 希望看到计时表中的行得到更新；“开始时间”行应该列出整个过程开始时的时间戳，而“停止时间”应该列出所有事情结束时的时间戳。不幸的是，执行抛出了一个异常，数据库并没有反映她的期望。

```
PHP Fatal error:  Uncaught exception 'PDOException' with message 'SQLSTATE[HY000]: General error: 2006 MySQL server has gone away' in /home/susanbrown/test.php:21 
Stack trace: 
#0 /home/susanbrown/test.php(21): PDO->query('UPDATE timers S...') 
#1 {main}
```

```
+------------+---------------------+ 
| name       | tstamp              | 
+------------+---------------------+ 
| start time | 2012-10-13 01:11:37 | 
| stop time  | 0000-00-00 00:00:00 | 
+------------+---------------------+ 
```

像亚瑟的阵列一样，苏珊的数据库也闹鬼了吗？好吧，如果我给你以下线索，看看你能否把这个谜拼起来:

1.  当一个进程分叉时，父进程被复制为子进程。然后，这些重复的流程会从该点开始逐个站点地继续执行。
2.  静态成员在一个类的所有实例之间共享。

PDO 连接被包装成单例，所以应用程序中对它的任何引用都指向内存中的同一个资源。`DB::connection()`首先返回对象引用，父进程分叉，父进程等待时子进程继续处理，子进程终止，PHP 清理已用资源，然后父进程再次尝试使用数据库对象。到 MySQL 的连接已经在一个子进程中关闭，所以最终调用失败。

天真地试图在结束日志查询之前再次获得连接对 Susan 没有帮助，因为相同的失效的 PDO 实例将被返回，因为它是单例的。

我建议避免使用单例变量——它们只不过是花哨的面向对象的全局变量，会使调试变得困难。即使在我们的例子中连接仍然会被一个子进程关闭，如果在第二个查询之前调用它，如果没有使用单例，至少`DB::connection()`会返回一个新的连接。

但是更好的办法是理解分叉时执行环境是如何被克隆的，以及各种资源是如何在所有进程中受到影响的。在这种情况下，更明智的做法是在子线程分叉后连接到父线程中的数据库，如果需要，子线程会自己连接。该连接不应共享。

```
#! /usr/bin/env php
<?php
$pids = array();
foreach (range(0, 4) as $i) {
    ...
}

$db = Db::connection(); 
$db->query("UPDATE timings SET tstamp=NOW() WHERE name='start time'"); 

while (count($pids)) {
    ...
}

$db->query("UPDATE timings SET tstamp=NOW() WHERE name='stop time'");
```

## 一个配得上弗兰肯斯坦博士的 API

玛丽·雪莱的《弗兰肯斯坦》讲述了一个科学家创造生命的故事，但他对生命的丑陋感到厌恶，因此放弃了生命。经过一点无端的死亡和破坏，弗兰肯斯坦博士追求他的创作字面上的地球的尽头，寻求其破坏。我们中的许多人已经将生命注入到如此丑陋的代码中，以至于我们后来希望我们可以逃离它——代码如此丑陋，如此迟钝，如此纠结，以至于它让我们想要作呕，但它只想要爱和理解。

几年前，我曾考虑过一个关于数据库接口的想法，如果它们更紧密地遵循 Unix 的“一切都是文件”哲学，它们会是什么样子:查询将被写入“文件”，结果集将从“文件”中读取。一件事接一件事，在我自己编写了一些死亡和毁灭代码之后，我写了下面的类，它与我最初的想法没有什么关系:

```
<?php
class DBQuery implements Iterator
{
    protected $db;
    protected $query;
    protected $result;
    protected $index;
    protected $numRows;

    public function __construct($host, $dbname, $username, $password) {
        $this->db = new PDO("mysql:dbname=$dbname;host=$host",
            $username, $password);
    }

    public function __get($query) {
        $this->query = $query;
        $this->result = $this->db->query($query);
        return $this->numRows = $this->result->rowCount();
    }

    public function __call($query, $values) {
        $this->query = $query;
        $this->result = $this->db->prepare($this->query);
        $this->result->execute($values);
        return $this->numRows = $this->result->rowCount();
    }

    public function clear() {
        $this->index = 0;
        $this->numRows = 0;
        $this->query = "";
        $this->result->closeCursor();
    }

    public function rewind() {
        $this->index = 0;
    }

    public function current() {
        return $this->result->fetch(PDO::FETCH_ASSOC,
            PDO::FETCH_ORI_ABS, $this->index);
    }

    public function key() {
        return $this->index;
    }

    public function next() {
        $this->index++;
    }

    public function valid() {
        return ($this->index < $this->numRows);
    }

    public function __toString() {
        return $this->query;
    }
}
```

结果是天才的，但令人厌恶的:一个看起来像对象(没有真正的 API 方法)或数组或字符串的实例…

```
<?php
$dbq = new DBQuery("localhost", "test", "dbuser",
    "dbpassword");

// query the database if the user is authorized
// (instance behaves like an object)
$username = "administrator";
$password = sha1("password");
if (!$dbq->{"SELECT * FROM admin_user WHERE username=? " .
    "AND password=?"}($username, $password)) {
    die("Unauthorized.");
}

// query the database and display some records
// (instance is iterable like an array)
$dbq->{"SELECT id, first_name, last_name FROM employee"};
foreach ($dbq as $result) {
    print_r($result);
}

// casting the object string yields the query
echo "Query: $dbq";
```

此后不久，我在博客上写下了这件事，并称之为邪恶。看到这些的朋友和同事反应都差不多:“太棒了！现在杀了它……用火杀了它。”

但是这些年来，我承认我在这个问题上有所软化。它真正改变的唯一规则是程序员对像`query()`和`result()`这样名字平淡的方法的期望。相反，它使用查询字符串本身作为查询方法，对象是接口和结果集。当然，这并不比一个过度一般化的 ORM 接口更糟糕，它将`select()`和`where()`方法链接在一起，看起来像一个 SQL 查询，但有更多的`->`。也许我的类真的没有那么邪恶？也许它只是想被爱？我当然不想死在北极！

## 最后

我希望你喜欢这篇文章，这些例子不会给你(太多)噩梦！我相信你也有自己的困扰或可怕的代码，无论你在世界的哪个角落，都没有必要让假期的乐趣消失，所以请在下面的评论中分享你的可怕 PHP 故事吧！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章