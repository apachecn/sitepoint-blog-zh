# 使用您的 Raspberry Pi 设置家庭 VPN 服务器

> 原文：<https://www.sitepoint.com/setting-up-a-home-vpn-using-your-raspberry-pi/>

使用物联网的一个原因是，当您不在家庭网络中时，很难连接到家中的设备。当我四处旅行时，我每周都面临这个困难——我需要运行测试和构建物联网演示，但我不在家做这些事情！我决定把我的 Raspberry Pi 变成一个 VPN，这样我就可以远程连接到我的家庭网络。这里告诉你如何使用名为 [PiVPN](https://github.com/pivpn/pivpn) 的 OpenVPN 安装程序来做同样的事情。

您可以使用 Pi 上的终端或者使用 SSH 远程连接到您的 Raspberry Pi 来运行下面的教程。如果你不太确定如何 SSH 到你的树莓 Pi 中，我有一个简短的指南，教你如何 SSH 到树莓 Pi 中。如果您没有在您的 Pi 上设置静态 IP 地址，我建议您直接在您的 Pi *上工作(否则，您的 IP 地址很可能会在这个过程中变成静态 IP，并把您踢出您的 SSH 会话！)*。

## 开始安装过程

首先，我们在 Pi 的终端中运行以下命令(通过 SSH 或直接在 Pi 上):

```
curl -L https://install.pivpn.io | bash
```

*重要提示:该命令将从 web 下载的随机脚本直接解析到您的 Pi bash 中。如果您不信任安装源，这可能会非常危险，因为它会立即运行您给它的任何代码。我没有一行一行地检查他们的 bash 命令(我信任他们！)但是如果你有任何疑问，可以在他们的 GitHub 账户(在`auto_install`文件夹内的`install.sh`下)上查看[。](https://raw.githubusercontent.com/pivpn/pivpn/master/auto_install/install.sh)*

运行该命令将打开一个稍微好看一点的基于文本的 GUI，它以一个简单的提示符开始:

![Initial automated installer prompt](img/328c60bbc93c3c02c46be362857f3ba0.png)

一旦你点击*输入*，你将被带到另一个屏幕，该屏幕将指出静态 IP 地址对于 VPN 服务的工作是很重要的:如果你的 Pi 没有静态 IP，你的路由器将没有 IP 地址来转发 VPN 功能。你的 Pi 上没有静态 IP？不要担心，自动安装程序将很快为您设置一个静态 IP。

![Screen telling you to have a static IP](img/aaf335cb24d4158dce1e4bdd4ab52660.png)

点击*进入*进入下一个屏幕:

![Ethernet or Wi-Fi selection](img/d3b06a7fcf657e79f2c78aa9efceb283.png)

在这个屏幕上要小心:点击 *回车*将带你进入下一个屏幕，而不是在两个单选按钮选项中进行选择。我在安装过程中犯了这个错误，重新启动安装过程来改变它会变得很混乱！

在接口选择中，您可以选择是否要在您的以太网连接 *(eth0)* 或 Wi-Fi *(wlan0)* 上设置 VPN。我个人选择 Wi-Fi，因为我的 Pi 不靠近我的路由器。如果您可以通过以太网连接 Pi，这将大大提高速度！要选择一个选项，用箭头键移动选择，用*空格键*选择。然后点击*进入*进入下一屏。

![Confirmation of current IP address screen](img/1b5d840aa6ad05b0d39316a64b85f2d0.png)

此屏幕确认您当前的 Pi IP 地址。我个人想把我的 Pi 的 IP 改成更令人难忘的，所以我点击箭头键把我的选择移动到`<No>`并点击*空格键*来选择它。然后我点击*回车*进入设置来改变我的 Pi 的静态 IP。

![Entering desired IP address](img/fbfb27ac1f6ff5e4ef1a129ef2dc7f50.png)

在这个屏幕中，您将输入您希望 Pi 拥有的静态 IP 地址。我选择了`192.168.0.31`。一旦你得到了你想要的 IP 地址，点击*进入*。

![IP address of the gateway](img/db349a64141047b4a36fc3fc3d8b3f23.png)

在这个屏幕中，您需要输入您的路由器或默认网关的 IP 地址。这将取决于您的网络设置，但很多时候这将是`192.168.0.1`。如果您不确定，请尝试输入您输入的任何 IP 地址，以便在浏览器中访问路由器的配置页面。一旦你把这个输入进去，点击*回车*键。

![Confirmation of IP address](img/26e7bdea31598ee8d0001f3317ffdac8.png)

点击*在这个屏幕上输入*来确认你的 IP 地址设置是正确的。如果您的家庭网络设置为大多数家庭网络的默认设置，它们看起来应该与上面的类似。如果没有，很可能你已经知道你自己的设置。

![Settings confirmed](img/187a4a898de16b9eff46a35aef171d0d.png)

可视化 GUI 风格的界面将会消失，您将会在终端中看到您的设置在显示的文本中得到确认。稍等片刻，它会执行这些操作来设置静态 IP 等。如果你已经登陆并改变了你的 IP 地址…很有可能，这就是你被卡住的地方，因为你的连接会掉线！如果您遇到这种情况，请再次运行该过程，但要连接到您这次设置的静态 IP。

一旦 PiVPN 的网络设置调整完成，它会带你回到一个更好看的屏幕。

![User selection start screen](img/71418a4225944e1a0b8e9e337442e6cc.png)

上面的屏幕只是通知我们，我们将很快选择我们的 VPN 的本地用户。点击*进入*开始并移动到下一个屏幕。

![Choosing Pi user](img/a734364a31118d9d2aefbdf61eb67a28.png)

在这个屏幕中，如果您的 Pi 设置为默认值，您可能只有一个选项—Pi 用户。如果是这样的话，打*回车*！如果你已经设置了一个自定义用户，并想通过它进行设置，通过箭头键选择该用户，并点击*空格键*。然后一旦用户被选中，点击*进入*。

![Unattended upgrades explanation](img/da8608100913482c1a73a6c00c67c7d8.png)

下一个屏幕建议你注意一些**非常重要的事情**！将您的 Pi 设置为 VPN 意味着它将有一个对更广泛的互联网开放的端口。这伴随着严重的责任:如果出现安全问题，任何人都有可能访问您的 Pi。根据你的网络设置，以 VPN 的方式访问你的 Pi 意味着非常危险的事情。这可能意味着**访问你的整个家庭网络**。出于这个原因，PiVPN 建议打开无人值守升级，这至少会自动更新安全包。*值得注意的是，您仍有责任关注媒体上的安全漏洞，并留意您网络上的奇怪活动。您可以设置大多数路由器来显示连接日志等；关注这些事情以及更多。*

![Do you want unattended upgrades question](img/3d8decb12dc777ad2759724a57173d6e.png)

所以，对于无人值守升级的问题，你可能想说是——除非你知道你在维护你的 Pi 更新方面做什么。一旦你做了那个决定，花哨的 UI 屏幕就会消失。

![Looking up updates](img/8cb67d938ca0ea6ad6ef9d766191e77e.png)

这里它通过`apt-get`检查潜在的更新。在此过程之后，它会通知您是否有安装后应该做的更新。在我的例子中，它在我相当旧的升级版 Pi 上发现了 143 个更新！在运行 VPN 的整个过程之后，运行`sudo apt-get upgrade`来确保您的 Pi 在不久的将来是安全的。

然后它还会检查您的 Pi 上是否安装了 OpenVPN。如果没有，它就开始这个过程！这将弹出以下屏幕:

![Setting the default VPN port](img/d63af009faaca2ce4f9cecae9ab37b9d.png)

该屏幕是 OpenVPN 安装过程的一部分。您可以选择通过 Pi 上的哪个端口运行您的 VPN。我让它保持原样—在端口 1194 —并点击*输入*。

![Confirming VPN port](img/8d2a2aa351bc45d936c1441566e16fa0.png)

检查输入的端口是否正确，然后再次点击*输入*。

![pivpn19](img/a2855ed9495e3b8557ed2ec7b6304466.png)

在上面的屏幕中，我们选择我们想要的加密级别。加密越大，运行和设置的时间就越长，但也就越安全。我坚持使用推荐的 2048 位，并点击*回车*。我不建议降到 1024 位，但如果你想要超级彻底的加密，你可以升到 4096 位。

![Default values notification](img/9abd9bb685f9a26eee1a84b45af26c3a.png)

现在，上面的屏幕告诉我们，下一个屏幕将显示安全证书信息的默认值。它让你知道你不需要改变它们，因为你和连接的客户是唯一能看到它们的人。它说的是事实。你不需要担心改变这些。点击*进入*进入下一个屏幕。

![pivpn21](img/f9a0fcc5b9f643a35e5c8f3d8648ffa9.png)

您不仅不需要担心更改它们，我也看不到更改这个屏幕中的值的方法。浏览它们，然后点击*进入*。

![Are these values correct](img/f29a008c3cf4e4bbeb69a8ab69961d8a.png)

它会询问这些值是否正确，只需再次点击*输入*即可。

![Alert for key generation](img/c9c34bd5a4ee64849d4b6ce3003a6804.png)

接下来，它让您知道它即将生成您的加密密钥。点击*进入*。

![Generating encryption keys](img/f1b237ab8085f6ac6789724d3f8f58d7.png)

然后它将离开光滑的用户界面，把你带回终端，开始生成密钥。生成需要一段时间，如果选择 4096 位加密，时间会更长！

![Still generating](img/4f30810f56b62d1429e0fe32613a54ec.png)

真的很久了…

![And even now still generating](img/fd773c167fa80d5711e0b098a6c0d48d.png)

运行时，一定要喝点茶或咖啡。

![Public IP or DNS](img/af1dd3c98f3914ac9349726acf6a0f5f.png)

一旦完成，它将询问你是否有一个你想要使用的公共 DNS 入口，或者你是否想要使用一个 IP 地址。这部分完全取决于你。您的 IP 地址是您在网络上从 ISP 处获得的面向公众的 IP 地址。如果你去像 www.whatismyip.com 这样的网站，它就会出现。有的 ISP 给一个静态的不会变，有的会断断续续的变。

对于我自己的 ISP，不能保证它会保持不变，所以我注册了[No-IP](https://www.noip.com)——一种允许你将他们提供的免费网址(比如`yourdomain.ddns.net`)链接到你的公共 IP 的服务。如果您的 IP 发生变化，您可以使用 No-IP 来更改该值。

如果你想使用你的 ISP 提供的 IP 地址，让它保持原样，点击*进入*。否则，用键盘导航到“DNS Entry”，按下*空格键*，然后按下*回车*进入下一个屏幕。

![Public DNS Name](img/f33349065e0019e8f943a268299f06a1.png)

如果您选择了“DNS 条目”，您将会看到上面的屏幕提示。像我上面做的那样添加你的网址。如果你选择了“IP 地址”，它会询问你。

![Choosing DNS provider](img/f53801ab4efbe008be1fec8a6d95e6b8.png)

接下来，会要求您选择要用于 VPN 的 DNS 提供商。如果您希望拥有 VPN 的原因是为了隐私，这可能很重要:无论您选择哪个 DNS 提供商，都可以看到 VPN 发出的请求。如果你正在寻找一个完全私有的 VPN，你会想要一个更加私有的 DNS 解决方案。在我的例子中，我没有把它用于那个目的，而是把它留为“Google ”,点击*回车*继续。

![Success screen](img/9ba6c502ba1d795d2c68c4803509b008.png)

至此，您已经成功地完成了安装！点击*进入*进入祝贺屏幕，但是请注意，我们需要按照提示运行`pivpn add`！

![Reboot option](img/09102576729d8b91b3d70d4622ee7eb4.png)

选择是重新启动您的 Pi！您可能需要用键盘选择它，就像前面的其他选择一样。

![Final terminal bits and pieces](img/79a85329464a1a2f7f6e9850e1e4551d.png)

它应该完成最后的工作，然后重启你的 Pi。

## 记得更新！

请记住，现在您已经完成了安装 VPN 的整个过程，运行以下命令以确保您的 Pi 在不久的将来是安全的:

```
sudo apt-get upgrade
```

一旦全部更新完毕，我们就可以放心地设置 VPN 访问的客户端了！

## 设置您的第一个 VPN 客户端

至此，您已经通过 PiVPN 在 Pi 上运行了一个 OpenVPN 实例。但是，要从网络上的其他计算机和设备访问 VPN，需要一个这些设备可以连接的客户端。要添加此客户端，我们输入以下命令:

```
pivpn add
```

它会要求您输入客户端的名称。随便你怎么称呼它。它还会询问密码:这是通过这个客户端访问 VPN 的密码。不要忘记这一点，否则您将无法使用此客户端连接到您的 VPN 服务器！

一旦你完成了，它将为客户端生成一个`.ovpn`文件。您将需要它来登录每台客户端设备。

## OpenVPN 客户端

您可以下载一系列 OpenVPN 客户端在各种平台上使用。你可以在 openvpn.net 找到它们。这些不是唯一可用的；我个人在 Mac 上使用过 [Tunnelblick](https://tunnelblick.net) 。

## 端口转发

每个人都不同的一个方面是路由器上的端口转发。您需要在路由器上为您为 VPN 设置的端口*(默认为 1194)* 设置转发。我们希望对该端口的任何请求都发送到您的 Pi 的 IP 地址。每个路由器的这种设置是不同的；不过，你可以在这里了解更多关于[港口转运的流程。查找“端口转发”和您的路由器名称，了解如何为您自己的路由器做到这一点。更新路由器设置时要小心！](http://www.online-tech-tips.com/computer-tips/ott-explains-what-is-port-forwarding-and-what-is-it-used-for/)

## 偷你的钥匙

从 Pi 中跨文件复制的最简单的方法是使用 SFTP。你可以使用像 Filezilla 这样的 FTP 程序来实现。这里有关于如何使用 [FTP 和树莓 Pi 的官方文档](https://www.raspberrypi.org/documentation/remote-access/ssh/sftp.md)。连接到 Pi 后，从`/home/pi/ovpns`开始复制您的密钥:

![ftpingkey](img/10c77b0d8b89f4a8965d2de167d91a67.png)

## 连接到您的 Pi 的 FTP

一旦你设置好了一切，在你的设备上打开你的 OpenVPN 应用程序并加载你从 Pi 下载的`.ovpn`文件。在加载时，它会询问你设置的密码:输入密码，它会运行并连接你！

## 结论

现在，您已经通过 VPN 成功连接到家庭网络，您应该能够轻松访问该网络上的设备。例如，如果你在那个网络上有一个本地 web 服务器，你应该能够使用它的本地网络地址访问在那个服务器上运行的网页。同样，如果您尝试使用其 IP 地址从 VPN 连接的设备连接到网络上的物联网设备，现在应该可以工作了！

手边没有覆盆子酱吗？看看 Surfshark 关于使用家用电脑或路由器设置 VPN 服务器的说明。如果你只是想给你的工具包添加一个可靠的 VPN，你可以[在我们的买家指南中为开发者比较最好的 VPN](https://www.sitepoint.com/best-vpns-for-developers/)。

如果你有任何关于在 Raspberry Pi 上设置 VPN 的建议，关于在开放网络上更好地保护 Raspberry Pi 的建议，或者关于 VPN 设置后可能发生的事情的其他想法，我很乐意听到它们！请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。

## 分享这篇文章