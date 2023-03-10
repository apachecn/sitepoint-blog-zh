# 快速提示:使用 Opera 进行本地开发，Nginx 502 错误

> 原文：<https://www.sitepoint.com/local-development-with-opera-nginx-502-error/>

*如果您正在使用 Apache 进行本地开发，这并不适用于您*

我最近在做一些本地开发工作，但第一次是在我的“新”浏览器——Opera 中。我像往常一样设置了我的 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)实例和它的虚拟主机，但是除了一个 502 错误之外，什么也没有显示:

[![Screenshot 2016-06-04 17.23.37](img/68966f82ea448b8b2cff4d5ad1c4e83f.png)](https://www.sitepoint.com/wp-content/uploads/2016/06/1465053845Screenshot-2016-06-04-17.23.37.png)

如果您检查开发工具，您会注意到状态是 502——典型的 Nginx 坏网关错误。

然后，我花了大约 30 分钟，从虚拟机中的空错误日志开始，重新检查 PHP 套接字和端口，直到我意识到我找错了地方:

[![Screenshot 2016-06-04 17.23.44](img/cd873e35888149ec46de4221cab8bc2b.png)](https://www.sitepoint.com/wp-content/uploads/2016/06/1465053871Screenshot-2016-06-04-17.23.44.png)

在撰写本文时的当前版本中，Opera 默认开启 VPN，当您试图访问有效的 TLD 时(。com，。app，。当 VPN 打开时，它崩溃了。你看到的错误来自 Opera 的 VPN，顺便说一句，运行 Nginx。

要解决这个问题，只需在本地开发期间关闭 VPN，它就会工作。

希望这有助于人们避免更多不必要的木材狩猎。

## 分享这篇文章