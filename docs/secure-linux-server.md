# 保护您的 Linux 服务器

> 原文：<https://www.sitepoint.com/secure-linux-server/>

Linux 操作系统是目前最稳定和多样化的操作系统之一。它也是世界上最受欢迎的服务器之一，这要归功于它的稳定性、流程处理和开发人员的奉献精神。不管你打算用 Linux 做什么，你可以打赌总有一种风格能满足你的特殊需求。

在开发领域，最流行和最常用的 Linux 发行版(被 Linux 极客称为“发行版”)是 [RedHat](http://www.redhat.com/) 。其他开源品种还包括 [Mandrake](http://www.mandrake.org/) 、 [Debian](http://www.debian.org/) ，或者 [SuSE](http://www.suse.com/) 。在本教程中，我们将引用 RedHat 9 发行版。

不要把你的大脑关在门外！

警告！如果您认为阅读这篇文章可以为您提供成为一名系统管理员所需的所有信息，那么请三思。本文将涵盖安全性的基础知识，但它并不是要取代常识，也不是一个合格的系统管理员所必需的交互思维。

这不是万能的！黑客和攻击者不断地改造自己，一个好的系统管理员将是多才多艺的，熟练的，必要时改造自己。我推荐以下资源来帮助您掌握与您的 Linux 服务器相关的系统管理和安全问题:

*   Linuxdocs.org—[http://www.linuxdocs.org/](http://www.linuxdocs.org/)*   证书-[http://www.cert.org/](http://www.cert.org/)

##### 装置

在您真正开始之前，您必须安装您的 Linux 发行版。因为我们正在设置服务器，所以我们将在没有 X-Windows(red hat 附带的图形用户界面)的情况下运行。如果你愿意，你可以选择安装 X，但是我们不会在本教程中使用它。

我们在这里操作的经验法则是，如果某样东西是不需要的，我们就不会启用它。采用这种方法的原因是，您安装的服务和模块越多，被利用和忽略的服务为您的机器提供入口的风险就越大。

根据您的服务器或发行版，我在这里提供的文件位置可能与您自己系统上的不一致。您可以使用以下命令来定位文件:`find / -name filename`。您还可以通过执行以下命令，使用 pico 或 vi 等工具来编辑文件:

```
# pico file
```

或者

```
# vi file
```

##### 封锁

这是保护新 Linux 机器的第一件事。可以采取许多措施来防止危险活动。

```
Changing the root Password
```

最明显和最简单的锁定方法是从一开始就更改(甚至初始设置)您的 root 密码。

每 30 天更改一次这是一个好主意，并且想出一个不容易被破解的密码也是明智的。有一些应用程序可以根据字典运行密码列表，并试图以这种方式破解密码。还有一些应用程序可以根据字典和“黑客”拼写运行密码列表。因此，使用“d0gf00d”作为密码是非常不安全的。

以 root 用户身份登录时，可以使用 passwd 命令更改密码。

##### 禁用`suid`

能够以另一个用户的身份运行一个应用程序*有时非常有价值，但也非常危险。最常见的应用是使用 suid。有了 suid，特权用户就可以像特权用户一样运行应用程序。例如，按照设计，Apache Web 服务器作为自己的用户运行，可以作为根用户执行命令。这样，普通用户就可以访问和编辑`/etc/passwd`文件。*

您可以通过执行以下命令来找出哪些文件正在使用 suid。permission 列(左侧)中带有“s”的任何内容都可以使用 suid 运行。

```
# ls -alF `find / -perm -4000` > /root/suid.txt
```

在您的服务器上，您可能会在/root/suid.txt 中看到类似这样的内容:

```
-rwsr-xr-x    1 root     root        60104 Apr  1  2002 /bin/mount* 

-rwsr-xr-x    1 root     root        35192 Apr 18  2002 /bin/ping* 

-rwsr-xr-x    1 root     root        19116 Apr  8  2002 /bin/su* 

-rwsr-xr-x    1 root     root        30664 Apr  1  2002 /bin/umount* 

-r-sr-xr-x    1 root     root       120264 Apr  9  2002 /sbin/pwdb_chkpwd* 

-r-sr-xr-x    1 root     root        16992 Apr  9  2002 /sbin/unix_chkpwd* 

-rwsr-xr-x    1 root     root        37528 Jan 17  2002 /usr/bin/at* 

-rwsr-xr-x    1 root     root        34296 Mar 27  2002 /usr/bin/chage* 

-rws--x--x    1 root     root        12072 Apr  1  2002 /usr/bin/chfn* 

-rws--x--x    1 root     root        11496 Apr  1  2002 /usr/bin/chsh* 

-rwsr-xr-x    1 root     root        21080 Apr 15  2002 /usr/bin/crontab* 

-rwsr-xr-x    1 root     root        36100 Mar 27  2002 /usr/bin/gpasswd* 

-rwsr-xr-x    1 root     root        19927 Apr 17  2002 /usr/bin/lppasswd* 

-rws--x--x    1 root     root         4764 Apr  1  2002 /usr/bin/newgrp* 

-r-s--x--x    1 root     root        15104 Mar 13  2002 /usr/bin/passwd* 

-rwsr-xr-x    1 root     root        14588 Jul 24  2001 /usr/bin/rcp* 

-rwsr-xr-x    1 root     root        10940 Jul 24  2001 /usr/bin/rlogin* 

-rwsr-xr-x    1 root     root         7932 Jul 24  2001 /usr/bin/rsh* 

-rwsr-xr-x    1 root     root       219932 Apr  4  2002 /usr/bin/ssh* 

---s--x--x    1 root     root        84680 Apr 18  2002 /usr/bin/sudo* 

-rwsr-xr-x    1 root     root        32673 Apr 18  2002 /usr/sbin/ping6* 

-r-sr-xr-x    1 root     root       451280 Apr  8  2002 /usr/sbin/sendmail.sendmail* 

-rwsr-xr-x    1 root     root        20140 Mar 14  2002 /usr/sbin/traceroute* 

-rwsr-xr-x    1 root     root        13994 Apr 18  2002 /usr/sbin/traceroute6* 

-rws--x--x    1 root     root        22388 Apr 15  2002 /usr/sbin/userhelper* 

-rwsr-xr-x    1 root     root        17461 Apr 19  2002 /usr/sbin/usernetctl*
```

许多系统管理员会建议停用不需要的 ping 和 traceroute 等服务。在这个特定的输出中，我将禁用`/usr/bin/chage`、`/usr/bin/chfn`、`/usr/bins/chsh`、`/bin/mount`、
、`/bin/umount`、`/usr/bin/gpasswd`、`/usr/sbin/usernetctl`、`/usr/sbin/traceroute`、`/usr/sbin/traceroute6`、
、`/usr/bin/newgrp`和 `/usr/sbin/ping6`、`/bin/ping`。

要禁用文件上的`suid`,使用以下命令。这使得文件只能由所有者执行，也使得它不可变(不能被修改或删除，甚至不能被链接):

```
# chmod 111 /path/to/file 

# chattr +I /path/to/file
```

记住，经验法则是，如果不需要某样东西，就禁用它！

**`/etc/securetty`** —接下来，您需要编辑/etc/securetty 文件。此脚本允许您定义哪些服务可以访问您的 TTY 设备。TTY 设备是任何基本输入/输出设备的别称，在这种情况下，该设备就是您的 Linux 控制台。

该文件包含 root 可以用来访问您的控制台的服务列表。这里最重要的是禁用 telnet(即，在该行前面使用`#`注释掉)。原因是 telnet 广播未加密的数据包。通俗地说，它通过扩音器向全世界大声喊出你的重要用户密码。显然，您不需要以这种方式广播您的 root 密码。我们的机器有下面的/etc/securetty 文件。

```
# pico /etc/securetty 

vc/1 

#vc/2 

#vc/3 

#vc/4 

#vc/5 

#vc/6 

#vc/7 

#vc/8 

#vc/9 

#vc/10 

#vc/11 

tty1 

#tty2 

#tty3 

#tty4 

#tty5 

#tty6 

#tty7 

#tty8 

#tty9 

#tty10 

#tty11
```

我们将注释掉除了`vc/1`和`tty1`之外的所有设备(在适当的行前面放置一个`#`),有效地防止除了这些单一控制台之外的 root 访问。那么访问 root 的唯一方法就是使用`su -`。

**`/etc/ftpusers`** —与禁用 telnet 传输方法一样，FTP 也不应用于根事务。顺便说一下，对于一个普通的 FTP 用户来说，找到一个 SFTP 客户端也是一个好主意。这将允许安全的 FTP 交易发生，只要主机提供商向其用户提供安全外壳访问(SSH)。但是，回到正题。

当您编辑/etc/ftpusers 时，请确保`root`不在列出的用户中。如果是，通过在行首加上一个`#`来注释掉它。

**`/etc/xinetd.conf`**—Linux 的旧版本使用/etc/inetd.conf，它的语法和用法略有不同。xinetd.conf 文件对您的网络至关重要。它会启动与您的网络连接相关的服务。从中，你可以(也应该！)禁用不运行或不需要的服务。

在我们的系统中，我们将进一步深入到/etc/xinet.d/目录，其中列出了每个默认网络互联服务的文件。在我们的系统上，这个目录包括`chargen`、`chargen-udp`、`daytime`、`daytime-udp`、`echo`、`echo-udp`、`finger`、`finger-udp`、`ntalk`、`rexec`、`rlogin`、 `rsh`、`rsync`、`servers`、`services`、`talk`、`telnet`、`time`和`time-udp`。

这些文件的内容如下所示:

```
# default: off  

# description: A daytime server. This is the tcp   

# version.  

service daytime  

{  

        type            = INTERNAL  

        id              = daytime-stream  

        socket_type     = stream  

        protocol        = tcp  

        user            = root  

        wait            = no  

        disable         = yes  

}
```

如果您不需要或不熟悉列出的任何服务，请进入该文件并将 disable 属性设置为 yes，直到您熟悉该服务的用法。确保无论何时对这些文件进行任何更改，都使用以下命令重新启动 `inet` `daemon`:

```
# /etc/rc.d/init.s/inet restart
```

##### ipchains

```
ipchains is Linux's answer to a firewall. There are a lot of neat tricks that can be performed with ipchains, and you can search for those tricks on Google. But the module itself is fairly easy to use once you get the hang of it. I hope you can stay with me on this, as it can sound a bit over-technical. PLEASE BE CAREFUL, AS YOU CAN EASILY LOCK YOURSELF OUT OF YOUR OWN BOX.

重要的是要认识到`ipchains`实际上指的是三个独立的链。典型的`ipchain`命令由几部分组成。

首先，它携带 3 个命令之一。

```

*   `-F`冲链*   `-P`设置链条的处理*   `-A`向链中添加新规则

要建立一个链，您可以使用:

```
# ipchains --F input  

# ipchains --A input REJECT
```

这是一个总括命令，基本上停止所有传入的流量。第一个命令刷新输入链，第二个命令向输入链添加拒绝所有流量的新规则。

如果你完全与网络断开，你可以做到这一点，但世界上大多数地方不是这样。世界上几乎每台 Linux 机器都连接到网络或互联网，所以使用这样一个总括命令是不现实的。

让我们看看更多可用的选项，以建立一个更智能的过滤系统。假设您的 Linux 机器是一个只能在本地局域网上访问的开发服务器。其网络设备的 IP 地址为 192.168.25.4，网络掩码为 255.255.255.0。

注意，在 Linux 上，您可以通过 ifconfig 确定源机器的网络 IP，或者在 Windows 上，在命令提示符下使用 ipconfig 命令。网络的其余部分也在 192.168.x.x 专用块上。

您可以编写如下所示的规则:

```
# ipchains --A input --I eth0  -s 192.168.1.0/255.255.255.0 --d 192.168.25.4 --j ACCEPT
```

这到底是什么意思？我们来分析一下。

*   `ipchains --A input` —如上所述，这向输入链添加了一个规则。*   `-I eth0`–这告诉防火墙我们要运行此规则的数据包流量连接到以太网设备 0 (Eth0)。*   `-s 192.168.1.0/255.255.255.0` —来源或发送 IP 地址为 192.168.1.0。斜线后面的数字表示掩码，在我们的例子中是 255.255.255.0*   `ACCEPT`表示应该允许来自该源的所有流量。您还可以使用 REJECT 来阻止流量。

ipchains 防火墙的最佳选择在于它的文档，可以在这里找到。

##### 其他技巧

为了进一步保护您的服务器，您还可以使用一些与服务器的 hosts*文件相关的技巧。

在/etc/hosts.deny 和/etc/hosts.allow 中，您可以处理称为 tcp wrappers 的项目，它只是将服务“包装”在特定的规则中。您的 hosts.allow 文件可能类似于以下内容:

```
// Allow localhost  

ALL : 127.0.0.1  

// Allow SSH Access to anyone except from 192.168.1.101  

sshd : ALL EXCEPT 192.168.1.101 : ALLOW
```

您的/etc/hosts.deny 文件可能类似于:

```
// No one can connect via anything except loopback localhost  

ALL : ALL EXCEPT 127.0.0.1:DENY
```

##### 入侵检测

你可以考虑使用类似于 [Tripwire](http://www.tripwire.org) 的包来检测入侵。它没有附带 Redhat，但是您可以获得源代码并自己编译。它的工作方式是创建并比较关键文件的哈希，以确定是否有任何更改。

一个有效的黑客不会只是闯入你的系统。他还将为自己创建一个后门，以便在其他时间获得访问权限。大多数时候，这些后门都在被利用的文件中，这是防止这种情况发生的一种方法。

##### 摘要

有安全意识的系统管理员可以使用许多其他技巧和提示。有效的关键是时刻保持警觉，准备跳出框框思考。通常有不止一种方法来剥一只猫的皮，黑客们一直在发明或发现新的方法。

请不要读这篇文章并认为这是系统安全的全部。这些提示仅仅触及了表面。快乐守护！l

## 分享这篇文章