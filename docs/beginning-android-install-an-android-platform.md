# 开始 Android:安装一个 Android 平台

> 原文：<https://www.sitepoint.com/beginning-android-install-an-android-platform/>

第二课:安装安卓平台

谷歌提供了各种 Android 平台，可以通过 SDK 的 SDK Manager 工具进行安装。第 2 课向您展示了如何使用`SDK Manager`来安装 Gingerbread 平台(版本 2.3.3)和平台工具。

* * *

注意姜饼是 Android 2.3 和次要更新(如 2.3.3)的代号。谷歌习惯于用甜点来命名它的平台。

* * *

## 启动 Android SDK 管理应用程序

启动 SDK Manager(或者安卓，基本工具之一)。过一会儿，您会看到这个应用程序的 Android SDK 管理器窗口，内容类似于图 2 所示。

[![](img/3c9d11ac9e5e05e9b468d0da0d0ed7cc.png "figure2")](https://www.sitepoint.com/wp-content/uploads/2012/03/figure2.jpg)

图二。Android SDK 管理器允许您安装或删除 Android 平台和其他项目。

￼￼￼￼￼￼￼￼￼￼￼￼￼
Android SDK Manager presents a user interface organized into a menubar and a content area. The menubar presents Packages and Tools menus:

*   Packages 菜单提供菜单项，用于显示更新的或新的包(Android 组件)、已安装的包和过时的包的某种组合。它还提供了菜单项，用于按 API 级别或存储库对包列表进行排序，以及用于重新加载内容区域中显示的包列表。
*   “工具”菜单显示用于管理 Android 虚拟设备(使用 Android 模拟器运行的设备配置)和附加站点的菜单项，用于指定与代理(作为中介位于网络之间的计算机或应用程序)和其他项目相关的选项，以及显示有关信息。

内容区域显示了 SDK 的路径、软件包信息表、用于确定要显示的软件包类别的复选框、用于选择软件包排序方式的单选按钮、用于安装和删除软件包的按钮，以及一个进度条，该进度条显示了 SDK 管理器扫描 Google 服务器以获取软件包信息的进度(在启动时以及当从软件包中选择重新加载时)。

## 安装姜饼 2.3.3 和平台工具

取消选中 Android 4.0.3 (API 15)，它会自动取消选中其层次结构下的所有复选框。相反，检查 Android 2.3.3 (API 10)，它会自动检查下面的所有复选框，并检查 Android SDK 平台工具。您应该得到如图 3 所示的用户界面。

[![](img/b71c3830856489bf235a8189918b980b.png "figure3")](https://www.sitepoint.com/wp-content/uploads/2012/03/figure3.jpg)

图 3。Android SDK 管理器的安装按钮显示有 22 个软件包需要安装。

Install 按钮标识了要安装的 22 个软件包。此计数包括“Extras”部分(在窗口底部)中的 Google USB 驱动程序包。如果你没有真正的 Android 设备，并计划通过 Android 模拟器进行所有的开发，你可以取消选中这个包。然而，你最好检查一下。

单击 Install，您将看到图 4 所示的选择要安装的包对话框。

[![](img/06d98617cd89a4ad844fecc26ce5bd12.png "figure4")](https://www.sitepoint.com/wp-content/uploads/2012/03/figure4.jpg)

图 4。绿色复选标记出现在将要安装的软件包旁边。

“选择要安装的软件包”对话框显示软件包列表，以及列表右侧窗格中突出显示的软件包的信息。“接受”和“拒绝”单选按钮允许您接受或拒绝突出显示的包。您可以通过选择 accept all 单选按钮来接受所有包。

名称旁边带有绿色勾号的包的“接受”按钮被选中。出现问号时，既不会选择接受按钮，也不会选择拒绝按钮。谷歌 USB 驱动包当然也是如此。您需要突出显示这个包，并选择它的 Accept 单选按钮，以确保它已经安装(假设您想要安装这个组件)。

单击“安装”安装所有选中的软件包。图 5 显示了 Android SDK 管理器日志窗口，它出现在 Android SDK 管理器窗口上，并提供了安装细节。它还显示一个进度条，显示安装所有选定软件包的进度。

[![](img/715fbdc22329eb2430ecadb7b87f16a5.png "figure5")](https://www.sitepoint.com/wp-content/uploads/2012/03/figure5.jpg)

图 5。下载进度同时出现在 Android SDK 管理器日志窗口和 Android SDK 管理器窗口的底部。

图 5 包括一个“停止 ADB 服务器失败(代码-1)”消息。ADB 代表 Android Debug Bridge，这是一个由客户端和服务器程序组成的工具，让您可以控制和连接您的 Android 设备。出现此消息是因为 ADB 服务器此时没有运行。

在安装过程中，您可能会遇到图 6 所示的 ADB 重启对话框。

[![](img/ec3f76b1d433488065e7ae88daa884f0.png "figure6")](https://www.sitepoint.com/wp-content/uploads/2012/03/figure6.jpg)

图 6。单击“否”按钮，因为此时没有必要重启 ADB 服务器。

如果你以前没有为 Android 开发过，ADB 服务器将不会运行，现在重启也没有意义。因此，单击“否”按钮。

安装完成后，您会看到一个“完成加载软件包”Android SDK 管理器和 Android SDK 管理器日志窗口底部的消息。关闭 Android SDK 管理器日志。
由于 Android SDK Manager 的用户界面有些 bug，它的安装按钮继续显示需要安装软件包。您可以通过取消选中然后选中“更新/新”和“已安装”复选框来消除这种混淆。

图 7 显示了应该安装的包。除非你打算在真正的 Android 设备上安装和测试你的应用程序，否则你不必安装谷歌 UDB 驱动程序。

[![](img/2ab5b4adc40a4a41c1791c4ba2ed789c.png "figure7")](https://www.sitepoint.com/wp-content/uploads/2012/03/figure7.jpg)

图 7。选择“已安装”复选框，仅查看已安装的软件包。

## 复习问题

以下复习问题有助于测试您对第二课材料的掌握程度:

*   如何启动 Android SDK Manager 应用程序？
*   描述这个应用程序的 Android SDK 管理器窗口。
*   这个应用程序的包菜单让你完成什么？它的工具菜单让你完成什么？
*   如何选择要安装的软件包？如何只查看已安装的软件包？
*   Android SDK 管理器日志窗口显示什么？
*   亚行是什么？

## 分享这篇文章