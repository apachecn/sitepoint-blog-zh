# 如何在 Windows 上安装 Apache Web 服务器

> 原文：<https://www.sitepoint.com/how-to-install-apache-on-windows/>

**开始 web 开发很容易:您创建一个名为`something.html`的文件，在记事本中编辑它，然后在浏览器中加载它。使用这个过程可以构建简单的站点，但是要真正探索其可能性，您需要一个 web 服务器。**

这篇受欢迎的文章在 2020 年更新，以提供最有帮助和最准确的指导。

## 什么是 Web 服务器？

[*(如果你愿意，可以跳到下一节……)*](#whyapache)

web 服务器是监听请求并返回数据(通常是文件)的软件。当你输入“www.mysite.com”时，请求被转发到一台运行 web 服务器软件的机器上，该软件将一个文件返回给你的浏览器——比如`index.html`的内容。然后，浏览器可能会基于 HTML 内容(如 CSS、JavaScript 和图形文件)发出进一步的请求。

由于 web 服务器位于您的浏览器和所请求的文件之间，它可以执行直接打开 HTML 文件所无法执行的处理。例如，它可以解析连接到数据库并返回数据的 PHP 代码。

你可以使用你的主机的网络服务器进行测试，但是上传会变得很无聊，而且在完全测试之前，改变可能会生效。您需要的是本地 web 服务器安装。

## 为什么是阿帕奇？

一般来说，最好使用你的网络主机所使用的网络服务器软件。除非你在微软 IIS 上创建 ASP.NET 应用程序，否则你的主机很可能会使用 Apache(最广泛、功能最全的 web 服务器)。这是一个开源项目，所以下载或安装不需要任何费用。

以下说明描述了如何在 Windows 上安装 Apache。macOS 自带 Apache 和 PHP。大多数 Linux 用户都预装了 Apache，或者可以从基本存储库中获得。

## 一体式包装

有一些优秀的 all-in-one Windows 发行版在单个安装文件中包含了 Apache、PHP、MySQL 和其他应用程序——比如 [XAMPP](http://www.apachefriends.org/en/xampp.html) (针对 Windows、Linux 和 MAC OS)[WAP server](http://www.wampserver.com/en/)和[WAP developer Pro](http://www.devside.net/server/webdeveloper)。使用这些包没有错，尽管手动安装 Apache 将帮助您了解更多关于系统及其配置选项的信息。

## Apache 安装向导

从 [Apache 下载页面](http://httpd.apache.org/download.cgi)可以获得一个优秀的官方`.msi`安装向导。这个选项当然推荐给新手用户或者第一次安装 Apache 的用户。

## 手动安装

手动安装有几个好处:

*   备份、重新安装或移动 web 服务器可以在几秒钟内完成。
*   您可以更好地控制 Apache 启动的方式和时间。
*   您可以在任何地方安装 Apache，比如便携式 USB 驱动器(对于客户端演示很有用)。

### 步骤 1:配置 IIS

Apache 监听 TCP/IP 端口 80 上的请求。您需要卸载或禁用任何使用该端口的程序。如果您有 Windows 的专业版或服务器版，您可能已经安装了 IIS。如果你更喜欢 Apache，要么[删除作为 Windows 组件的 IIS，要么禁用它的服务](http://support.microsoft.com/kb/321141)。

### 步骤 2:下载文件

我们将使用来自 Apache Lounge 的非官方 Windows 二进制文件。这个版本在性能和稳定性方面比官方的 Apache 发行版有所改进，尽管我还没有注意到显著的不同。然而，它是由 www.apachelounge.com/download/[的一个可手动安装的 ZIP 文件提供的。](http://www.apachelounge.com/download/)

你还应该从 Microsoft.com 下载并安装 Windows C++运行时。你可能已经安装了这个，但是再安装一次也无妨。

像往常一样，记得对所有下载进行病毒扫描。

### 步骤 3:提取文件

我们将在`C:/Apache24`中安装 Apache，因此将 ZIP 文件解压到`C:/`驱动器的根目录。Apache 可以安装在你系统的任何地方，但是你需要修改`SVROOT`配置来指向你解压后的位置——比如`E:/Apache24`。

### 步骤 4:配置 Apache

Apache 是用 Apache 文件夹中包含的文本文件`conf/httpd.conf`配置的。用你最喜欢的文本编辑器打开它。

请注意，所有文件路径设置都使用正斜杠(`/`)而不是 Windows 反斜杠。如果你在除了`C:/Apache24`之外的地方安装了 Apache，现在是搜索并替换所有对`C:/Apache24`的引用的好时机。

对于您的生产环境，您应该更改几行代码:

*   第 60 行，监听端口 80 上的所有请求:

    ```
    Listen *:80 
    ```

*   第 162 行，通过删除#(可选，但很有用)来启用 mod-rewrite:

    ```
    LoadModule rewrite_module modules/mod_rewrite.so 
    ```

*   第 227 行，指定服务器域名:

    ```
    ServerName localhost:80 
    ```

*   第 224 行，允许`.htaccess`覆盖:

    ```
    AllowOverride All 
    ```

### 步骤 4:更改网页根目录(可选)

默认情况下，Apache 返回在其`C:/Apache24/htdocs`文件夹中找到的文件。最好使用另一个驱动器或分区上的文件夹，以便于备份和重新安装。出于本例的目的，我们将创建一个名为`D:WebPages`的文件夹，并相应地更改`httpd.conf`:

*   第 251 行，设置根:

    ```
    DocumentRoot "D:/WebPages" 
    ```

*   第 252 行:

    ```
    <Directory "D:/WebPages"> 
    ```

### 步骤 5:测试您的安装

现在可以测试您的 Apache 配置了。打开命令框(开始>运行>`cmd`，输入:

```
# navigate to Apache bin directory
cd /Apache24/bin
# Test httpd.conf validity
httpd -t 
```

它应该显示“语法正常”。如果没有，纠正任何`httpd.conf`配置错误并重新测试，直到没有错误出现。

### 步骤 6:将 Apache 安装为 Windows 服务

启动 Apache 最简单的方法是将其添加为 Windows 服务。以**管理员**的身份打开一个新的命令提示符，并输入以下内容:

```
cd /Apache24/bin
httpd -k install 
```

打开控制面板、管理工具、服务，然后双击 Apache2.4。将启动类型设置为“自动”，以确保每次启动 PC 时 Apache 都会启动。

或者，将启动类型设置为“Manual”，并在您选择使用命令“net start Apache2.4”时启动 Apache。

### 步骤 7:测试 Web 服务器

在 Apache 的网页根目录下创建一个名为`index.html`的文件(或者是`htdocs`或者是`D:\WebPages`，并添加一点 HTML 代码:

```
<html>
    <head>
        <title>Testing Apache</title>
    </head>
    <body>
        <p>Apache is working!</p>
    </body>
</html> 
```

确保 Apache 已经成功启动，打开 web 浏览器并输入地址`http://localhost/`。如果一切顺利，您的测试页面应该会出现。

一般来说，大多数问题都是由`httpd.conf`配置文件中的错误设置引起的。如需更多信息，请参考 [Apache 文档](http://httpd.apache.org/docs/2.2/)。

## 包裹

就是这样！以下是一些其他相关指南:

*   [如何安装 PHP](https://www.sitepoint.com/how-to-install-php-on-windows/)
*   [如何安装 MySQL](https://www.sitepoint.com/how-to-install-mysql/)
*   MySQL:MyISAM 表的利与弊
*   [MySQL:InnoDB 表的利与弊](https://www.sitepoint.com/mysql-innodb-table-pros-cons/)
*   [如何使用 MySQL 外键加快数据库开发](https://www.sitepoint.com/mysql-foreign-keys-quicker-database-development/)

*有关工作流的更多信息，请观看我们的截屏视频[什么是 npm，我如何使用它？](https://www.sitepoint.com/premium/screencasts/what-is-npm-and-how-can-i-use-it)*

## 分享这篇文章