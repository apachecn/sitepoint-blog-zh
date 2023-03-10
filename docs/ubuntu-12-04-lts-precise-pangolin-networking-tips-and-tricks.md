# Ubuntu 12.04 LTS 精确穿山甲:网络技巧和窍门

> 原文：<https://www.sitepoint.com/ubuntu-12-04-lts-precise-pangolin-networking-tips-and-tricks/>

网络通常被认为是复杂的和非常难以管理的，但是因为它在你的计算机的日常使用中形成了一个重要的角色，这篇文章的目的是通过向你展示控制 Ubuntu 12.04，LTS 精确穿山甲是多么容易，来揭示一些有助于改善你的计算机连接和整体性能的“提示和技巧”。

所以让我们开始吧…

## 我的本地 IP 地址是什么

这可能是一个简单的问题，但在一个复杂的世界里，简单的问题往往会被忽略。

### 使用图形工具:

右键单击 Ubuntu 桌面顶部面板中的“网络图标”,然后选择“连接信息”,如下所示:

![](img/5fce2ba10772dc63b228156399cc7373.png)

随后出现的对话框将提供关于当前设置的反馈。

### 使用命令行界面:

另一方面，对于那些开始享受终端功能的人，或者那些使用服务器(通过控制台或 Putty 或类似工具)的人。

如果您有基于无线的连接，请运行:

```
iwconfig
```

如果您有“有线”连接，请运行

```
ifconfig
```

“ifconfig”的结果如下所示:

```
eth0      Link encap:Ethernet  HWaddr 00:1d:92:65:09:e1
          inet addr:192.168.1.100  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::21d:92ff:fe65:9e1/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:510 errors:0 dropped:0 overruns:0 frame:0
          TX packets:315 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:80353 (80.3 KB)  TX bytes:38731 (38.7 KB)
          Interrupt:42 Base address:0xa000 

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:108 errors:0 dropped:0 overruns:0 frame:0
          TX packets:108 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:8176 (8.1 KB)  TX bytes:8176 (8.1 KB)
```

因为命令行为我们提供了额外的机会，如果您想快速识别计算机上所有可用的以太网设备，您可以运行

```
ifconfig -a | grep eth
```

结果将如下所示:

```
eth0      Link encap:Ethernet  HWaddr 00:1d:92:65:09:e1
```

很好，但在我们结束之前，我想借此机会向您展示另一个有用的命令，它可以帮助您识别系统可用的所有网络接口。这个工具被称为“lshw”命令，它不仅会详细说明您的以太网设备，还会提供关于您的硬件“其他”功能的大量信息。

要使用这个工具，只需运行下面的命令并等待几秒钟，Ubuntu 就会查询您的设备:

```
sudo lshw -class network
```

结果看起来会像这样:

```
  *-network
       description: Ethernet interface
       product: RTL8111/8168B PCI Express Gigabit Ethernet controller
       vendor: Realtek Semiconductor Co., Ltd.
       physical id: 0
       bus info: pci@0000:02:00.0
       logical name: eth0
       version: 01
       serial: 00:1d:92:65:09:e1
       size: 100Mbit/s
       capacity: 1Gbit/s
       width: 64 bits
       clock: 33MHz
       capabilities: pm vpd msi pciexpress bus_master cap_list rom ethernet physical tp mii 10bt 10bt-fd 100bt 100bt-fd 1000bt 1000bt-fd autonegotiation
       configuration: autonegotiation=on broadcast=yes driver=r8169 driverversion=2.3LK-NAPI duplex=full firmware=N/A ip=192.168.1.160 latency=0 link=yes multicast=yes port=MII speed=100Mbit/s
       resources: irq:43 ioport:d800(size=256) memory:feaff000-feafffff memory:feac0000-feadffff
```

有时候你只是“不得不”喜欢细节，但现在我们知道了一些关于我们的计算机的事情，我们可以开始控制:-)

## 如何使用网络管理器创建静态 IP 地址

以下说明向您展示了如何使用网络管理器创建固定(或静态)IP 地址。

这种方法最适合可能需要保留网络管理器功能的所有台式机用户，或者那些使用上网本、笔记本电脑和其他无线连接的用户，这些用户将来可能需要基于 DHCP 的选项。别担心，一切都很简单:

单击顶部面板上的网络菜单(向上/向下箭头图标)并选择“编辑连接”。

或者，您可以选择系统设置>网络>从左侧面板选择您的“网络连接”，然后选择“选项”。

![](img/6b842cdb215f5d8fbb9e4449a3b4dacc.png)

现在选择相关的连接并单击 edit。

例如，如果您使用“有线连接”，请选择“有线”选项卡来查找您的连接。

![](img/26c539d6027be91070c68c907d9b1d25.png)

在出现的对话框中，您应该:

*   选择“IPv4 设置”选项卡，并将“方法”更改为“手动”。
*   单击添加，用您的 IP 地址、网络掩码和默认网关填写每个字段。每次输入完地址后，请务必按下“输入/返回”键，否则对话框将无法接受您的新设置，并且“保存”按钮将保持不活动/灰色。
*   包括 DNS 服务器的 IP 地址。多个地址应该用逗号分隔。
*   单击保存并关闭剩余的对话框。

这就对了…

如果一切正常，您应该能够查看网站和/或查看您的本地网络，但在某些情况下，根据您的网络拓扑结构，可能需要重新启动。

要确认您的新设置，只需检查您的连接设置，如前所示(见上文)

但是如果我又需要 DHCP 呢？
如果您需要返回到动态分配的 IP 地址，只需重新运行前面的步骤。找到您当前的连接，选择“IPv4 设置”选项卡，并将“方法”更改为“自动(DHCP)”。要完成此反转，请单击保存并重新启动(取决于网络条件和 DHCP 服务器的配置)。

## 禁用网络管理器，并对静态 IP 地址进行“硬编码”

本解决方案解释了如何在不使用网络管理器的情况下创建固定(或静态)IP 地址。

网络管理器是一个非常好的功能，但只适合需要动态分配连接或需要无线连接的个人。是的，可以说这两种方式在全世界的家庭和办公室中都非常流行，但是对于我们这些使用和喜欢使用网络管理器的“有线连接”的人来说，这种好处是以牺牲性能为代价的。因此，这种解决方案使用传统的方法来“硬编码”您的网络连接，它最适合那些要求最高性能或绝对稳定性的个人。

> 通过使用这种方法，您应该注意到，我们不会删除网络管理器，而只是禁用它，目的是通过绕过或避开该设备来利用性能收益。这些性能提升不仅体现在网络速度上，还体现在计算机的整体性能上。

同样，不要担心，禁用网络管理器的过程非常简单，但需要我们在终端中运行各种“命令”来完成一些步骤。

因此，让我们从备份原始文件开始。

在终端类型中:

```
sudo cp /etc/NetworkManager/NetworkManager.conf /etc/NetworkManager/NetworkManager.conf.bak
```

> 这将用于将我们的原始文件备份到“/etc/network manager/network manager . conf . bak”。希望你不会需要它，但如果你需要恢复原始文件，只需打开终端，像这样反转命令:“sudo CP/etc/network manager/network manager . conf . bak/etc/network manager/network manager . conf”并重新启动。

并且确信我们已经完全备份，我们现在将继续禁用网络管理器。

在终端中，键入:

```
sudo gedit /etc/NetworkManager/NetworkManager.conf
```

这将在我们的文本编辑器中打开“NetworkManager.conf”文件。

现在改变:

托管=假

到

托管=真

所以看起来是这样的:

![](img/a4a9f937d1d277994aa4f9d583b28b75.png)

完成后，保存文件并关闭它。

> 您可以随时重新启用网络管理器，只需颠倒上述步骤或恢复备份文件即可。记住重新启动以应用这些更改。

现在我们已经成功禁用了网络管理器，我们可以通过对“接口”文件进行一些直接更改来创建静态 IP 地址；但是出于安全考虑，让我们从备份原始文件开始。

在端子和类型中:

```
sudo cp /etc/network/interfaces /etc/network/interfaces.bak
```

> 这将把我们的原始文件备份到/etc/network/interfaces.bak。同样，我们并不需要它，但是如果您需要恢复它，请打开终端，然后像这样反转命令:sudo CP/etc/network/interfaces . bak/etc/network/interfaces

现在让我们添加我们的静态 IP 地址。

在终端类型中:

```
sudo gedit /etc/network/interfaces
```

以常规方式验证您的身份，然后替换文件内容，如下所示，其中 xxx 代表与您的需求相关的值:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
	address 	xxx.xxx.xxx.xxx
	netmask 	xxx.xxx.xxx.xxx
	network 	xxx.xxx.xxx.xxx
	broadcast 	xxx.xxx.xxx.xxx
	gateway 	xxx.xxx.xxx.xxx
```

为了帮助您，这里有一个解释相关值的示例:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
	address 	192.168.1.100 	(this is my computers IP address)
	netmask 	255.255.255.0 	(this is my subnet mask)
	network 	192.168.1.0	(this is my network base address)
	broadcast 	192.168.1.255	(this is my broadcast address)
	gateway 	192.168.1.254	(this is my gateway/router's ip address)
```

完成后，只需保存并关闭文件，然后重启计算机来完成这些步骤。

此时，我们将重新启动计算机，以便“解除”网络管理器，一旦完成，您可以随时通过对“interfaces”文件(如上所示)进行必要的更改并使用以下命令重新启动网络服务来重新配置您的 IP 地址:“sudo/etc/init . d/networking restart”。

重新启动计算机后，您会发现(视觉上)什么都没有改变，但结果是，您应该会体验到“更”响应的计算机和网络连接。

> 如果您想重新启用网络管理器。只需颠倒上述步骤和/或恢复您的备份文件并重新启动，就像我们使用“cp”或 copy 命令创建它们一样，除非您删除它们，否则您的原始备份文件将一直保留，以供将来参考。

## 创建虚拟网络适配器(向单个以太网卡添加多个 IP 地址)

在某些情况下，这可能非常有用，作为对我们之前的“禁用网络管理器并硬编码静态 IP 地址”解决方案的扩展，我现在将向您展示如何为同一个以太网卡分配多个 IP 地址:

在终端类型中:

```
sudo gedit /etc/network/interfaces
```

验证您自己的身份，然后像这样替换文件内容，其中 xxx 代表与您的需求相关的值:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 static
	address 	xxx.xxx.xxx.xxx
	netmask 	xxx.xxx.xxx.xxx
	network 	xxx.xxx.xxx.xxx
	broadcast 	xxx.xxx.xxx.xxx
	gateway 	xxx.xxx.xxx.xxx
auto eth0:0
iface eth0:0 static
	address 	xxx.xxx.xxx.xxx
	netmask 	xxx.xxx.xxx.xxx
	gateway 	xxx.xxx.xxx.xxx
```

为了尽可能简单，这里有一个例子:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 static
	address 	192.168.1.100
	netmask 	255.255.255.0
	network 	192.168.1.0
	broadcast 	192.168.1.255
	gateway 	192.168.1.254
auto eth0:0
iface eth0:0 static
	address 	192.168.1.101
	netmask 	255.255.255.0
	gateway 	192.168.1.254
```

通过这样做，我将为我的单个以太网卡添加两个 IP 地址。它不仅具有 IP 地址 192.168.1.100，还具有备用地址 192.168.1.101(作为虚拟实例)。

诀窍是遵循虚拟命名方案(即 eth0:0、eth0:1、eth0:2 等)，并确保每个实例维护一个唯一的 IP 地址。这样，您可以根据需要创建任意多个 IP 地址的“实例”。

为了说明这一点，让我们在示例中添加第三个 IP 地址:

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 static
	address 	192.168.1.100
	netmask 	255.255.255.0
	network 	192.168.1.0
	broadcast 	192.168.1.255
	gateway 	192.168.1.254
auto eth0:0
iface eth0:0 static
	address 	192.168.1.101
	netmask 	255.255.255.0
	gateway 	192.168.1.254
iface eth0:1 static
	address 	192.168.1.102
	netmask 	255.255.255.0
	gateway 	192.168.1.254
```

完成后，记得重新启动电脑以应用更改，或者打开“终端”并键入:

```
sudo /etc/init.d/networking restart
```

然后，您可以运行“ifconfig”来查看新设置。

玩得开心..！

## 更改您电脑的主机名

您可能喜欢您当前的计算机名称，或者您可能想要更改它。

在这个简短的食谱中，我将向你展示如何相对容易地查看和更改你的计算机名…

在终端类型中:

```
sudo gedit /etc/hostname
```

该文件可能如下所示:

```
ubuntu-computername
```

要更改计算机的名称，只需删除现有内容并根据需要进行替换。

请记住，实际名称可以是您想要的任何名称，只要您记住一些基本原则:

*   台式机通常使用单一名称(如上所示),而所有服务器或“充当服务器的台式机”应采用 computer name . computer domain . suffix 的格式
*   在本地网络上，计算机应避免使用标准或典型的基于互联网的后缀或 TLD，除非您的本地 DNS 可以解决此类相似性。
*   使用“internet 友好”的名称，不要使用网络上已经存在的计算机名称。
*   不要使用空格或非互联网友好字符。

一旦完成，只需选择“保存”，“关闭”文件，并重新启动您的计算机，以查看更改。

您的新计算机名称将出现在登录屏幕的左上方，但如果您错过了这一点，或者需要随时确认您的当前主机名或新主机名。

打开终端并键入:

```
sudo hostname
```

结果应该反映出您所做的更改…

## 管理主机文件

hosts 文件包含一系列 IP 地址和相应的主机名。大多数用户可能从来不需要接触他们计算机的这个区域，但是有时你可能需要在你的机器上编辑 hosts 文件。这可能是因为许多原因(例如，逆转恶意活动的影响)，也可能是因为您试图让您的生活变得更快，购买“硬编码”内部或外部网站的地址。同样，如果您的网络包含其 IP 地址未在现有 DNS 记录中列出的计算机，则建议您将它们添加到 hosts 文件中。考虑到这一点，我现在将向您展示如何管理您的主机文件。

在开始之前，您可以通过在终端中键入以下命令来备份该文件:

```
sudo cp /etc/hosts /etc/hosts.bak
```

现在，要继续并定制您的 hosts 文件，只需返回命令行并键入:

```
sudo gedit /etc/hosts
```

典型的桌面文件如下所示(只需将示例值替换为与您的计算机相关的值):

```
127.0.0.1			localhost
127.0.1.1			ubuntu-computername

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

然而，话虽如此，但为了记录在案，同一文件的“服务器”或“桌面服务器”版本应该更像这样:

```
127.0.0.1       localhost.localdomain   localhost
192.168.1.100 	ubuntu-computername.ubuntu-domainname.lan  ubuntu-computername

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

你会注意到我的例子包括 IPv6 指令，这完全取决于你的判断，因为这取决于你的网络是否支持它。

例如，如果您的计算机不使用 IPv6，您可以忽略这些值(不包括它们或通过在行首放置一个“哈希”字符来注释掉它们)。

```
# The following lines are desirable for IPv6 capable hosts
#::1     ip6-localhost ip6-loopback
#fe00::0 ip6-localnet
#ff00::0 ip6-mcastprefix
#ff02::1 ip6-allnodes
#ff02::2 ip6-allrouters
```

当然，禁用 IPv6 是可选的，有时可以提高性能，但如果你有疑问，就忽略它们(但正如他们有时在“老 blighty”中所说的那样——“不要修复没有坏的东西”)。

所以回到正轨…

在您的 hosts 文件中，您可能希望包括一个预先确定的服务器和工作站列表，以便加快名称解析，如下所示:

```
192.168.1.200		servername1 www servername1.localdomain.lan
192.168.1.200		servername2 mail servername2.localdomain.lan
```

> 在我的示例中，除了实际名称之外，我还包括了一个别名，即服务器名 1 也称为 www，服务器名 2 也称为 mail。你不需要这样做，因为我的其他例子将显示。

Windows(和 Mac)用户应该注意到，这个过程非常类似于在任何基于 Windows/Mac 的操作系统上定制“hosts”文件，生成的文件可能如下所示:

```
127.0.0.1			localhost
127.0.1.1			ubuntu-computername

# PUT YOU COMMENT AFTER A HASH (#) FOR REFERENCE - NOT REQUIRED BUT USEFUL
192.168.1.200		www.website1.com
192.168.1.201		www.website2.com

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

因此，我的计算机现在将使用 www.website1.com 的 IP 地址，而不是搜索 DNS 记录，从而确保我能够比以前更快地查看有问题的网站。

更进一步…给它一点扭转。

对于那些希望行使“家长权力”的人来说，你可以很容易地利用这一点来阻止对一些“不想要的网站”的直接访问，方法是将希望去 www.website1.com 的人发送到 www.website2.com，就像这样:

```
#192.168.1.200	www.website1.com
192.168.1.201		www.website1.com
192.168.1.201		www.website2.com
192.168.1.202		www.website3.com
192.168.1.123		www.website4.com
192.168.1.167		www.website5.com
```

请注意，我已经用哈希(#)注释掉了网站 1 的真实地址，网站 1 和网站 2 现在有了相同的 IP 地址…(IP 地址是网站 2 的实际地址)

无论如何，这都不是一个完美的解决方案，但它是一个值得思考的问题，可以作为未来发展的起点；-)

## 如何重新配置 DNS 解析

本节的目的是向您展示如何配置适当的名称服务器，以便在将 IP 地址解析为主机名时使用，反之亦然。它不打算解释如何将系统配置为名称服务器，因为这是我将在以后的文章中涉及的内容。只有当您不使用网络管理器并希望使用静态 IP 地址时，才应该影响此文件。

> resolv.conf 文件是解析器配置文件，用于配置工作站或服务器对互联网域名系统(DNS)的访问。该文件定义了要使用的名称服务器以及它们的使用顺序。因此，您应该将最可靠的服务器放在第一位。按照惯例，最多支持三个名称服务器。但是，如果没有给出名称服务器选项，解析器将尝试连接到本地主机上的名称服务器。

Resolv.conf 仍然可以做到这一点，但是随着 Precise Pangolin 的发布，一些事情发生了变化，无论您运行的是桌面还是服务器，您的系统都可能运行 resolveconf 程序:

“Resolveconf”是一个驻留在“/etc/resolve conf”中的小程序，它在启动时动态修改名称服务器信息。它是一个有用的工具，但对于我们的目的来说，它可能会造成中断，因此我们将通过简单地向'/etc/network/interfaces '文件添加一个新条目来解决它:

在终端类型中:

```
sudo gedit /etc/network/interfaces
```

现在将以下名称服务器行添加到您的文件中，将 XXX 改为更适合您需要的名称:

```
dns-nameservers xxx.xxx.xxx.xxx xxx.xxx.xxx.xxx
```

与上面的例子相似，这里是使用 Google 公共名称服务器的新文件:

```
auto lo
iface lo inet loopback
auto eth0
iface eth0 static
	address 	192.168.1.100
	netmask 	255.255.255.0
	network 	192.168.1.0
	broadcast 	192.168.1.255
	gateway 	192.168.1.254
        dns-nameservers 8.8.8.8 8.8.4.4
```

完成后，单击“保存”，然后“关闭”文件并重启以应用更改。

重新启动时，您可以看到新的名称服务器信息已经应用到我们的“resolve.conf”文件中，方法是打开终端并键入:

```
cat /etc/resolv.conf
```

可能看起来像这样:

```
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
nameserver 8.8.8.8
nameserver 8.8.4.4
nameserver 127.0.0.1
```

因此，完成更改后，要查询系统的 DNS 功能，只需返回命令行并键入:

```
nslookup www.sitepoint.com
```

或者尝试

```
dig www.sitepoint.com
```

这就是现在…但最重要的是，玩得开心:-)

所以直到下一次…

我希望你继续享受使用 Ubuntu 12.04 LTS 精确穿山甲。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Ubuntu Linux](https://learnable.com/courses/ubuntu-linux-65) 。

## 分享这篇文章