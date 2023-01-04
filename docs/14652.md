# 使用安全外壳和安全副本

> 原文：<https://www.sitepoint.com/using-secure-shell-and-secure-copy/>

web 开发人员和网站管理员可以进一步确保安全访问他们的服务器的一种方法是限制使用 telnet 和 ftp。

虽然可以对 ftp 进行严格配置，并在 ssl 下运行以获得额外的安全性，但它和 telnet 仍然是服务器安全性的弱点。替代品是可用的。

首先，通过要求使用安全外壳(ssh)替代 telnet 访问，用户会话被加密并基于密钥，而不是基于明文用户名和密码。SSH 很容易为所有平台用户所用，包括终端、终端应用程序和为 Linux、Macintosh 和 Windows 准备的 GUI 客户机。大多数*Nix 服务器默认安装了 ssh 服务器，可以安全地禁用 telnet，同时仍然确保通过命令行(终端)和客户端(大多数支持 telnet 和 ssh)的访问。

对于基于 Windows 的服务器，有开源的 ssh 服务器可用，其中最流行的是用于 Windows 的 OpenSSH。这将在 [Cygwin](http://www.cygwin.com/) 下安装 ssh，而不需要在 Windows 服务器上加载完整的 Cygwin 安装。

使用 ssh 的一个直接好处是可以访问安全拷贝(scp)和安全 ftp (sftp)。这两者都提供了传输文件的加密方法，是 telnet 和 ftp 的全功能替代品。

SSH、scp 和 sftp 可以从命令行使用，就像用户使用 telnet 和 ftp 一样。下面是一些例子:

嘘

`'ssh [user@domainname.com](mailto:user@domainname.com)'`

ssh domainname.com-l 用户”(针对 OS X 用户)。如果这是您第一次登录，系统会提示您接受并在本地 known_hosts 文件中生成一个条目，并提供一个密码，该密码会被安全地传输。然后，您可以像在 telnet 中一样访问所有相同的命令行功能。

SCP–将一个或多个文件从一个系统传输到另一个系统。例如，如果我有一个应用程序要上传并安装在我的服务器上，我可以执行以下操作:

`'scp application.tar.gz [user@domainname.com](mailto:user@domainname.com):'`

，并提示我输入密码，然后文件被传输到服务器上的“我的用户”文件夹中。如果我想为服务器上传一些 RPM 更新，我会确保我在文件所在的目录中，并发送以下内容

`'scp *.rpm [user@domainname.com](mailto:user@domainname.com):'`

，再次安全地传输文件。

scp 的另一个好处是稍微增加了一些压缩，从而缩短了传输时间。

SFTP——登录与命令行上的 FTP 一样，

`'sftp [user@domainname.com](mailto:user@domainname.com)'`

，回答密码提示后，将启动一个安全的 ftp 会话。

如果通过运行 ssh-keygen 设置一个密钥来处理系统和服务器之间的安全握手，则“无密码”会话是可用的。一个很好的教程可以在 [Fedora News](http://fedoranews.org/dowen/sshkeys/) 找到

客户端可用于所有平台，例如下面列出的平台。

**Linux**
——本地终端 Shell，包括 Bash、CSH (C Shell)和 KSH(Korn Shell)
——支持 SSH 的工具，如 gFTP 和 KDE ftp 工具

**Macintosh**
——Linux 上的原生终端外壳
——[MAC ssh 和 MacSFTP](http://pro.wanadoo.fr/chombier/)

**Windows**
–[OpenSSH for Windows(含客户端)](http://sshwindows.sourceforge.net/)
–[Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/)，很多 Windows 用户的最爱。

## 分享这篇文章