# 使用 Windows 7 XP 模式在同一台计算机上运行 IE6、IE7 和 IE8

> 原文：<https://www.sitepoint.com/ie6-ie7-ie8-win7-xp-mode-2/>

## 硬盘差异

VMware 有一个很好的工具，可以拍摄虚拟机的快照并创建多个克隆。每个克隆的虚拟机都是独立的，但都继承自同一个基本安装。VirtualBox 也允许克隆，尽管没有那么复杂。Virtual PC 提供了一种称为硬盘“差异”的功能，允许您从现有的硬盘映像创建任意数量的虚拟机。不幸的是，对原始磁盘映像的任何更改都会破坏所有继承的虚拟机。但是，我们可以使用差异从我们的 XP 模式磁盘映像创建两个新的虚拟机。一个虚拟机将用于 IE6，另一个将用于 IE7。

1.  首先，创建一个新的虚拟机。从启动虚拟机 > 程序 > Windows 虚拟机 > 虚拟机然后点击工具栏上的创建虚拟机，出现如图[图 4 所示的对话框，“虚拟机名称和位置”](#fig_vm_name "Figure 4. VM Name and Location")。

    **图 4。虚拟机名称和位置**

    ![VM Name and Location](img/ce92bf6370178a58504f3fed04467e37.png)

    输入新虚拟机的名称和位置，然后单击下一步的。

2.  下一个屏幕询问您的虚拟机需要多少内存。256MB 对于刚运行 IE 应该够用了；输入金额，点击下一步。

3.  下一个屏幕，如[图 5 所示，“配置您的虚拟机硬盘”](#fig_vmhdd_config "Figure 5. Configuring your VM hard disk")，要求您添加一个虚拟硬盘。选择使用高级选项创建虚拟硬盘，点击下一步。

    **图 5。配置您的虚拟机硬盘**

    ![Configuring your VM hard disk](img/984a52cb5acf03434bcc258023eb9d36.png)

    然后从[图 6“选择差分”](#fig_hdd_diff "Figure 6. Choose Differencing")中显示的选项中选择差分选项。

    **图 6。选择差分**

    ![Choose Differencing](img/615f24a0019c4b563ab0451d686ccb59.png)

    点击下一个两次，然后选择你原来的 XP 模式硬盘。这是一个名为`Windows XP Mode.vhd`的文件，位于您最初安装 XP 模式的位置。最后，点击创建保存虚拟机并返回虚拟机对话框。

4.  在启动新虚拟机之前，右键单击其图标并选择设置来更改特定于您的 PC 或设置的任何参数。

您的新 VM 现在已经准备好了，但是记住您应该创建两个:一个用于 IE6，一个用于 IE7。必要时重复上述四个步骤。一旦创建了两个虚拟机，我建议将 XP 模式的硬盘文件设为只读(`Windows XP Mode.vhd`)。如果您意外地启动了原来的 XP 模式映像，这将确保文件不会被修改。

## 从 Windows 7 运行 IE6

按照以下步骤从 Windows 7 桌面运行 IE6:

1.  双击虚拟机文件夹中的图标启动 IE6 虚拟机(开始 > 所有程序 > Windows 虚拟机 > 虚拟机)。你需要用你在安装 XP 模式时输入的 XPMUser ID 和密码登录(我说过你需要记住它！).

2.  现在，您可以对 XP 和 IE6 进行任何相关的配置更改。例如，开发人员应该安装[微软 Internet Explorer 开发人员工具栏](http://www.microsoft.com/downloads/details.aspx?familyid=e59c3964-672d-4511-bb3e-2d5e1db91038&displaylang=en)——它不是 Firebug，但总比没有好。

3.  接下来我们需要复制 IE 快捷方式。右键点击 XP 开始按钮，选择浏览所有用户。您复制到此文件夹中的任何快捷方式都将复制到 Windows 7“开始”菜单中。因此，必要时创建或复制一个 IE 快捷方式，就像我在[图 7 中所做的，“创建一个 IE6 快捷方式”](#fig_ie6_shortcut "Figure 7. Creating an IE6 shortcut")。

    **图 7。创建 IE6 快捷方式**

    ![Creating an IE6 shortcut](img/5bb601fbf35e95cc5d16ab23c258ab5b.png)

    片刻之后，一个包含 IE6 图标的新文件夹将出现在 Windows 虚拟 PC 开始菜单中。

4.  从 XP 开始菜单中，选择注销。关闭窗口，但从下拉列表中选择休眠。

5.  要从 Windows 7 启动 IE6，请单击开始 > 程序 > Windows 虚拟机，然后找到新创建的虚拟机文件夹，并单击 IE 图标。第一次运行 IE 时，会提示输入 XPMUser 密码，如图[图 8，“XPMUser 密码提示”](#fig_xpmuser_prompt "Figure 8. XPMUser password prompt")—输入，勾选记住我的凭证，点击确定。

    **图 8。XPMUser 密码提示**

    ![XPMUser password prompt](img/019415320c90bbb6a698423df684ac51.png)

    假设你让你的虚拟机休眠，IE6 会在你每次点击它的图标的几秒钟内启动。

## 从 Windows 7 运行 IE7

运行 IE7 的步骤与上面的 IE6 相同，除了您应该:

*   确保启动您创建的另一个虚拟机，并且

*   第二步，[下载并安装 IE7。](http://www.microsoft.com/downloads/details.aspx?displaylang=en&FamilyID=9ae91ebe-3385-447c-8a30-081805b2f90b)

现在，您可以从同一个 Windows 7 桌面运行 IE6(从一个虚拟机磁盘)、IE7(从另一个虚拟机磁盘)和 IE8(作为本机应用程序);证据在[图 9 中，“IE6、IE7、IE8 都在一个桌面上”](#fig_all_the_ies "Figure 9. IE6, IE7, and IE8 all together on one desktop")。

**图 9。IE6、IE7 和 IE8 都在一个桌面上**

![IE6, IE7, and IE8 all together on one desktop](img/3090aa8571fdc20021124283f7545a80.png)

有人注意到 IE6 的界面有多简单好吗？sshh—*不要告诉任何人！*

## 您是否在考虑升级 Windows 7？

XP 模式是我买 Windows 7 专业版的唯一原因。对于 web 开发人员来说，它消除了常规 IE 测试的障碍，节省了等待虚拟机启动的无数时间，并允许您尽早发现渲染问题。这令人印象深刻，当然也让额外的花费物有所值。但是，要注意选择正确的版本。

你会尝试 XP 模式吗？它会减少你的 web 开发和测试时间吗？请留下您的评论，让我知道您的进展。

**Go to page:** [1](https://sitepoint.com/net-web-services-5-steps) | [2](https://sitepoint.com/net-web-services-5-steps-2/)

## 分享这篇文章