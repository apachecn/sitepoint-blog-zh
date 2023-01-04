# 使用 vnc2swf 进行轻量级屏幕投射

> 原文：<https://www.sitepoint.com/lightweight-screencasting-with-vnc2swf/>

考虑制作一个电影剧本？只要是免费的，又快又脏就行了吗？ [vnc2swf](http://www.unixuser.org/~euske/vnc2swf/) 可能正是您所需要的:

1.  设置 VNC 服务器。免费的用于 Windows 平台的 VNC 服务器(之前的 WinVNC)将在 Windows 平台上发挥作用，还有用于 Mac 平台的 OS xvnc(T2)。
2.  安装并运行 vnc2swf。你需要先设置 Python，但是你需要的所有东西都链接在这个网站上。一旦它运行，按 F9 开始记录你的桌面。
3.  完成后停止录制。将生成一个 SWF，您可以将其发布到 Web 上。
4.  使用配套的编辑工具砍掉不想保留的电影片段，然后添加单独录制的 MP3 音轨。

就这样，你得到了你的截屏！如果你不熟悉 Unix 命令，网站上的截屏演示可能有点难以理解，但在命令提示符下稍加修改，你应该可以相对容易地让它在 Windows 或 Mac OS X 上运行。

## 分享这篇文章