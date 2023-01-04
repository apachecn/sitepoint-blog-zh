# PHP 中的 UDP 端口扫描

> 原文：<https://www.sitepoint.com/udp-portscanning-php/>

端口扫描在系统管理/所有权中起着合法的作用。通过准确地确认计算机接受连接的端口，可以确保操作系统没有作为默认安装的一部分向外界开放不必要的端口。它还允许我们检查我们的系统是否被入侵，是否有端口被打开，以允许黑客通过互联网向我们的机器发送远程命令。

虽然对自己的端口进行 TCP 端口扫描很常见，但许多人低估了开放 UDP 端口的潜在危险，这可能会导致危害，或表明危害已经发生。正如 nmap 联机帮助页所说:

<q>“还有 cDc Back Orifice 后门程序，它隐藏在 Windows 机器上的可配置 UDP 端口上。更不用说利用 UDP 的许多常见易受攻击的服务，如 snmp、tftp、NFS 等。”</q>

普通家庭用户对他们自己的机器进行端口扫描的最大障碍是缺乏简单的软件来为他们进行扫描。不要误解我——nmap 是目前最好的端口扫描工具之一——但是普通用户使用*nix 命令行工具会有多舒服呢？在我看来，一个只需要普通用户先生访问特定的 URL 并等待 PHP 脚本对他们的机器运行 TCP 和 UDP 扫描，然后将扫描结果返回给他们的工具，正是医生所要求的。

带着这个想法，我开始在互联网上搜索，找到了一个 TCP 端口扫描器的实现，Jim Barcelona 编写了这个实现，并在 [php wizard](http://www.phpwizard.net/resources/tutorials/securing_servers.html) 上发布

太好了！完成一半的工作后，我应该很快就能写出一个 UDP 端口扫描器。我的意思是，编写 UDP 端口扫描器能有多难？事实证明，要困难得多。

##### PHP 中的 TCP 端口扫描

使用`fsockopen`功能创建 TCP 套接字时，指定远程机器的 IP 地址和想要连接的端口号。使用底层的套接字功能，PHP 将尝试在指定的端口上创建一个到远程机器的虚电路，以允许进一步的通信发生。如果目的端口不可用，那么远程机器上的 TCP 提供者将拒绝连接请求，并且`fsockopen`函数将返回一个布尔值`failed`。

所以我们现在有了一个容易理解和实现的 TCP 端口扫描器。我们建立了一个循环，指定我们希望扫描的最小和最大端口号，并在循环中，尝试在循环索引的当前值处打开一个连接。如果尝试打开端口失败，则该端口没有服务。如果我们能够成功地打开到该端口的套接字，那么在该端口有一个服务，我们将它作为一个密钥登录到我们的开放端口数组中。然后我们通过调用`getservbyport`插入该键的值，这将返回通常在该端口注册的 unix 服务。最后，我们关闭打开的套接字，并进入循环的下一次迭代。

##### PHP 中的 UDP 端口扫描

当应用程序希望使用 UDP 协议通过网络发送数据时，它通过分配的端口号将数据提供给 UDP，同时告诉 UDP 数据应该发送到目标系统上的哪个端口。然后，UDP 创建一个 UDP 消息，标记源和目的端口号，然后传递给 IP 进行传递。

当目的地机器接收到 UDP 包时，UDP 软件在包的报头中寻找目的地端口号，并将有效载荷传递给已经注册使用该端口号的任何应用程序。如果没有应用程序为指定的端口号注册，那么“ICMP 目的地不可达:端口不可达”错误消息被返回到远程机器，并且有效负载被丢弃。

在 PHP 中创建 UDP 套接字类似于创建 TCP 套接字，但有一些不同。您仍然调用`fsockopen`函数，但是您必须指定:

*   你想使用 UDP 协议，
*   远程计算机的 IP 地址和
*   您想要连接的端口号。

与 TCP 套接字相反，此时不存在到远程计算机的连接。

```
function _scanPort ($portNumber) {  

 $handle = fsockopen($this-> targetIP, $portNumber, &$errno, &$errstr, 2);  

 if (!$handle) {  

 echo "$errno : $errstr <br/>";  

 }  

 socket_set_timeout ($handle, $this-> timeout);  

 $write = fwrite($handle,"x00");  

 if (!$write) {  

 echo "error writing to port: $index.<br/>";  

 next;  

 }  

 $startTime = time();  

 $header = fread($handle, 1);  

 $endTime = time();  

 $timeDiff = $endTime - $startTime;   

 if ($timeDiff >= $this-> timeout) {  

 fclose($handle);  

 return 1;  

 } else {  

 fclose($handle);  

 return 0;  

 }  

}
```

因为从我们的 UDP 套接字读取的调用将会阻塞(等到它接收到数据)，所以我们为`set_socket_timeout` 函数设置一个值。这告诉套接字在一段特定的时间内只在套接字上侦听(阻塞)来自远程机器的响应。如果超过这个时间，那么套接字将停止侦听，代码将继续运行。我们将很快看到如何使用它来识别开放端口。

因为 UDP 是无连接的，所以此时没有与远程机器建立虚电路。相反，您必须使用`fwrite`函数通过套接字传递您想要发送到远程机器的数据。然后，我们立即记录开始监听来自远程机器的响应的时间，并使用`fread`监听套接字。此时，可能会发生以下两种情况之一:

1.  远程服务器返回“ICMP 目的地不可达:端口不可达”错误信息给我们，`fread`结束，或者

3.  套接字在等待响应时超时。

在这两种情况下，我们现在记录监听结束的时间，并通过从开始时间中减去结束时间得出监听花费的总时间。我们将这个数字与我们设置的套接字超时值进行比较。如果它小于超时值，那么远程服务器返回一个“ICMP 目的地不可达:端口不可达”的错误消息给我们，我们知道端口是关闭的。如果套接字超时，那么我们知道发生了以下两种情况之一:该端口的应用程序正在等待接收有效命令，或者数据包在传输过程中丢失(UDP 不提供有保证的传递，如果数据包在途中丢失，我们将不会收到任何相关信息)。

因此，我们建立了一种方法来辨别港口是否有任何东西(即套接字在超时前返回)，我们现在需要一种方法来判断套接字超时是应用程序等待合法数据的结果，还是数据包在传输过程中丢失了。最简单的方法是向该端口发送多个数据包，如果我们收到一个没有超时的响应，那么我们就知道那里没有应用程序，端口关闭了。但是，我们如何才能做到这一点，而不浪费太多的带宽，并尽量减少应用程序的运行时间？

在对该类被指示扫描的 UDP 端口进行全面扫描之前，我们将在端口范围内非常高的位置进行一次较小的端口扫描，以最大限度地减少合法开放端口的发现，并测试机器之间的网络条件。这种初始扫描是用我们的 `_networkProbe`方法进行的。由于大多数开放的 UDP 端口都位于或低于端口 1024，我们将在端口 55000 范围内向上扫描。在我们可以假设的端口范围内，任何超时如此之高的端口都是因为丢失了数据报，而不是因为我们检测到了开放的端口。

```
function _networkProbe ($noTrials=100, $startPortNumber=55000) {   

 $endPortNumber = $startPortNumber + $noTrials;  

 // temporarily set timeout to 2 seconds. we'll modify this with the  

 // data that we get from this method  

 $this-> timeout = 2;  

 // setup a for loop to scan the ports   

 for ($portNumber = $startPortNumber; $portNumber < $endPortNumber;   

  $portNumber++) {  

 $startTime = $this-> _getmicrotime();  

 $result = $this-> _scanPort($portNumber);  

 $endTime = $this-> _getmicrotime();  

 $timeDiff = $endTime - $startTime;  

 if (!$result) {  

 $responsesArray[] = $timeDiff;  

 $totalTime += $timeDiff;  

 }  

 }  

 $noResponses = count($responsesArray);  

 // if more than 40% of the datagrams timed out, abort the scan  

 if ($noResponses < (.6 * $noTrial)) {  

 echo "The connection is losing too many packets. Scan aborted. <br/>";  

 exit;  

 }  

 $averageResponseTime = $this-> _calcAvgResponseTime ($noResponses,   

  $totalTime);   

 $standardDeviation = $this-> _calcStdrDeviation ($responsesArray);  

  // calculate the timeout value  

 $timeoutValue = ceil($averageResponseTime + 4 * $standardDeviation);   

 // calculate number of cleanup iterations we'll need   

 // percentFalsePositive is the % of datagrams that we sent in   

 // the trial that timed out  

 $percentFalsePositives = ($noTrials - $noResponses)/$noTrials;   

 // percentResponses is the % of datagrams that we sent in the trial   

 // that returned (eg -- didn't timeout)  

 $percentResponses = $noResponses/$noTrials;  

 // calculate the total number of ports to be scanned in the   

 // real scan  

 $portRange = $this-> maxPort - $this-> minPort + 1;  

 // estFalsePositives is the estimated number of false positives we   

 // anticipate getting from the real scan  

 $estFalsePositives = $portRange * $percentFalsePositives;  

 $this-> cleanupIterations = $this->   

_calcNoIterations ($estFalsePositives, $percentResponses, $portRange);  

 if ($this-> debug == 1) {  

 echo "<br/>";  

 echo "total time $totalTime<br/>";  

 echo "timeout value: " . $this-> timeout . "<br/>";  

 echo "cleanup iterations: " . $this-> cleanupIterations . "<br/>";  

 echo "<br/>";  

 flush();  

 }  

}
```

所以我们现在知道:

*   我们样本中丢失的数据包数量，
*   已发送的数据包总数
*   我们必须侦听每个无阻塞响应的数据包的平均时间。

根据样本中丢失的数据包数量、初始扫描中发送的数据包总数以及主扫描中将要扫描的端口数量范围，我们可以计算必须运行的迭代次数，并重新测试检测到的开放端口，以消除误报。我们使用的公式是指数衰减对数，它的功能性可以在该类的`_calcNoIterations`方法中找到。

我们还将使用返回“ICMP Destination Unreachable:Port Unreachable”错误消息并且没有阻塞的`fread` 调用的平均响应时间，来计算这些单独时间的标准偏差。我们将标准偏差乘以系数 4(4 sigma ),并将其添加到我们的平均响应时间中。这允许我们最小化超时值，并且仍然合理地确定我们没有消除太多的扫描，如果它没有超时，这些扫描将会返回。在这一点上，这种检查实际上是多余的，因为`set_socket_timeout`的值不能设置为小于一秒的值，而这正是大多数导出的超时值的位置。但是，如果套接字超时值被修改为接受小于一秒的值，我们可以预计在消除没有服务的端口时，运行时间会减少五分之一。

##### 使用 UDP 端口扫描类

UDP 端口扫描类在抽象 UDP 端口扫描的复杂性方面做得非常好。使用该类本身很简单:包含该类，并根据向其传递目标 ip 地址的类创建一个对象，还可以选择开始扫描的端口、结束扫描的端口，以及该对象在进行扫描时是否应该输出信息。默认情况下，起始端口设置为 1，结束端口设置为 1024，输出为开。

然后调用 objects `doScan`方法，并将其输出赋给一个变量，该变量将把扫描结果保存为一个数组。这里有一个例子:

```
include ('classes/udpPortScanner.inc');   

$udpScanner = new udpPortScanner("$REMOTE_ADDR");   

$ports = $udpScanner-> doScan();   

if (count($ports) == 0) {   

 echo "no open udp ports detected.<br/>";   

} else {   

echo "open udp ports:<br/>";   

 foreach ($ports as $portNumber => $service) {   

 echo "$portNumber ($service)<br/>";   

 }   

}
```

##### 关闭

我必须做两处修改，还有一处我想做。首先，如果网络条件很好，消除误报的迭代次数的计算值会导致一个过低的值，并且我得到的返回端口实际上并没有打开。为了弥补这一点，我修改了代码，将最小清理迭代次数提高到 5 次。

UDP 端口扫描器严重依赖于两台机器之间的网络条件。在测试中，我在澳大利亚和南非(我在美国)的朋友自愿接受扫描，扫描仪很难检测到关闭的端口。这是因为网络条件导致大量数据包在传输过程中丢失，并且遇到了很长的运行时间。为了防御该扫描器，对位于南非的主机上的前 1024 个 UDP 端口的 nmap 扫描花费了近一个小时，这表明这是 UDP 端口扫描特有的问题，而不是该扫描器实现特有的问题。考虑到这一点，显然有些网络连接太差，无法在合理的时间内完成扫描。因此，我修改了代码，以便在初始网络测试扫描期间，如果超过 40%的数据包丢失，就中止扫描。您可以随意删除或修改该值，但要注意的是，扫描一个不良的网络跨度可能需要相当长的时间。

最后，我还想收集关于微软将其各种服务放在哪些端口上的信息，以补充`getservbyport`函数，该函数将只返回映射到*nix box 上的传统端口的服务。然后，我可以指定哪个微软服务在端口上运行，在以后的版本中，指出这是如何压缩的，并描述禁用该服务和关闭端口(如果它不在使用中)的方法。

我要感谢来自 suddendecelaration.com 的 David LaCroix，他帮助我建立了 tcpdump 来分析我对他的一台自告奋勇的机器进行的 UDP 端口扫描。他的帮助有助于完成这个软件的调试。此外，感谢 waferbaby.com[的丹尼尔·博根](http://www.waferbaby.com)和 carfolio.com[的扎克·麦格雷戈](http://www.carfolio.com)，他们两人都帮助确定了跨洋 UDP 端口扫描并不是一个好主意。

有关互联网协议的更多信息，我强烈推荐 O'Reilly 和 Associates 的“互联网核心协议:最终指南”，作者为 Erik A. Hall。您可以在 [O'Reilly 的网站](http://www.oreilly.com/catalog/coreprot/)上找到更多信息。

tcpPortScan 和 udpPortScan 类的官方主页是这里的。您可以在那里查看课程的最新更新，并报告您的任何问题或建议。

## 分享这篇文章