# 使用 VirtualBox 构建您自己的开发服务器

> 原文：<https://www.sitepoint.com/build-your-own-dev-server-with-virtualbox/>

Sun 的 [VirtualBox](http://virtualbox.org/) 非常适合测试不同的桌面环境(例如，浏览器测试)，但我发现它也非常适合运行测试*服务器*环境。您可以将 Apache、PHP 和 MySQL 放在一个虚拟的 Linux 服务器上，而不是直接安装在您的台式机上。这样就不会干扰您的桌面，并且您可以确保您的开发环境尽可能接近您的最终部署环境。

有了运行在桌面操作系统中的虚拟 Linux 服务器，您可以通过 SSH 连接到它，向它上传文件，从它加载网页——无论您对真正的实时服务器做什么。您需要的所有软件都是免费的，而且配置简单。让我们开始吧！

## 开店

第一步是[下载 VirtualBox 客户端](http://www.virtualbox.org/wiki/Downloads)。选择适合您的主机系统的版本。

您还需要为您的 Linux 服务器获取一个磁盘映像。对于本教程，我将使用 64 位版本的 Ubuntu server 9.04(T1)，但是您可以随意使用您更喜欢的发行版。当然，您可能需要修改一些说明来适应您的特定设置。

我们也可以使用桌面构建，但是因为我们只对服务器功能感兴趣，所以最好坚持使用服务器构建:我们将节省内存，因为没有加载图形桌面界面。

## 安装 Ubuntu 服务器

启动 VirtualBox，点击**新建**新建一个虚拟机。逐步完成向导，确保选择 64 位版本的 Ubuntu(如果这是您下载的磁盘映像)。对于其他选项，我使用了默认值:RAM、磁盘大小、磁盘类型等等。

现在选择您的新虚拟机，并单击**开始**。VirtualBox 将询问您如何在虚拟机上安装操作系统。因为我们下载了一个。iso，从**媒体类型**菜单选择 **CD/DVD-ROM 设备**，从**媒体源**菜单选择**镜像文件**，选择你的 Ubuntu 服务器 iso。当系统启动时，你会看到 Ubuntu 的安装程序。选择你的语言，然后选择**安装 Ubuntu 服务器**。

按照屏幕上的说明安装服务器。请注意，当您对硬盘进行分区时，虚拟机只能“看到”您之前创建的磁盘映像。请随意使用整个磁盘。稍后，安装程序会提示您安装其他软件。出于我们的目的，我们将安装 LAMP 服务器和 OpenSSH 服务器包。这样，我们就拥有了开箱即用的全功能 web 服务器所需的一切。

当需要重启新服务器时，你可以从 VirtualBox 菜单中选择**设备>卸载 CD/DVD-ROM** 来“弹出”安装光盘。

使用您在安装过程中选择的用户名和密码登录新系统。用以下软件升级您的系统也是一个好主意:

```
sudo aptitude update
sudo aptitude safe-upgrade
```

## 从主机系统访问虚拟服务器

既然我们的服务器已经启动并运行，我们希望能够从我们的主机系统访问它。我们将对它进行设置，这样我们就可以对它进行 SSH，通过 SFTP 向它传输文件，并向 Apache 发出 HTTP 请求。

为此，我们需要编辑虚拟机的 xml 配置文件:

*   在 Mac 上，文件位于`~/Library/VirtualBox/Machines/<machine name>/<machine name>.xml`
*   在 Windows 上，它位于您个人文件夹的`.VirtualBox/Machines`子目录中。

所以对于我的机器，我称之为“Ubuntu 服务器”，我正在编辑`Machines/Ubuntu Server/Ubuntu Server.xml`

在文件的顶部，您应该会看到一个`<ExtraData>`标签。在该标记内，复制以下标记:

```
<ExtraDataItem name="VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/HostPort" value="2222"/>
<ExtraDataItem name="VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/GuestPort" value="22"/>
<ExtraDataItem name="VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/Protocol" value="TCP"/>
<ExtraDataItem name="VBoxInternal/Devices/pcnet/0/LUN#0/Config/apache/HostPort" value="8888"/>
<ExtraDataItem name="VBoxInternal/Devices/pcnet/0/LUN#0/Config/apache/GuestPort" value="80"/>
<ExtraDataItem name="VBoxInternal/Devices/pcnet/0/LUN#0/Config/apache/Protocol" value="TCP"/>
```

这些线路将 VirtualBox 配置为将主机系统上特定端口的请求转发到来宾系统上其他指定的端口。对于 SSH，我们将主机系统的端口 2222 转发到客户机系统的端口 OpenSSH 在这里监听)。同样的原则也适用于 Apache 配置项，主机上的端口 8888 映射到客户机上的端口 80。

完成后，保存 xml 文件并重启虚拟机。

如果机器无法启动，很可能是因为网络接口配置问题。在我们添加的行中，我们指定`pcnet`作为网络接口。为了确保这就是你的虚拟机正在使用的，在 VirtualBox 主窗口中右键点击它，然后点击**设置**。在**网络**选项卡中，从**适配器类型**下拉列表中选择一个 PCnet 适配器。现在，您应该可以毫无问题地重启虚拟机了。

现在，如果您在您的主机系统上打开一个浏览器，指向 `http://localhost:8888/`，您应该会看到默认的 Apache“It works！”页面。太好了！

类似地，要 SSH 到您的新服务器，使用您在 Ubuntu 服务器安装期间设置的用户名 SSH 到 localhost 上的端口 2222。(如果您在 Windows 上，您可以使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) SSH 客户端来执行相同的功能):

```
ssh -l <username> -p 2222 localhost 
```

你会收到通常的“未知主机”安全警告；键入“yes”进行连接，系统会提示您输入密码。输入后，您应该登录到您的服务器！随便看看，不要客气。

当我们仍然登录时，让我们再做一个任务:默认情况下，Ubuntu Server 中的 Apache web 根目录是`/var/www/`，您的默认用户对此没有写权限。让我们改变这一点，这样你就可以用 SFTP 上传文件到你的网络根目录。输入以下命令并按回车键:

```
sudo chown <username> /var/www
```

要使用 FTP 连接到您的服务器，不需要额外的配置。OpenSSH 通过 SFTP (SSH FTP)协议为您提供“免费”的 FTP。大多数客户(例如，FileZilla )支持它；只需选择 SFTP 作为协议，本地主机作为服务器，端口 2222，以及您的 Ubuntu 用户名和密码。选择`/var/www/`作为默认目录，您应该能够在服务器之间传输文件。

让我们测试一下一切是否正常:创建一个名为`info.php`的 php 文件，其中包含通常的`phpinfo`调用:

```
<?php
  phpinfo();
?> 

```

使用 FTP 客户端将文件上传到服务器的`/var/www/`文件夹。现在将你的浏览器指向`http://localhost:8888/info.php`，你会看到 PHP 信息页面。表格顶部的**系统**行将告诉你 PHP 正在 Ubuntu 上运行。

你有它！您可以测试服务器配置，提高您的系统管理员技能，并在您常用的桌面上运行的完整 Linux 服务器环境中开发您的网站和应用程序。

## 分享这篇文章