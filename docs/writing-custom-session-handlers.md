# 编写自定义会话处理程序

> 原文：<https://www.sitepoint.com/writing-custom-session-handlers/>

会话是一种帮助 web 程序员克服互联网无状态本质的工具。您可以使用它们来构建购物车、监控对网站的访问，甚至跟踪用户如何在您的应用程序中导航。PHP 的默认会话处理行为在大多数情况下可以满足您的所有需求，但是有时您可能希望扩展功能并以不同的方式存储会话数据。本文将向您展示默认功能是如何工作的，然后继续向您展示如何覆盖它来提供一个定制的解决方案。

## 会话存储的剖析

在实现自定义会话保存处理程序之前，理解 PHP 通常如何存储会话数据是有帮助的。数据保存在服务器上的一个小文件中，该文件与一个唯一的 ID 相关联，然后由浏览器存储在客户机上的 cookie 中。如果不使用 cookies，ID 通常作为 URL 中的参数传递。无论使用哪种方法，PHP 都使用会话 ID 在后续的页面请求中检索会话数据。您可以通过首先确定 PHP 保存会话数据的位置并检查其内容来检查这是如何工作的。你可以检查你的`php.ini`文件中的`session.save_path`指令，或者使用`session_save_path()`函数输出路径。

```
<?php
echo session_save_path();
```

输出将是存储会话数据的路径。您可以在`php.ini`中更改位置，或者简单地将新路径传递给`session_save_path()`函数。

```
<?php
session_save_path("/path/to/session/data");
```

如果您想设置一个不同的目录来存储会话数据，选择一个在您的根 web 目录之外的位置是一个很好的做法，因为这可以降低有人劫持会话的风险。只要确保您已经给了目录足够的权限，可以被服务器读取和写入。

现在您已经知道会话数据存储在哪里，您可以导航到该目录并找到存储与每个活动会话相关的信息的各个文件。通常，文件名为“sess_ ”,后跟与数据相关联的会话 ID。您可以通过使用`session_id()`功能找到您的会话 ID。

```
<?php
echo session_id();
```

输出是一个 32 个字符的伪随机字符串，类似于以下内容:

```
k623qubavm8acku19somu6ce1k0nb9aj
```

打开文件`sess_k623qubavm8acku19somu6ce1k0nb9aj`，你会看到一个描述会话中数据的字符串。如果您要像这样在会话中存储以下数组:

```
<?php
$arr = array("red", "blue"); 
$_SESSION["colors"] = $arr;
```

该文件的内容如下所示:

```
colors|a:2:{i:0;s:3:"red";i:1;s:4:"blue";}
```

数据的编码方式与`serialize()`函数处理数据的方式非常相似。当数据存储在一个会话中时，所有的数据被整理在一起、序列化，并且在 PHP 中默认的会话存储机制的情况下，被放在一个文件中。当您需要检索数据时，会话会取消数据的序列化，以供应用程序使用。

这里需要记住的是，会话读写数据的默认方式是通过`serialize()`和`unserialize()`函数。如果您要改变这些数据的存储方式，情况也是如此。您可以更改数据的存储位置，但不能更改存储方式。

## 会话生命周期

当您使用`session_start()`开始或继续一个会话时，该会话的数据文件被打开，数据被读入`$_SESSION`数组。当脚本执行结束时，数据被保存回文件。因此，当您设置会话变量时，它不会立即存储。当然，您可以通过调用`session_write_close()`来强制会话存储数据。

`session_set_save_handler()`提供了一种用新功能覆盖默认会话处理机制的方法，以便您可以将数据存储在您喜欢的位置。它需要六个参数，每个参数都是处理会话生命周期特定阶段的回调。它们是:

1.  打开会话文件
2.  关闭会话文件
3.  读取会话数据
4.  写入会话数据
5.  破坏会话
6.  会话文件和数据的垃圾收集

您必须为生命周期的每个阶段注册一个函数，否则 PHP 将发出一个无法定位该函数的警告。

```
Warning: session_set_save_handler(): Argument 1 is not a valid callback
```

回调可以用 PHP 允许的任何方式定义，所以它们可以是直接的函数、闭包、对象方法或静态类方法。

```
<?php
session_set_save_handler("open", "close", "read", "write", "destroy", "garbage");
```

在这个例子中，我假设函数`open()`、`close()`、`read()`、`write()`、`destroy()`和`garbage()`已经被定义并注册为对`session_set_save_handler()`的回调。

## 创建自定义会话保存处理程序

为了展示每个回调函数，我将覆盖默认的会话处理行为，将它们存储在 MySQL 数据库中。该表的基本模式应该包括一个会话 ID 字段、一个数据字段和一个确定上次访问会话时间的字段。

```
CREATE TABLE session ( 
    session_id CHAR(32) NOT NULL, 
    session_data TEXT NOT NULL, 
    session_lastaccesstime TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP, 
    PRIMARY KEY (session_id)
);
```

生成会话 ID 的默认方法使用 MD5 算法，该算法生成 32 个字符的字符串，因此我将我的`session_id`字段设置为`CHAR(32)`。会话保存的数据可以是无限的，但是最好不要滥用这个工具。访问时间字段可以是`TIMESTAMP`类型。

### 会议开幕

会话经历的第一个阶段是打开会话文件。在这里你可以执行任何你喜欢的动作；PHP 文档表明这个函数应该被当作一个构造函数，所以如果你使用 OOP 方法，你可以用它来初始化类变量。回调采用两个自动传递的参数——保存路径和会话名称。

```
<?php
function open($path, $name) {
    $db = new PDO("mysql:host=myhost;dbname=mydb", "myuser", "mypassword");

    $sql = "INSERT INTO session SET session_id =" . $db->quote($sessionId) . ", session_data = '' ON DUPLICATE KEY UPDATE session_lastaccesstime = NOW()";
    $db->query($sql);    
}
```

这里的示例实现将在数据库中创建一个条目来存储数据。如果会话条目已经存在，`session_last_accesstime`将用当前时间戳更新。时间戳用于确保会话是“实时的”；稍后，它将与垃圾收集一起用于清除过时的会话，我稍后将对此进行讨论。

### 阅读会话

会话打开后，会话的内容将立即从您指定的任何存储中读取并放入`$_SESSION`数组中。回调使用一个参数，即会话 ID，它使您能够识别正在读取的会话。回调必须返回一串序列化的数据，因为 PHP 会像前面讨论的那样对其进行解序列化。没有必要在你的代码中迎合这一点。如果此会话没有会话数据，您应该返回一个空字符串。

```
<?php
function read($sessionId) { 
    $db = new PDO("mysql:host=myhost;dbname=mydb", "myuser", "mypassword");

    $sql = "SELECT session_data FROM session where session_id =" . $db->quote($sessionId);
    $result = $db->query($sql);
    $data = $result->fetchColumn();
    $result->closeCursor();

    return $data;
}
```

理解这一点很重要，即不是每次访问会话变量时都提取这些数据。只有在会话生命周期的开始，当 PHP 调用 open 回调，然后调用 read 回调时，才会拉取它。

### 向会话写入

将数据写回到您正在使用的任何一个存储，要么在脚本执行结束时发生，要么在您调用`session_write_close()`时发生。回调接收两个参数，要写入的数据和会话 ID。接收到的数据已经被 PHP 序列化了。

```
<?php
function write($sessionId, $data) { 
    $db = new PDO("mysql:host=myhost;dbname=mydb", "myuser", "mypassword");

    $sql = "INSERT INTO session SET session_id =" . $db->quote($sessionId) . ", session_data =" . $db->quote($data) . " ON DUPLICATE KEY UPDATE session_data =" . $db->quote($data);
    $db->query($sql)
}
```

有许多方法可以处理传递给读写回调的数据。PHP 将数据序列化传递给 write 函数，并期望它从 read 函数序列化回来，但这并不意味着您必须以这种方式存储它。您可以在写入回调中立即取消数据的序列化，然后根据数据执行一些操作，或者按照您希望的方式存储数据。这同样适用于读取回调。所有这些决定都依赖于实现，由您来决定什么最适合您的情况。

### 会议闭幕

关闭会话发生在会话生命周期结束时，就在写入会话数据之后。没有参数传递给这个回调函数，所以如果您需要处理特定于会话的东西，您可以调用`session_id()`来获得 ID。

```
<?php
function close() {
    $sessionId = session_id();
    //perform some action here
}
```

### 破坏会话

手动销毁会话是必不可少的，尤其是当使用会话作为保护应用程序部分的一种方式时。当调用`session_destroy()`函数时，回调被调用。会话 ID 作为参数传递。

```
<?php
function destroy($sessionId) {
    $db = new PDO("mysql:host=myhost;dbname=mydb", "myuser", "mypassword");

    $sql = "DELETE FROM session WHERE session_id =" . $db->quote($sessionId); 
    $db->query($sql);

    setcookie(session_name(), "", time() - 3600);
}
```

在其默认的会话处理能力中，`session_destroy()`函数将清除`$_SESSION`数组中的所有数据。关于 php.net 的文档指出，任何全局变量或 cookies(如果使用的话)都不会被清除，所以如果你正在使用一个定制的会话处理程序，你也可以在这个回调中执行这些任务。

### 碎片帐集

会话处理程序需要迎合这样一个事实，即程序员并不总是有机会手动销毁会话数据。例如，当用户注销并且不再需要时，您可以销毁会话数据，但是不能保证用户会使用注销功能来触发删除。PHP 偶尔会调用垃圾收集回调来清除过时的会话数据。这里传递的参数是会话的最大生存期，它是一个整数，详细描述了生存期跨越的秒数。

```
<?php
function gc($lifetime) {
    $db = new PDO("mysql:host=myhost;dbname=mydb", "myuser", "mypassword");

    $sql = "DELETE FROM session WHERE session_lastaccesstime < DATE_SUB(NOW(), INTERVAL " . $lifetime . " SECOND)";
    $db->query($sql);
}
```

PHP 随机执行垃圾收集。垃圾收集被调用的概率是通过`php.ini`指令`session.gc_probability`和`session.gc_divisor`决定的。例如，如果概率设置为 1，除数设置为 100，垃圾收集器有 1%的机会在每次请求时运行(1/100)。

示例回调使用会话的最大生存期来比较上次访问会话的时间。如果超过了会话的生存期，它将删除该会话以及与其相关的所有数据。

## 摘要

在许多情况下，更改 PHP 会话处理的默认行为会很有用。本文向您展示了 PHP 如何“开箱即用”地处理会话数据，以及如何通过将数据存储在 MySQL 数据库中来改变这一点以满足您自己的应用程序的需求。当然，您可以选择自己喜欢的数据库或其他解决方案，如 XML、Memcache 或其他基于文件的系统。在数据被写入之前解除序列化将使您能够按照您认为合适的方式存储数据，只是要记住，您必须以序列化的形式将数据发送回来，以便 PHP 在读取数据时使用它。

图片经由[谢尔盖·米罗诺夫](http://www.shutterstock.com/gallery-121426p1.html) / [快门](http://www.shutterstock.com)

## 分享这篇文章