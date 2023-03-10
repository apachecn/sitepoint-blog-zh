# 与外界沟通

> 原文：<https://www.sitepoint.com/proc-open-communicate-with-the-outside-world/>

我们可以通过许多方式从 PHP 与其他应用程序交互并共享数据；有 web 服务、消息队列系统、套接字、临时文件等。嗯，今天我想特别向大家展示一种方法，`proc_open()`。该函数生成一个新命令，但带有打开文件指针，可用于发送和接收数据以实现进程间通信(IPC)。

## 什么是烟斗？

要理解进程如何发送和接收数据，你知道管道是什么。

Unix 哲学导致早期开发者编写许多小程序，每个程序都有非常具体的功能。这些程序将纯文本作为通用格式共享，用户可以将它们“链接在一起”以获得更大的功能。一个命令的输出成为下一个命令的输入。让数据在命令之间流动的虚拟通道被称为管道。

如果您曾经在 Unix shell 中工作过，那么您很可能使用过管道，甚至可能没有意识到这一点。例如:

```
$ mysql -u dbuser -p test 
```

这里调用了`mysql`实用程序，系统建立了一个管道，通过这个管道将`mydata.sql`文件的内容输入到`mysql`中，就像你在键盘上直接输入一样。

管道有两种:[匿名](http://en.wikipedia.org/wiki/Anonymous_pipe "Anonymous pipe - Wikipedia, the free encyclopedia")和[命名](http://en.wikipedia.org/wiki/Named_pipe "Named pipe - Wikipedia, the free encyclopedia")。匿名管道是临时的，只在流程运行期间创建，一旦不再需要就销毁。上面将文件内容重定向到`mysql`的例子使用了匿名管道。另一方面，命名管道有一个名称，可以无限期地存在。它是使用特殊命令创建的，通常在文件系统中作为特殊文件出现。

不管是什么类型，任何管道的一个重要属性就是它是一个 [FIFO](http://en.wikipedia.org/wiki/FIFO "FIFO - Wikipedia, the free encyclopedia") (先进先出)结构。这意味着一个进程首先写入管道的数据是另一个进程从管道中读出的第一个数据。

## proc_open()简介

PHP 函数`proc_open()`执行命令，就像`exec()`一样，但是增加了通过管道引导输入和输出流的能力。它接受一些可选参数，但强制参数是:

*   要执行的命令。
*   描述要使用的管道的数组。
*   一个数组引用，稍后将用对管道端点的引用填充该数组引用，以便您可以发送/接收数据。

可选参数用于调整生成命令的执行环境。这里就不讨论了，不过你可以在 PHP 手册里找到[更多信息](http://php.net/proc_open "PHP: proc_open - Manual")。

除了要执行的命令之外，我认为定义管道的描述符数组是需要注意的最重要的参数。文档将其解释为“索引数组，其中键表示描述符编号，值表示 PHP 如何将描述符传递给子进程”，但这到底是什么意思呢？

表现良好的 unix 进程的三个主要数据流是 STDIN(标准输入)、STDOUT(标准输出)和 STDERR(标准错误)。也就是说，有一个用于传入数据的流，一个用于传出数据的流，还有一个用于信息性消息的传出流。传统上，STDIN 用整数 0 表示，STDOUT 用 1 表示，STDERR 用 2 表示。因此，键为 0 的定义将用于设置输入流，键为 1 的定义用于设置输出流，键为 2 的定义用于设置错误流。

这些定义本身可以采取两种形式中的一种，要么是打开的文件资源，要么是描述管道性质的数组。对于匿名管道，描述性数组的第一个元素是字符串“管道”，第二个元素是“r”、“w”或“a”，这取决于管道是被读取、写入还是被追加。对于命名管道，描述性数组保存字符串“file”、文件名，然后是“r”、“w”或“a”。

一旦被调用，`proc_open()`填充第三个参数的数组引用，以将资源返回给进程。引用中的元素可以被视为普通的文件描述符，它们与文件和流函数一起工作，如`fwrite()`、`fread()`、`stream_get_contents()`等。

当你完成与外部命令的交互时，清理干净是很重要的。您可以关闭管道(使用`fclose()`)和进程资源(使用`proc_close()`)。

根据目标命令/进程的行为，您可能需要在 STDIN 开始工作之前关闭它(这样它就知道不要再期待任何输入)。此外，您应该在关闭该进程之前关闭 STDOUT 和 STDERR 连接，否则它可能会在等待所有内容都清理干净后再关闭。

## 一个实际的例子:转换维基标记

到目前为止，我只讨论了事情是如何工作的，但是我还没有向您展示一个使用`proc_open()`来生成和与外部进程通信的例子。所以，我们来看看它有多好用。

假设我们需要将带有 wiki 标记的文本块转换成 HTML，以便在用户的浏览器中显示。我们使用 [Nyctergatis 标记引擎](http://nyctergatis.com/creole/ "nyctergatis - Nyctergatis Markup Engine") (NME)来执行转换，但是因为它是一个编译过的 C 二进制文件，所以我们需要一个在需要时启动`nme`的方法以及一个传递输入和接收输出的方法。

```
<?php
// descriptor array
$desc = array(
    0 => array('pipe', 'r'), // 0 is STDIN for process
    1 => array('pipe', 'w'), // 1 is STDOUT for process
    2 => array('file', '/tmp/error-output.txt', 'a') // 2 is STDERR for process
);

// command to invoke markup engine
$cmd = "nme --strictcreole --autourllink --body --xref";

// spawn the process
$p = proc_open($cmd, $desc, $pipes);

// send the wiki content as input to the markup engine 
// and then close the input pipe so the engine knows 
// not to expect more input and can start processing
fwrite($pipes[0], $content);
fclose($pipes[0]);

// read the output from the engine
$html = stream_get_contents($pipes[1]);

// all done! Clean up
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($p);
```

首先布置描述符数组，0 (STDIN)作为匿名管道，可由标记引擎读取，1 (STDOUT)作为匿名管道，可由引擎写入，2 (STDERR)将任何错误消息重定向到错误日志文件。

管道定义上的“r”和“w”起初可能看起来不太直观，但请记住，它们是引擎将使用的通道，因此是从它的角度配置的。我们写入读管道，因为引擎将从中读取数据。我们从写管道中读取数据，因为引擎已经将数据写入其中。

## 结论

与外部流程交互的方式有很多；考虑到你需要如何处理和处理什么，有些可能比使用`proc_open()`更好，或者`proc_open()`可能只是医生针对你的情况开出的处方。当然，您将实现有意义的东西，但是现在您将知道如果需要的话如何使用这个强大的函数！

我在 GitHub 上放了一些[示例代码](https://github.com/phpmasterdotcom/CommunicateWithTheOutsideWorld "phpmasterdotcom/CommunicateWithTheOutsideWorld - GitHub")，模拟了一个使用 NME 的基本 wiki，就像上面的例子一样。如果您有兴趣进一步玩和探索，请随意克隆它。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章