# 使用 iptables 保护您的服务器

> 原文：<https://www.sitepoint.com/secure-server-iptables/>

确保连接到互联网的 Linux 服务器安全的核心是拥有一个好的防火墙和特定的策略。对于那些考虑 Linux 防火墙的人来说，有很多选择，但是，一个免费的包含的解决方案是通过 Netfilter 和 iptables 提供的。

##### 状态防火墙

从 Linux 内核 2.4 和更高版本开始，Netfilter 已经被大多数(如果不是全部的话)Linux 发行商作为一个内核扩展包含进来。iptables 是它的对应物，是管理防火墙规则的工具。我简单地称之为“iptables ”,它在 Linux 桌面或服务器上创建了一个状态防火墙。“有状态”是指防火墙跟踪进出服务器和/或网络的数据包状态的能力。

这是对以前的 ipchains 的改进，以前的 ipchains 不提供数据包状态。因此，iptables 可以区分新的和现有的连接，并跟踪流量。iptables 识别四种数据包状态:新的、已建立的、相关的和无效的。

在部署 iptables 防火墙时，开发人员可以采取任意数量的路线和选项:通过像 [APF](http://www.rfxnetworks.com/apf.php) 这样的预打包解决方案，从像 [Webmin](http://www.webmin.com) 这样的控制面板中，它有一个 iptables 模块，或者通过像 [Firestarter](http://www.fs-security.com/) 这样的 GUI 配置工具。

出于本文的目的，我们将重点关注保护提供多种服务的单一开发 Web 服务器环境。这将通过在命令行文本编辑器(通常存储在/etc/sysocnfig/iptables)中手动配置 iptables 来实现。

这也意味着我们将只讨论过滤器表，它是防火墙系统中的三个表之一(其他包括 Mangle，它管理数据包流量的服务质量问题，以及 NAT(网络地址转换)表)。

##### iptables 的工作原理

iptables 基于 TCP 协议握手执行其规则。当远程设备连接到您的服务器时，会发送一个带有 SYN(同步的缩写)位的数据包，通常会通过从您的服务器发送回客户端的 SYN/ACK(同步确认)进行确认。然后，客户端通过 ACK 确认收到，网络关系就建立了。

近年来，由于 SYN flooding(用于执行拒绝服务攻击)的一些广为人知的案例，这个术语对于非网络管理员来说也变得更加熟悉。当一台(或多台)远程恶意主机反复向服务器上的多个端口发送 SYN 数据包时，就会发生这种情况，服务器会对此进行确认。但是，远程恶意主机不是发回 ACK 以打开合法连接，而是继续发送 SYN 数据包，服务器反复尝试确认它们，最终阻塞带宽和系统资源，并严重阻碍或阻塞所有其他流量。

稍后，当我们配置服务器的防火墙时，我们将查看一个选项来防止 SYN 泛滥。

##### 入门指南

只有 root 用户可以管理 iptables，因此如果用户不小心，通常的预防措施(以 root 用户身份执行操作)可能会损害服务器的健康。

iptables 可能会也可能不会在您的系统上运行。您可以通过发出`'/etc/rc.d/init.d/iptables status'`进行检查，它将列出您的防火墙规则的状态，或者返回类似“防火墙已停止”的内容。

控制 iptables 操作很简单，使用上面的命令可以选择启动、停止、状态等选项。

此时，我们假设您没有 iptables 配置，我们将从头开始构建规则文件。首先，我们需要确定您希望启用的服务(即 FTP、SSH、mail 和 HTTP)，确定需要关注的领域(如 SYN flooding ),并注意您可能试图禁止访问的任何 IP 地址。

如果您像我一样使用 Red Hat 风格的系统(开发服务器上的 Fedora Core 3)，您可能会在/etc/sysconfig/iptables 文件中找到一些通用规则。如果现有的 iptables 文件已被使用，您会想要备份它。这可以在命令行上通过`'cp /etc/sysconfig/iptables /etc/sysconfig/iptables.backup'`来完成。

要开始构建您的规则，请在您喜欢的命令行编辑器中打开该文件。我发布`'vi /etc/sysconfig/iptables'`；您将按插入键或字母 I 键开始编辑。请记住，当您完成编辑时，您需要按 ESC 键并键入':wq '来将文件保存在 Vi 中。

##### 允许目标访问

让我们建立一组防火墙规则，允许 ftp，只允许 ssh 到您指定的 IP 地址和其他服务可能需要的其他端口。我们将分解每个部分，并在进行过程中讨论细节。

一个警告:这个防火墙的例子将不会是一个真正偏执的生产机器完全硬化的概述；它旨在作为帮助您习惯使用可靠的包过滤处理基本规则的入门书。

在您喜欢的文本编辑器中打开 iptables 文件，首先设置一些基本参数。使用以下条目:

```
#My firewall config in /etc/sysconfig/iptables 

#It is good practice to comment, initial, and date your config files for the sake of shared #administrative environments and, also, so you remember what has been done in a file. 

*filter 

:OUTPUT ACCEPT [0:0] 

:FORWARD ACCEPT [0:0] 

:FWALL-INPUT - [0:0] 

:INPUT ACCEPT [0:0] 

-A INPUT -j FWALL-INPUT
```

这允许从您的服务器传出访问，允许转发以及接受所有传入流量(如果不需要通过您的服务器传递数据包，只需将`FORWARD`从`ACCEPT`更改为`DROP`)。但是，该代码会将所有传入流量转发到我们的防火墙规则(`FWALL-INPUT`)进行过滤。

由于 SSH 对于系统管理、用户访问和其他选项(例如要求客户端进行 SFTP)来说是一个关键因素，所以让我们启用这项服务。根据您管理客户端/用户的方式，您可以简单地允许所有传入的 SSH 流量:

```
#accept all incoming ssh traffic 

-A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 22 -j ACCEPT
```

或者，可以将 SSH 限制为已识别的静态 IP 地址主机:

```
#accept incoming ssh traffic from user John Doe 

-A FWALL-INPUT -p tcp -m tcp -s x.x.x.x --dport 22 -j ACCEPT 

#end specific ssh access â€“ this commenting is handy of you have multiple users here as #you can start and end sections if  users have multiple IPs from which they can access
```

通过分解这些代码行，我们可以看到:

*   `-A`将规则附加到防火墙规则集
*   `-p`代表协议(在不同情况下可以是`tcp`、`udp`和`icmp`
*   `-m`用于匹配，并为扩展数据包管理提供选项，例如对`SYN`位进行粒度控制，定义目的端口和源端口。这在“man iptables”中有更详细的解释，因为有多种级别的选项可用。
*   `-s`表示源地址，其中`0/0`代表任何主机，可以使用具体的主机 IP 地址(如上例)，也可以表示网段，如`10.0.1.0/24`。
*   `--dport`指向目的港；在 SSH 的情况下是`22`。
*   `-j`选择目标(或跳转目标)，可以是自定义目标，也可以是`ACCEPT`或`DROP`等常用内置目标之一。

接下来，让我们考虑一下 FTP，它是 Webmin 控制面板的一个入口，也是一系列常用端口，它们对服务器在互联网上的运行非常重要。

`# manage ftp port traffic
-A FWALL-INPUT-p tcp -m tcp --dport 21 -j ACCEPT
# end ftp port`

 `#我的 webmin 自定义端口
-A FWALL-INPUT-p TCP-m TCP-dport 42009-j ACCEPT
# end webmin` 

`#SNMP monitoring so I can use a remote monitoring tool
-A FWALL-INPUT-p udp -m udp --dport 161 -j ACCEPT
-A FWALL-INPUT-p udp  -m udp --sport 1023:2999 -j ACCEPT
#end SNMP`

快速安全说明:启用 SNMP 远程访问时，请确保投入时间保护您的 SNMP 配置文件，包括更改社区字符串和使用身份验证。

```
# some standard out ports with port definition 

#POP mail 

-A FWALL-INPUT-p tcp -m tcp --dport 110 -j ACCEPT  --syn 

#HTTPS 

-A FWALL-INPUT-p tcp -m tcp --dport 443 -j ACCEPT  --syn 

#SMTP Traffic 

-A FWALL-INPUT-p tcp -m tcp --dport 25 -j ACCEPT  --syn 

#HTTP 

-A FWALL-INPUT-p tcp -m tcp --dport 80 -j ACCEPT  --syn 

#In my case - Urchin 

-A FWALL-INPUT-p tcp -m tcp --dport 9999 -j ACCEPT  --syn 

#MySQL database server 

-A FWALL-INPUT-p tcp -m tcp --dport 3306 -j ACCEPT  --syn 

-A FWALL-INPUT-p udp -m udp --dport 3306 -j ACCEPT 

#IMAP mail services 

-A FWALL-INPUT-p tcp -m tcp --dport 143 -j ACCEPT  --syn 

#DNS 

-A FWALL-INPUT-p tcp -m tcp --dport 53 -j ACCEPT  --syn 

-A FWALL-INPUT-p udp -m udp --dport 53 -j ACCEPT 

-A FWALL-INPUT-p udp -m udp -s 0/0 -d 0/0 --sport 53 -j ACCEPT 

#Localhost traffic 

-A FWALL-INPUT-i lo -j ACCEPT 

#The below commits the rules to production for iptables to execute 

COMMIT
```

你会注意到我们添加了`--syn`标志。这是之前提到的 iptables 中的`-m`(用于匹配)选项的一部分。这里，我们特别确保只接受带有`SYN`位并且没有设置`ACK`的新连接。

此时，您已经有了一个工作的开发防火墙。您可以根据自己的需求添加和删除服务。但是，我们可以添加一些条目。我们可以添加一个最终规则，丢弃所有不符合前面任何端口条件的数据包。

```
#Drop all other new requests not meeting any existing rule requirements applied to traffic 

-A FWALL-INPUT -p tcp -m tcp -j REJECT  --syn 

-A FWALL-INPUT -p udp -m udp -j REJECT
```

##### SYN 洪水保护

对于一些额外的保护，我们还可以通过将请求的数量限制在 5 秒钟来防止新请求的泛滥(设置了`SYN`位但没有`ACK`的数据包，如本文前面所讨论的)，这允许系统有时间应用规则。

```
-A FWALL-INPUT â€“p tcp --syn -m limit --limit 5/second -j ACCEPT
```

这应该出现在规则的顶部，就在第一个 SSH 条目的上方。

##### 禁止访问

如果您在日志中发现了一些麻烦的主机，可以通过 iptables 禁止这些主机；然而，要小心 IP 伪装。对你想要阻止的 IP 地址做一些研究，以确保它不是合法的 SMTP 服务器，或者更糟:你的一个被欺骗的客户端。

要阻止特定主机:

```
#Block malicious system 

-A FWALL-INPUT -p tcp -m tcp -s x.x.x.x -j DROP
```

##### 检查防火墙日志

iptables 传统上将基本条目记录到`/var/log/messages`。但是，如果您想跟踪和研究流量，需要在防火墙规则中注明具体的日志记录。许多人更喜欢只记录`drop/reject`动作，因为这可以让他们看到任何潜在的恶意行为。

这可以通过如下所示的条目来处理:

```
#Option 1 logging drop/reject actions 

-A FWALL-INPUT -j LOG --log-level alert 

#Option 2 logging with a prefix for easy search/grep of log file 

-A FWALL-INPUT -j LOG --log-prefix "Dropped: "
```

最后，一个漂亮的[开源 iptables 日志分析器](http://www.gege.org/iptables/)可用了；它提供了一个类似于通常用来查看 Web 流量统计的界面。这个守护进程可以在 LAMP(特别是 PHP 和 MySQL)环境中实现，用来记录所有 iptables 动作，而不是默认的 T0。

设置和配置非常简单，只需对 iptables 文件稍加编辑就可以启动报告过程。

##### 后续步骤

iptables 的手册页内容丰富，建议在将防火墙投入生产之前花一些时间熟悉各种选项。例如，存在使用-m(匹配)选项来管理数据包状态的选项，即只允许特定服务的新的和已建立的连接。

网络上有无数与 iptables 相关的文档，但是，最好从源头开始: [Netfilter 的网站](http://www.netfilter.org)。教程的范围从基本的网络概念和包过滤到设置网络地址转换和高级连接跟踪选项。

最后，如前所述，这种防火墙并不代表全部和最终的配置。在探索 iptables 的选项时，这是一个很好的起点。基于他们所处的环境，管理员需要适应不同程度的偏执。我管理的一些系统被尽可能地锁定，而另一些系统则为测试和开发敞开了大门。

## 分享这篇文章