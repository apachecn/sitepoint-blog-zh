# 在 Windows 上运行 Apache 时如何解决端口 80 问题

> 原文：<https://www.sitepoint.com/unblock-port-80-on-windows-run-apache/>

*这篇文章写于 2011 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于使用 Apache 的知识，你可能会对这篇关于使用 Apache CloudStack 启动 VM 的文章很感兴趣。*

 *如果客户意识到开发人员在操作系统上遇到的麻烦就好了。客户会在他们的电子邮件失败*(他们忘记打开路由器)*或者他们的网站中断*(他们使用的是 IE5.0)* 的第一时间打电话给你。当 Apache 因为 80 端口被阻塞而无法启动时，可以找谁？

很抱歉没有写一篇典型的 web 开发帖子，但这正是我遇到的问题，而且似乎许多其他人也遇到过。我在 Windows 上运行 Apache 和 IIS，更喜欢使用[服务器切换方法](https://www.sitepoint.com/run-apache-iis-same-pc-2/),这样任何时候都只有一个应用程序监听端口 80。它运行得很好，我已经在 Windows 7、Vista、XP 和 NT 上运行类似的设置很多年了。

然而今天，Apache 无法启动。应用程序事件查看器(管理工具)中出现以下错误:

```
The Apache service named  reported the following error:
>>> (OS 10013) An attempt was made to access a socket in a way forbidden by its access permissions.
:make_sock: could not bind to address 0.0.0.0:80.

```

另一个应用程序占用了端口 80。

## 明显的候选人

有许多著名的 Windows 程序使用端口 80:

**IIS**
最有可能的罪魁祸首是微软互联网信息服务器。您可以在 Windows 7/Vista 上从命令行停止该服务:

```
net stop was /y
```

或 XP:

```
net stop iisadmin /y
```

**SQL Server Reporting Services**
即使卸载 SQL Server，SSRS 也可以保持活动状态。要停止服务:

1.  打开 SQL Server 配置管理器。
2.  在左侧窗格中选择“SQL Server 服务”。
3.  双击“SQL Server Reporting Services”。
4.  点击停止。
5.  切换到服务选项卡，并将启动模式设置为“手动”。

令人恼火的是，Skype 可以切换到 80 端口。要禁用它，选择工具>选项>高级>连接，然后取消选中“使用端口 80 和 443 作为传入连接的替代”。

## 80 端口是干什么用的？

如果非法入境者、SSRS 和 Skype 不是罪魁祸首，进一步的调查工作是必要的。在命令行上输入以下内容:

```
netstat -ao
```

将列出活动的 TCP 地址和端口—找到本地地址为“0.0.0.0:80”的线路，并记下 PID 值。

现在右键单击任务栏并选择启动任务管理器。导航到进程选项卡，如有必要，单击查看>选择列…以确保选中“PID(进程标识符)”。现在，您可以找到上面记录的 PID。描述和属性应该有助于您确定哪个应用程序正在使用该端口。

任务管理器允许你终止进程，但是要小心——特别是当它是“NT 内核和系统”的时候。

## 微软-HTTPAPI/2.0

NT 内核和系统是一个基本服务。停止它可能会以类似蓝屏的方式停止 Windows。因此，在命令行中输入以下内容:

```
telnet 127.0.0.1 80
```

如果你面对的是一个空白屏幕，输入“GET”并按回车键。很有可能，您会看到一行声明 Microsoft-HTTPAPI/2.0 正在监听端口 80。如果是这种情况，请从“管理工具”中打开“服务”并找到“Web 部署代理服务”。停止服务并将其启动类型设置为“手动”。

Web Deployment Agent 服务是与 WebMatrix 一起部署的，这是我苦恼的原因。它也可以与使用 Microsoft 的 Web 平台安装程序安装的其他应用程序一起分发。

这让我沮丧了几个小时，所以我希望它能解决你在阿帕奇或 WAMP 的创业问题。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [PHP & MySQL 初学者网络开发](https://learnable.com/courses/php-mysql-web-development-for-beginners-13)。

## 分享这篇文章*