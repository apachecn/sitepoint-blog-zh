# PHP 和 with 用 PHP 深入研究 Windows

> 原文：<https://www.sitepoint.com/php-wmi-dig-deep-windows-php/>

有许多设备(服务器、台式机、笔记本电脑、平板电脑、手机等)运行 Windows 操作系统。我们生活在基于 nix 的世界中的许多人不得不在这个操作系统中工作，或者如果我们不工作，我们迟早会工作。除了我们期望从一个*nix 系统中得到的常规工具(比如 Apache、PHP、MySQL、C/C++编译器等)，Windows 还提供了一系列其他操作系统所没有的独特功能，WMI 就是其中之一。

在这篇文章中，我们将解决这样的问题:什么是 WMI？如何在 PHP 中使用 WMI？我们将有一些简单的示例代码来介绍基本的编程技术。

#### 什么是 WMI，为什么我们需要处理它

在这篇文章中，MSDN 网站给出了 WMI 的官方定义，摘录如下:

> windows Management Instrumentation(WMI)是 Microsoft 对基于 Web 的企业管理(WBEM)的实现，这是一项行业计划，旨在开发一种在企业环境中访问管理信息的标准技术。

这里的三个关键词是:**基于网络的**、**管理信息**、**企业环境**。因此，如果我们的托管 IT 环境是一个大规模、基于 Windows 的体系结构，并且我们希望检索每个单独节点的管理信息并以 web 方式呈现出来，我们将需要与 WMI 进行交互。WMI 还能够做其他事情，比如在远程 PC 上生成一个进程，但这超出了本文的范围。

WMI 提供机器的全面知识，包括硬件和软件。它有所谓的 CIM ( [公共信息模型](http://msdn.microsoft.com/en-us/library/aa389234.aspx))以面向对象的方式封装信息。它还提供了几个编程接口来检索所述信息。在纯 Windows 环境下，这些将是 [PowerShell](http://technet.microsoft.com/en-us/library/bb978526.aspx) ，VB Script 和。网络语言。但是在我们的例子中，它将是 PHP。

用 WMI 编程的一个基本问题是:哪些“信息”是可用的？换句话说，哪些对象/类是可用的？幸运的是，微软提供了 WMI 提供的类及其属性的完整列表。请访问[此处](http://msdn.microsoft.com/en-us/library/aa394554.aspx)获取完整参考。在 WMI 编程中，大多数时候我们指的是 [Win32 类](http://msdn.microsoft.com/en-us/library/aa394084.aspx)。

### 先决条件

在 Windows 主机上，必须安装 WMI 来提供 CIM。默认情况下，任何比 Windows XP 新的 Windows 系统都应该安装并启用 WMI。

我们可以通过以下两个步骤来验证这一点:

1.  在您的 Windows 机器中启动“`Computer Management`”，并查看名为“`Windows Management Instrumentation`”的服务是否正在运行。如果没有，请启动该服务。
2.  在命令提示窗口中启动“`wbemtest`”。将出现一个名为`Windows Management Instrumentation Test`的对话框。该对话框中的许多按钮目前被禁用，但我们可以单击“`Connect...`”按钮来调用一个新的对话框，如下所示:

![](img/29b6cc45b86a30287adcc1679a56b47d.png)

通常，我们不需要改变什么。`root\cimv2`是我们的 WMI 接口的系统内置名称空间。在此对话框中点击`Connect`按钮即可。这将带我们回到前一个窗口，所有按钮都已启用。

能够连接到机器的 WMI 接口只是先决条件之一。我们还需要确保 Windows 防火墙将允许 WMI 呼叫通过。

在 Windows 防火墙中，选择“`Advanced Settings`”，然后启用 WMI 相关条目的入站和出站访问规则。请看下面的截图。

![](img/ea3a3c242e3a6585d8816352970f0871.png)

![](img/7bece776cf7feba13a82d96a3b7e0248.png)

在远程机器上启用 WMI 防火墙规则后，我们可以按照上面的步骤 2 测试连接。要连接到远程机器，我们需要在默认名称空间(“`root\cimv2`”)前面加上我们需要连接的 PC 的 IP 或名称(“`\\192.168.1.2\root\cimv2`”)，并提供该远程机器的用户名和密码。

### WMI 的 PHP 扩展(。网)

用 WMI(或者更准确地说，。NET 功能)，我们将需要启用`php_com_dotnet.dll`。向`php.ini`添加一行，如下所示:

```
extension=php_com_dotnet.dll
```

并重新启动 web 服务器。

**注意:** `php_com_dotnet.dll`是 Windows 才有的扩展。这意味着我们必须在类似 WAMP 的环境中运行 WMI 调用 PHP 文件，而不是在*nix 环境中。当然，我们将通过 WMI 管理的机器都需要基于 Windows。

### 对 WMI 的进一步了解

做了所有必要的准备后，在我们开始用 PHP 编写 WMI 之前，我们真的需要回到我们之前提出的基本问题:**有哪些“信息”可用？**

我们可以期望 WMI 提供关于 BIOS、CPU、磁盘、内存使用等的信息。但是这些信息是如何呈现的呢？

除了深入研究提供的官方文档，让我们再次打开`wbemtest`对话框并连接到我们的本地机器。在`WMI Tester`对话框中，点击`Enum Classes...`按钮，弹出如下对话框:

![](img/bd890c6e4ccd6f2cb560cae48d2e98c7.png)

在此对话框中，不要在文本框中输入任何内容，选择`Recursive`并点击`OK`。它应该会弹出另一个对话框，如下所示:

![](img/e1dfaaaaa9f7f02ef59ffab99ec2744c.png)

这是一个非常长的列表(我的 Windows 8.1 PC 中有 1110 个对象)。您的电脑可能会给出不同的列表，但应该与此列表大致相同。请花些时间浏览一下，看看 WMI 提供的课程名称。例如，在上图中，我们突出显示了一个类`Win32_LogicalDisk`。这包含与机器逻辑磁盘相关的所有信息。要更深入地了解该类提供的内容，请双击该类，将会出现另一个`Object editor`对话框:

![](img/92624370177c43ffe5524097e0b3c21b.png)

仔细看看属性面板。这里列出的所有属性都是我们可以检索的。例如，`VolumeName`将是我们为逻辑磁盘分配的名称。

WMI 的 Win32 类有很多条目需要查看。一些最常用的是:

*   计算机系统硬件类，包括冷却设备、输入设备(键盘、鼠标等)、大容量存储、主板、网络设备、打印、视频和显示器等。
*   已安装的应用程序类，包括字体等。
*   操作系统类，包括驱动、内存、进程、注册表、用户等。
*   性能计数器类，包括所有与性能相关的类。
*   等等等等。

我们现在对 WMI 类的结构及其相关属性有了更清晰的了解。

### 用 PHP 编程 WMI

下面的代码片段显示了 IP 192.168.1.4 上远程机器的逻辑磁盘的一些基本信息:

```
<?php
    $pc = "192.168.1.4"; //IP of the PC to manage

    $WbemLocator = new COM ("WbemScripting.SWbemLocator");
    $WbemServices = $WbemLocator->ConnectServer($pc, 'root\\cimv2', 'your account', 'your password');
    $WbemServices->Security_->ImpersonationLevel = 3;

    $disks =    $WbemServices->ExecQuery("Select * from Win32_LogicalDisk");

    foreach ($disks as $d)
    {
        $str=sprintf("%s (%s) %s bytes, %4.1f%% free\n", $d->Name,$d->VolumeName,number_format($d->Size,0,'.',','), $d->FreeSpace/$d->Size*100.0);

        echo $str;
    }
```

在我的系统上，上面会打印出类似这样的内容:

```
C: (System) 104,864,059,392 bytes, 60.4% free
D: (Data) 209,719,963,648 bytes, 84.3% free
E: (Misc) 185,521,188,864 bytes, 95.3% free
```

这是一个非常简单的例子，但它奠定了 PHP WMI 程序的基本结构和流程。

首先，创建一个类型为`WbemScripting.SWbemLocator`的 COM 对象实例。

然后，将通过`ConnectServer`方法建立与 PC 的连接。这个方法调用的四个参数是不言自明的。最后，我们需要将安全模拟设置到适当的级别。级别 3 是 WMI 脚本的推荐级别。关于等级的详细解释记录在[这里](http://msdn.microsoft.com/en-us//library/system.security.principal.tokenimpersonationlevel.aspx)。第三级的意思是“T1”，意思是:

> 服务器进程可以在其本地系统上模拟客户端的安全上下文。服务器不能在远程系统上模拟客户端。

简而言之，我们的脚本(以及我们创建的服务实例)正在用提供的帐户/密码“模拟”用户。完全符合我们的需要。

请注意，上面的代码是创建远程 COM 连接来管理远程 PC 的方法。要管理本地 PC，语法会稍有不同，但不会太多:

```
<?php
    $pc = "."; 
    $obj = new COM ("winmgmts:\\\\".$pc."\\root\\cimv2");

    $disks =  $obj->ExecQuery("Select * from Win32_LogicalDisk");
    // Rest of the code is the same as previous remote connection sample
```

这稍微简单一些，因为我们不需要提供凭证和模拟，但是这是基于运行这个代码片段的用户拥有管理员特权的假设。

为了获得类及其相关数据，我们使用了 WQL (WMI 查询语言)语句。这非常类似于我们向 MySQL 服务器发出的 SQL 语句，但是在这种情况下，我们从 WMI 检索数据。`Win32_LogicalDisk`是 WMI 中的一个“表”,它存储与逻辑磁盘相关的所有信息。要从其他*表*中访问数据，请使用如上所示的`Query Result`对话框中列出的名称。这也允许我们过滤结果。例如，`Select * from Win32_LogicalDisk where size > 150000000000`只会返回那些大小超过 150G 的逻辑设备(大概)。

如果成功，`ExecQuery`语句将返回一个`variant`类型的对象。一个缺点是，如果我们试图`var_dump`该对象，PHP 将简单地打印出类似`object (variant) #3...`的内容。当我们试图`var_dump`变量`$d`时，也会发生同样的事情。输出中实际上没有对进一步编程有用的东西。

实际上，我们只需要知道对象是可迭代的。在这种情况下，当我们使用一个`foreach`循环时，每个`$d`实例将保存一个对逻辑磁盘的对象引用。然后，我们可以用熟悉的`->`符号访问该逻辑磁盘实例中的属性。如上所示，可以在该特定类的`Object editor`对话框中找到属性列表。

确保正确拼写类名(`Win32_LogicalDisk`)和属性名(如`Size`、`Name`)。Windows 不区分大小写，但是如果我们提供了错误的名称，将会抛出并返回一个错误。

正如我们前面提到的，WMI 编程也可以用其他语言来完成，比如 C#、VB Script 等语言。然而，WMI COM 接口是这样一个动态接口，我们不能指望这些语言中的任何一种提供代码完成提示来轻松访问所有属性。我们必须依靠上面显示的对话。

帮助程序员的一个解决方案是用必要的方法将每个 WMI 类进一步封装成一个 PHP 类。这应该是一个非常简单的任务，我将把它留给那些感兴趣的人来玩。

#### 结论

WMI 是一个强大的工具，掌握着 Windows 操作系统最隐秘的秘密。在一个拥有同类 Windows 机器的大规模网络中，我们可以依靠 WMI 来检索这些重要信息，并帮助系统管理员更好地管理所有机器。

在这篇文章中，我们只讨论了 WMI 和 PHP WMI 编程的基础，但是已经为以后的工作打下了基础。

如果你想看更详细的 WMI 教程，请在下面留下你的评论！

## 分享这篇文章