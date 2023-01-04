# Ubuntu 12.04 LTS 精确穿山甲:构建防火墙

> 原文：<https://www.sitepoint.com/ubuntu-12-04-lts-precise-pangolin-building-a-firewall/>

Ubuntu 的默认防火墙配置工具被称为“UFW”。开发来简化 iptables 防火墙配置，UFW 提供了一种用户友好的方式来创建基于 IPv4 或 IPv6 主机的防火墙，这将有助于保护您的计算机免受未经授权的访问，在这篇文章中，我将向您展示如何设置，配置和管理您在 Ubuntu 12.04 LTS 精确穿山甲的安全需求。

所以让我们开始吧…

## 移除 AppArmor

Ubuntu 12.04 预装了 AppArmor。一个内核级子系统，它通过为您想要保护的每个应用程序和服务实施唯一的策略配置文件来工作，并且为了降低攻击风险，除非您专门定义了一个配置文件来标识一系列功能和/或文件系统访问权限，否则对应用程序或进程的所有形式的访问都会被拒绝。因此，如果有人发现了通过 Apache 或 MySQL(甚至是基于 web 浏览器或聊天的应用程序)注入或启动恶意代码的方法，如果有问题的应用程序或服务受到不允许任何“代码执行权限”的 AppArmor 配置文件的保护，则该利用很可能会失败或不起作用。

> 根据您的情况，您应该认为这一步是可选的。显然，它在许多方面与 SELinux 没有什么不同(在大多数基于 RedHat 的系统上都可以找到),如果你真的打算使用它或探索它的潜力，我会跳过这一步，通过阅读 https://help.ubuntu.com/12.04/serverguide/apparmor.html 的服务器指南开始你的研究

Apparmor 无疑是一个非常有效和安全的解决方案，是的，如果您还没有在您的台式机和服务器上启用 AppArmor，那么您可能会错过一个非常强大的工具，但是在许多情况下，它也可能被证明是过于复杂的，如果不是非常耗时的话，出于本文的目的，我们不会使用它。

因此，我将借此机会向您展示如何禁用和删除 AppArmor，以便我们能够专注于防火墙安全的传统方法。

授予自己“root”权限，如下所示:

```
sudo su
```

以正常方式授权自己，然后像这样删除它:

```
/etc/init.d/apparmor stop
update-rc.d -f apparmor remove
apt-get remove apparmor apparmor-utils
```

最后，重新启动计算机以确保相关更改生效:

```
reboot
```

## 你好 IPTables

对于 Linux 内核和包含的 Netfilter 子系统来说，没有比简单地“看引擎盖下”更好的介绍了。在终端或控制台中，键入:

```
sudo /sbin/iptables -L
```

响应将类似于以下内容:

```
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

这清楚地显示了默认和“空规则集”。

iptables 的目的是控制数据包到达电脑的方式。每个数据包都将通过 Netfilter 子系统，根据通过 iptables 程序提供给它的规则进行接受、处理或拒绝，因此 iptables 是管理防火墙所需的全部。

在这个阶段，这一切看起来和听起来都非常复杂，但是不要担心这是 Ubuntu，Canonical 确实使这变得非常简单…

## 那么 UFW 呢

如果你读过我以前的文章，你会注意到我提到了 UFW。

也称为无冲突防火墙，它不是(也不应该被认为是防火墙)，但它的目的是提供一种“人性化”的方法来管理您的 IPTables，这些 IPTables 用于阻止、过滤、操纵或重定向网络流量。

因此，让我们确保它已安装，对于桌面版本的用户，安装还提供了对名为 gUFW 的图形伴侣的访问。

在终端类型中:

```
sudo apt-get install ufw gufw
```

在控制台类型中:

```
sudo apt-get install ufw gufw
```

## UFW 入门

要启用防火墙，请使用:

```
sudo ufw enable
```

要随时禁用防火墙，请使用:

```
sudo ufw disable
```

要启用防火墙“日志”，请使用:

```
sudo ufw logging on
```

要随时禁用“记录”，请使用:

```
sudo ufw logging off
```

> 所有日志文件都可以在/var/log 中找到

要设置默认策略，请使用:

```
sudo ufw default allow
```

要删除默认策略，请使用:

```
sudo ufw default deny
```

> 建议的方法是拒绝对所有端口/服务的访问，并慢慢打开您需要的端口/服务。记住，通过设置默认策略，您将暴露您的整个系统；因此，最好从关闭所有端口/服务开始，并在需要时打开它们。

要检查防火墙的状态，请使用:

```
sudo ufw status
```

或者

```
sudo ufw status verbose
```

到目前为止很简单…如果你正在寻找图形工具，只需打开“破折号”并搜索 GUFW。

## 管理 UFW

在接下来的部分中，我将通过大量的例子来“带您浏览”端口的管理。

### 允许和拒绝

对于那些希望允许访问任何特定端口的用户:

```
sudo ufw allow port_number
```

同样，要拒绝对任何特定端口的访问，请使用:

```
sudo ufw deny port_number
```

请记住用特定的端口号替换“端口号”…

### 下面是一些“基于端口”的示例:

“允许”访问端口 53

```
sudo ufw allow 53
```

删除对端口 53 的“允许”访问

```
sudo ufw delete allow 53
```

“允许”访问端口 80

```
sudo ufw allow 80/tcp
```

删除对端口 80 的“允许”访问

```
sudo ufw delete allow 80/tcp
```

## 用 UFW 管理服务

现在，让我们转向更简单的服务管理流程。

### 允许和拒绝

要允许访问任何特定端口，请使用:

```
sudo ufw allow service_name
```

同样，要拒绝对任何特定端口的访问，请使用:

```
sudo ufw deny service_name
```

请记住用特定的服务名替换“service_name ”,但是如果您不知道您的服务叫什么，您总是可以通过简单地键入:

```
less /etc/services
```

### 一些“基于服务”的例子的时间到了:

“允许”访问端口 smtp

```
sudo ufw allow smtp
```

拒绝对端口 smtp 的访问

```
sudo ufw deny smtp
```

删除对端口 smtp 的“允许”访问

```
sudo ufw delete allow smtp
```

删除“拒绝”对端口 smtp 的访问

```
sudo ufw delete deny smtp
```

“允许”访问端口 ssh

```
sudo ufw allow ssh
```

拒绝对端口 ssh 的访问

```
sudo ufw deny ssh
```

删除对端口 ssh 的“允许”访问

```
sudo ufw delete allow ssh
```

删除“拒绝”对端口 ssh 的访问

```
sudo ufw delete deny ssh
```

我希望你现在能明白这有多简单…

## 混合了高级语法

对于那些希望使用一系列更复杂的规则集的人来说，语法会稍有变化，但过程是相同的:

为了允许由特定 IP 地址使用，

```
sudo ufw allow from XXX.XXX.XXX.XXX
```

为了允许特定的子网，我们调用网络掩码并使用

```
sudo ufw allow from XXX.XXX.XXX.XXX/XX
```

最后，通过您可以使用的特定端口和 IP 地址，

```
sudo ufw allow from XXX.XXX.XXX.XXX to AAA port YY
```

或者，您可以使用“deny”命令，并通过使用与上面显示的过程不太相似的过程来阻止访问。
阻止由特定 IP 地址使用、

```
sudo ufw deny from XXX.XXX.XXX.XXX
```

要按特定子网进行阻止，我们调用网络掩码并使用

```
sudo ufw deny from XXX.XXX.XXX.XXX/XX
```

最后，要通过您可以使用的特定端口和 IP 地址进行阻止，

```
sudo ufw deny from XXX.XXX.XXX.XXX to AAA port YY
```

哪里 XXX。XXX.XXX.XXX 是特定的 IP 地址，AAA 是特定的协议，YY 是特定的端口号。

例如:
要允许 ip 地址 192.168.1.14 访问所有协议的端口 53，您需要键入:

```
sudo ufw allow from 192.168.1.14 to any port 53
```

或者，要允许 ip 地址 192.168.1.32 访问所有协议的端口 22，您需要键入:

```
sudo ufw allow from 192.168.1.32 to any port 22
```

> 协议可以是 TCP、UDP 或两者都是(任何)

## 提醒一句

当试图阻止对特定 IP 地址的访问时，您应该知道规则应该遵循一组逻辑顺序。

从理论上讲，这意味着如果第一条规则提供了对特定端口或服务的完全访问权限，那么以后阻止该用户的任何尝试都将被忽略。因此在实践中，与其简单地删除所有规则并重新排序，不如打开源文件并包含一个新的部分，如下所示:

授予自己“root”权限，如下所示:

```
sudo su/pre>
And then:
For Terminal users use,
```

```
gedit /etc/ufw/before.rules
```

对于控制台用户(用您喜欢的文本编辑器替换“nano ”)来说，

```
nano /etc/ufw/before.rules
```

在“before.rules”中查找如下所示的行:

```
# drop INVALID packets (logs these in loglevel medium and higher)
-A ufw-before-input -m state --state INVALID -j ufw-logging-deny
-A ufw-before-input -m state --state INVALID -j DROP
```

然后直接添加您的“删除”规则，如下所示:

```
# drop INVALID packets (logs these in loglevel medium and higher)
-A ufw-before-input -m state --state INVALID -j ufw-logging-deny
-A ufw-before-input -m state --state INVALID -j DROP
MY FIRST DROP RULE GOES HERE
MY SECOND DROP RULE GOES HERE
MY THIRD DROP RULE GOES HERE
```

仅此而已。很快你就会运行一个非常安全的环境。

所以直到下次…
我希望你继续享受使用 Ubuntu 12.04 LTS 精确穿山甲。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Ubuntu Linux](https://learnable.com/courses/ubuntu-linux-65) 。

## 分享这篇文章