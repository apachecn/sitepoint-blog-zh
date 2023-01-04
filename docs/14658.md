# OS X 开源

> 原文：<https://www.sitepoint.com/open-source-on-os-x/>

当苹果发布 OS X 时，它成为了将 Unix 的强大功能与尖端的图形用户界面和桌面结合在一起的标杆。凭借其对微软 Office 的支持和与 Windows 的互操作性，OS X 没有成为 Linux 世界一直在寻找的“杀手级桌面”是一个奇迹。然而，那是另一个讨论。

苹果通过发布 OS X、达尔文的 Unix 基础，并使其能够在 X11 环境中运行，加入了开源社区(在苹果正在进行的众多其他开源项目中—[见此处](http://developer.apple.com/darwin))。这给 Macintosh 带来了 Linux 和 Unix (*nix)应用，有点像 Cygwin 给 Windows 用户带来了运行 X11。主要区别在于 OS X 的核心内核是一个修改过的 BSD Unix 内核，而 Cygwin 在 Windows 内核上模拟一个*nix 环境。

Fink 更进一步，在 OS X 上移植了 3,583 * nix 应用程序(截至今天早上)，在 Fink 中运行。该平台可以通过两种方式安装，要么从源代码编译，要么通过一个方便的二进制包(在 Debian Linux 主题中)。

有了 Fink，你可以在 X11 环境中完全发挥作用，加载 X 窗口和 X 应用程序以及扩展的终端应用程序集。这进一步将 OS X 定位为一个全面的开发环境，在本地主机上有一个丰富的服务器测试工具集，并且可以完全访问您最喜欢的*nix 应用程序的完整产品组合。

为了使过渡更加容易，Fink Commander，也在 Sourceforge 找到了[，作为 Fink 包装的图形前端。它在 Fink 提供的命令行工具上放了一个 GUI，用于在你的 OS X 机器上下载和编译*nix 应用。](http://finkcommander.sourceforge.net/)

我发现另外一个网站对我在 OS X 上使用 Fink 很有帮助，它包括了关于 Fink 和 Panther(10.3 . x)–[Sao ' s Place–Fink 和苹果 OS X](http://homepage.mac.com/sao1/fink/) 的主题。

## 分享这篇文章