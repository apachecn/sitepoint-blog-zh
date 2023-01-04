# 如何使用 Apache 虚拟主机在一台 PC 上测试多个网站

> 原文：<https://www.sitepoint.com/multiple-websites-apache-virtual-hosts/>

很难找到一个只负责一个网站的 web 开发人员。在本文中，我们将配置您的开发 PC，以便您可以使用每个网站的专用域名测试任意数量的网站。您将需要在本地安装 Apache 2.2 的[，以及可选的](https://www.sitepoint.com/how-to-install-apache-on-windows/) [PHP](https://www.sitepoint.com/how-to-install-php-on-windows/) 和 [MySQL](https://www.sitepoint.com/how-to-install-mysql/) 。

## 天堂主机文件

当您在浏览器中输入地址时，域名通常会被域名服务器转换为 IP 地址。在此之前，计算机将检查自己的主机文件。几乎每个操作系统上都有主机文件:

*   **Windows NT、2000、XP、2003 和 Vista:**% WinDir % system32 driversetchosts(注意此位置可以在注册表项 HKEY _ LOCAL _ MACHINESYSTEMCurrentControlSetServicesTcpipParametersDataBasePath 中更改)
*   **Windows 95、98、ME:** %WinDir%hosts
*   **Linux，Unix，BSD:** /etc/hosts
*   **麦克 OS X:**/私人/etc/主机

在大多数情况下，您的主机文件将包含一个条目:

```
 127.0.0.1  localhost 
```

在浏览器中输入“http://localhost/”将解析为 TCP/IP 环回地址 127.0.0.1(您的 PC)。

主机文件可以包含任意数量的域映射。假设我们想在本地测试两个站点:*www.mysite1.com*和*www.mysite2.org*。出于测试目的，我们可以在 hosts 文件中创建两个域，例如*我的站点 1* 和*我的站点 2* :

```
 127.0.0.1  localhost
127.0.0.1  mysite1
127.0.0.1  mysite2 
```

一些操作系统会在保存主机文件后立即实施更改。Windows 用户应该在命令行输入“nbtstat -R”。如果其他方法都失败，请尝试重新启动。

## 配置 Apache 虚拟主机

Apache 可以在一台机器上运行任意数量的网站。(注意:Microsoft IIS 的服务器版本也可以，但 Windows Professional 或 VisualStudio.NET 版本不能)。

关闭 Apache 并在文本编辑器中加载它的配置文件 confhttpd.conf。假设我们希望域 *mysite1* 使用 D:WebPagesmysite1 中的文件，而 *mysite2* 使用 D:WebPagesmysite2 中的文件，我们将在文件底部添加以下虚拟主机定义:

```
 # Virtual hosts
NameVirtualHost *:80

# Any unspecified domain
<VirtualHost *:80>
	DocumentRoot D:/WebPages
</VirtualHost>

# mysite1 domain
<VirtualHost *:80>
	ServerName mysite1
	DocumentRoot D:/WebPages/mysite1
</VirtualHost>

# mysite2 domain
<VirtualHost *:80>
	ServerName mysite2
	DocumentRoot D:/WebPages/mysite2
</VirtualHost> 
```

保存文件并重启 Apache。

## 测试您的站点

将您的网站文件相应地复制到 D:WebPagesmysite1 和 D:WebPagesmysite2。现在，您可以在浏览器中输入 **http://mysite1/** 或 **http://mysite2/** 来测试适当的站点。

PHP 程序员的额外提示
知道网站是运行在实时环境还是开发环境是很有用的，例如，调试消息不会显示在实时网站上。您可以使用如下代码来检测域:

```
 <?php
$debug == ($_SERVER['HTTP_HOST'] == 'mysite1');
if ($debug) echo "debug message";
?> 
```

另请参见:

*   [如何安装 Apache](https://www.sitepoint.com/how-to-install-apache-on-windows/)
*   [如何安装 PHP](https://www.sitepoint.com/how-to-install-php-on-windows/)
*   [如何安装 MySQL](https://www.sitepoint.com/how-to-install-mysql/)

在一台电脑上测试多个网站，有没有其他的小技巧？

## 分享这篇文章