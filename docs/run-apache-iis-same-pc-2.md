# 如何同时运行 Apache 和 IIS:第 2 部分

> 原文：<https://www.sitepoint.com/run-apache-iis-same-pc-2/>

在本文第 1 部分中，我们讨论了如何将 Apache 和 IIS 安装在不同的真实或虚拟机器上。然而，如果你是一个团队的首席开发人员，这可能不切实际。在本帖中，我们将讨论如何在同一台机器上安装两台服务器。

## 同步服务器

您可以在同一台 Windows PC 上同时安装 Apache 和 IIS。尽管应用程序将会运行，但它们都在 TCP 端口 80 上侦听 web 请求——这将会有冲突，因此需要进行一些配置。

最简单的解决方法是将其中一台服务器的默认端口改为 81(或任何其他备用号)。在 Apache 上，您可以在 httpd.conf 配置文件中更改 **Listen** 指令:

```
 Listen *:81 
```

在 IIS 上，您可以在 IIS 管理器中更改**绑定**设置:

![IIS TCP port bindings](img/b1470062bb4eadafac977097e5e1e853.png)

微软为所有版本的 IIS 提供了一个有用的 TCP 端口操作页面。

因此，对于使用端口 80 的服务器，浏览器 URL 将是 **http://localhost/** ，对于使用端口 81 的服务器，浏览器 URL 将是 **http://localhost:81/** 。

这种方法允许您在不同的服务器上测试相同的应用程序，尽管您可能会遇到文件锁定或其他一些奇怪的问题。

这是一个很好的解决方案，但我没有使用它。主要原因:我经常在测试时忘记更改端口，备用端口号会导致开发复杂化，我很少同时需要两台服务器，我不喜欢后台程序在我不使用它们的时候运行。幸运的是，有一个替代方案…

## 服务器交换

我的首选解决方案是在我需要的时候运行我想要的任何服务器。Apache 和 IIS 都是作为 Windows 服务启动的——打开**管理工具** > **服务**。找到 Apache 和 IIS(W3SVC–万维网发布服务),并将两种启动类型都设置为手动:

![IIS TCP port bindings](img/395e21913404a8c73a54f0db90882659.png)

(记下 Apache 的服务名——在上面的截图中，是“Apache2.2”。)

当你的电脑启动时，两个服务器都不会运行。为了启动和停止服务，我们将创建 4 个批处理(。bat)文件放在同一文件夹中。批处理文件是从 MS-DOS 时代就已经存在的命令行指令列表:

**start-apache.bat**
这将停止 IIS 并启动/重启 apache。(注意,‘Apache’被假定为第三行中的服务名，但是您的安装可能不同。)

```
 @call stop-iis.bat
@call stop-apache.bat
@net start Apache 
```

**stop-apache.bat**
这将停止 apache(如有必要，更改服务名)。

```
 @net stop Apache 
```

**start-iis.bat**
这将停止 Apache 并启动/重启 iis。

```
 @call stop-apache.bat
@call stop-iis.bat
@net start W3SVC 
```

**stop-iis.bat**
这将在 Windows Vista 或 7 上停止 iis。

```
 @net stop was /y 
```

**stop-iis.bat**
这将在 Windows XP 或更早版本上停止 iis。

```
 @net stop iisadmin /y 
```

您可以双击这些文件来运行它们，或者创建桌面/开始菜单快捷方式以便于访问。

我希望这些解决方案中的一个或两个对您有用。对于在同一台 PC 上运行不同的 web 服务器，你还有其他的技巧吗？

## 分享这篇文章