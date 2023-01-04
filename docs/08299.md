# Ubuntu 12.04 LTS 精确穿山甲:桌面必备

> 原文：<https://www.sitepoint.com/ubuntu-12-04-lts-precise-pangolin-desktop-essentials/>

在以前的一篇文章中，我向你展示了安装 Ubuntu 12.04 LTS 是多么容易，现在你应该已经熟悉了你的桌面是如何工作的，你的文件存储在哪里，以及一些使 Ubuntu 12.04 LTS Precise 穿山甲成为一个真正伟大的操作系统的基本概念。

总的来说，Ubuntu 也预装了一系列优秀的软件，并且为了安装软件，Ubuntu 有自己的软件中心。让你即时访问数以千计的免费和开源应用程序、游戏和杂志确实让事情变得很容易，但有这么多可供选择，对于我们这些想要“立即行动”的人来说，这可能有点难以承受(如果不是很耗时的话)。所以问题仍然是，我从哪里开始？

要充分利用你的电脑，简单来说就是你能运行什么软件，能听什么音乐，能看什么视频，能玩什么游戏。你想要拥有自己的桌面，让它做一些奇妙的事情，你想要熟悉的东西，但你也想要不同的东西。选择可能是一个两难的问题，但本文的目的是通过一个安装后“提示和技巧”的清单，立即交付一个全功能的桌面。我不仅会向你展示软件中心过程的快捷方式，还会向你展示如何安装一整套屏幕保护程序，删除来宾帐户，以及安装经典的 Gnome 界面——让你有足够的时间在闲暇时发现 Ubuntu 软件中心隐藏的瑰宝。

所以让我们开始吧…

## 其他软件来源。

在我们开始之前，我们必须确保我们的软件来源列表是最新的。

只需打开 Ubuntu 软件中心图标(可在启动器中找到，或在“dash”的搜索栏中搜索“Ubuntu 软件中心”)，然后:

*   将鼠标放在顶栏上(靠近您的用户名)，选择编辑>软件源
*   从出现的窗口中选择“其他软件”。
*   勾选“规范合作伙伴”和“规范合作伙伴(源代码)”旁边的复选框。该资料库包括对 Skype 和 Adobe Reader 等软件的访问。
*   选择“更新选项卡”来配置您的更新计划。建议每周或每天安排一次。
*   完成后单击“关闭”,这样操作系统就可以用新的设置进行自我更新。

> **请注意，**
> 为了这篇文章的目的，我将不处理任何个人包存档(PPA)的使用。也称为 PPAs，这些档案用于帮助测试预发布或专业软件。

## 使用终端。

软件安装的简要介绍可以在[这里](https://help.ubuntu.com/community/InstallingSoftware)找到，但是为了本文的目的，我们将使用终端。

> 根据单一定义，终端类似于 Microsoft Windows 中的命令行，但不同之处在于它可以在系统上执行任何操作。这是一个非常强大的应用程序，但是经过一点实践之后，你会发现它是一个非常容易使用的工具。

您可以使用以下方法之一来查找终端:

*   破折号>搜索终端
*   仪表板>更多应用程序>附件>终端
*   但我最喜欢的是简单地使用 Ctrl + Alt + T 作为键盘快捷键。

关于这个主题的有用指南可以在[这里](https://help.ubuntu.com/community/UsingTheTerminal)和[这里](https://help.ubuntu.com/community/AptGet/Howto?action=show&redirect=AptGetHowto)找到，但是一旦终端打开，你会看到类似下面的东西:

```
username@ubuntu:~$
```

让我们离题一下，做一个简单的实验，试试 a 命令。键入以下内容(与您看到的完全一样)或使用复制/粘贴，完成后按“return/enter”键:

```
date
```

通过这样做，你只是要求系统告诉你时间和日期。显然，在您的系统上，确切的时间和日期会有所不同，但您现在应该会看到类似这样的内容:

```
Sat Apr 28 15:35:35 BST 2012
```

在本文中，这是我们将会遇到的最复杂的情况:-)

当然，我会鼓励您在闲暇时阅读背景信息，但这不是必需的，因为我们要做的只是运行以下类型的命令来执行安装请求:

```
sudo apt-get install <package_name>
```

或者安装我们将使用的多个软件包:

```
sudo apt-get install <package_name_1> <package_name_2> <package_name_3>
```

相反，或者如果你犯了一个错误，你需要删除一个软件包，只需使用以下命令格式(用你想删除的软件的名字替换<package_name>):</package_name>

```
sudo apt-get remove <package_name>
```

最重要的是要记住。一开始可能会有点尴尬和奇怪(我只是向您介绍了这个主题)，但是通过这样做，您不仅会对 Linux 操作系统有一个真正的感觉，而且您会发现终端是完成工作的最快和最有效的方式。

## 更新您的系统

因此，没有进一步的犹豫，让我们直接使用终端来更新我们的系统。这些命令与我们刚刚讨论的有些不同，但是我确信它们是不言自明的:-)这些命令是不言自明的

打开终端并键入:

```
sudo apt-get update && apt-get upgrade
```

您将被要求验证自己的身份，因此只需输入您的密码并点击“return/enter”键。
“终端”现在将对您的系统进行检查，查看是否有可用的更新。

如果没有可用的更新，则不需要进一步的操作，但是如果有可用的更新,“终端”会要求您通过简单地使用 Y(是)或 N(否)来确认更新，以完成任务。

一个大的更新可能需要一段时间才能完成，但就是这样，更新您的系统真的很简单，您可以在任何需要的时候重复这些步骤。

## 构建您的桌面

现在我们将注意力转向构建桌面。

因此，让我们从安装一组有用的实用程序开始，返回到终端并键入:

```
sudo apt-get install flashplugin-installer gsfonts-x11 unace rar unrar p7zip-rar p7zip zip unzip sharutils uudeview mpack lha arj cabextract file-roller acroread geany geany-plugins filezilla calibre gparted
```

有些软件包名称您可能会很熟悉，而有些可能不熟悉，但我们刚刚要求系统安装一组工具，包括 Adobe Flash 插件和相关字体，(acroread) Adobe Acrobat Reader，一系列 zip/压缩实用程序，(gparted)磁盘分区工具，(calibre)电子书目录和查看器，(filezilla)FTP，以及(geany)共享较大 IDE 的一些功能的压缩文本编辑器。

同样，我们现在可以考虑安装一系列工具来完成带有全局菜单栏的 Libre Office，提供 PDF 功能并整合 Mozilla 插件，同时包括一个名为 DIA 的类似 Viso 的图表创建器。

```
sudo apt-get install dia libreoffice-pdfimport mozilla-libreoffice
```

现在，让我们安装基本的工具包，它将使您能够从源代码进行编译(您现在可能不想做这样的事情，但是通过安装这些实用程序，您将为自己将来提供选择):

```
sudo apt-get install build-essential checkinstall cdbs devscripts dh-make fakeroot libxml-parser-perl check avahi-daemon
```

最后，对于那些喜欢选项并想安装 Google Chromium 的人，在终端输入:

```
sudo apt-get install chromium-browser chromium-browser-l10n
```

这难道不比单独搜索和安装每个项目更快更容易吗:-)

## 为声音、视觉和设计而建

对于那些希望在终端类型中获得最佳视听回放和记录的用户:

```
sudo apt-get install smplayer libxine1-ffmpeg gxine mencoder mpeg2dec vorbis-tools id3v2 mpg321 mpg123 libflac++6 ffmpeg totem-mozilla icedax tagtool easytag id3tool lame nautilus-script-audio-convert libmad0 libjpeg-progs flac faac faad sox ffmpeg2theora libmpeg2-4 uudeview flac libmpeg3-1 mpeg3-utils mpegdemux liba52-0.7.4-dev libquicktime2 gstreamer0.10-ffmpeg gstreamer0.10-fluendo-mp3 gstreamer0.10-gnonlin gstreamer0.10-sdl gstreamer0.10-plugins-bad-multiverse gstreamer0.10-plugins-bad gstreamer0.10-plugins-ugly totem-plugins-extra gstreamer-dbus-media-service gstreamer-tools ubuntu-restricted-extras ttf-mscorefonts-installer regionset
```

这个重要实用程序的集合应该为您提供足够的媒体编解码器和播放器，让您开心一段时间:-)

对于那些希望使用桌面进行摄影和/或图形设计的用户，在终端类型中:

```
sudo apt-get install scribus gimp gimp-data gimp-plugin-registry gimp-data-extras inkscape pinta libraw-bin ufraw ufraw-batch and gimp-ufraw rawtherapee
```

这将安装 Scribus，Gimp，Pinta 和 Inkscape 以及一系列用于单反数码摄影的 RAW 文件查看器/编辑器/插件。

## 如何删除来宾帐户

Ubuntu 的“访客”账户是默认安装的，对于我们这些想要保持登录界面整洁的人来说，要移除它，我们需要做的就是:

在终端类型中:

```
sudo gedit /etc/lightdm/lightdm.conf
```

这将在您的文本编辑器中打开“lightdm”配置文件。在该文件的底部，在现有副本的下面添加以下一行:

```
allow-guest=false
```

现在保存文件，关闭并重启。
来宾帐户应该不再可见！

## 恢复屏幕保护程序

Ubuntu 12.04 LTS 精确穿山甲在标准安装中没有任何屏幕保护程序，如果你像我一样(并且想念他们)，可以这样修复。

运行以下命令删除现有的 gnome-screensaver 包:

在终端类型中:

```
sudo apt-get purge gnome-screensaver
```

接下来，运行以下命令安装“xscreensaver”软件包:

在终端类型中:

```
sudo apt-get install xscreensaver xscreensaver-gl-extra xscreensaver-data-extra
```

接下来，运行以下命令创建“xscreensaver 自动启动文件”。

在终端类型中:

```
sudo gedit /etc/xdg/autostart/screensaver.desktop
```

这将在文本编辑器中打开一个新创建的文件。只需将以下几行复制并粘贴到该文件中，然后单击“保存”:

```
[Desktop Entry]
Name=Screensaver
Type=Applicaton
Exec=xscreensaver -nosplash
```

关闭文件，重新启动，在“dash”中搜索屏幕保护程序，并根据需要进行配置，您将拥有您需要的所有屏幕保护程序…

## 退回到 Gnome 桌面环境

有人喜欢团结，有人不喜欢；(我有什么资格评判)但是如果你发现自己希望在 Ubuntu 上看到 Gnome 的样子，只需打开终端，一次输入一行命令(按下每行末尾的“enter/return”键):

```
sudo apt-get install gnome-session-fallback
sudo apt-get install indicator-applet-appmenu
sudo apt-get install gnome-tweak-tool
```

每个命令可能需要几分钟才能完成，但完成后，只需“重启”您的计算机，并在登录提示符下，使用小“齿轮”图标来更改用户界面风格。

你可以选择 Unity，Gnome Classic 或者 Gnome 3。

另一方面，如果你现在不想这样做，或者只是出于好奇，Gnome Classic 看起来是这样的:

![](img/7074477c4100de55da3f69ad2de9d124.png)

![](img/273bf86ff0689807c4822078d8b7920b.png)

而 Gnome 3 看起来是这样的:

![](img/10c079e86d18485b6b5482c6c2bbc8f7.png)

## 他们问的安全问题呢…

最后但同样重要的是，我们希望让我们的桌面环境安全无虞，为此我们需要一个防病毒解决方案。

在终端类型中:

```
sudo apt-get install clamav clamtk
```

这将安装 ClamAV，一个开源(GPL)防病毒引擎，旨在检测特洛伊木马，病毒，恶意软件和其他恶意威胁。

安装完成后，只需在“dash”中搜索 ClamTK，它非常容易使用，该软件也可以在 Windows 上运行，所有更新都是免费的。更多详情见[https://www.clamav.net/](https://www.clamav.net/)。

最后，为了完成我们的“桌面起点”,我们现在要安装一个防火墙。

非常感谢，Ubuntu 让这成为一个简单的选择，只需要你安装简单的防火墙。也被称为 UFW，它被设计得非常简单，并且打包了一个直观的用户界面。据开发商称，

> “开发 ufw 的原因是，我们想创建一个服务器级的防火墙实用程序，它更适合‘人类’。”

他们可能称之为简单(确实如此)，但不要低估它的力量。

在终端类型中:

```
sudo apt-get install gufw
```

当安装完成后，你可以在 UFW 下的“dash”中找到它，但现在这是我将离开你的地方，因为我将在以后回到 UFW 和防火墙配置的艺术。

如果你阅读了这篇文章，你应该(到现在)已经有了一个“应用程序”的桌面，足够你开始使用了。我想是时候让你上场了…

我希望你喜欢使用 Ubuntu 12.04 LTS 精确穿山甲。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Ubuntu Linux](https://learnable.com/courses/ubuntu-linux-65) 。

## 分享这篇文章