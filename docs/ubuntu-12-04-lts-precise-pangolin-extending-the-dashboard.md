# Ubuntu 12.04 LTS 精确穿山甲:延伸仪表盘

> 原文：<https://www.sitepoint.com/ubuntu-12-04-lts-precise-pangolin-extending-the-dashboard/>

Unity desktop 允许您在本地和远程搜索信息，在本文中，我们将向您展示如何安装一系列当前的镜头和望远镜，这将允许您扩展您的 dash，并最大限度地利用 Ubuntu 12.04 LTS 精确穿山甲。

所以让我们开始吧…

## 那么什么是镜头和瞄准镜呢？

镜头是为 Unity dash 提供额外搜索功能的功能。可以把它想象成一块空白的画布或者一个容器，在这个容器中会发生特定主题或类别的搜索事件。另一方面，示波器将为现有的透镜提供新的源或分支。例如，一个视频镜头可以让你搜索视频文件，但通过添加一个 YouTube 范围，这将使你能够在本地和 YouTube 上搜索视频；就像 Flickr Scope 为现有的照片镜头提供额外的功能一样。

> 您应该知道，安装新镜头和新望远镜后，桌面不会自动检测它们。在您退出当前会话并重新登录以重新启动 Unity 界面之前，它们不会出现。

### 烟灰缸镜头

**Unity AskUbuntu 镜头**搜索 AskUbuntu 网站。如果你有一个问题，一个悬而未决的问题，或者你只是想问一个简单的问题，那么你可以使用这个镜头直接从你舒适的桌面上查找信息:

![](img/7758246ae6f65d49a9625f4fca7921c0.png)

要安装 **Unity AskUbuntu Lens** ,要么启动 Ubuntu 软件中心并像安装其他软件一样安装，要么打开终端并键入:

```
sudo apt-get install unity-lens-askubuntu
```

### 扩展您当前的工具

你喜欢社交网络吗？如果是这样，那么你可能要考虑安装**Unity gwiber 镜头**。基于 Gwibber(默认社交网络应用程序)的功能，这款镜头可以让您搜索、查看和访问您最喜爱的社交网络应用程序上发布的帖子、回复、消息甚至图像。

要安装此镜头，请打开终端并键入:

```
sudo apt-get install unity-lens-gwibber
```

**Unity 洋泾浜镜头**怎么样？Pidgin 的用户将知道，它是一个多功能的即时消息客户端，能够同时处理多个网络，通过使用这个镜头，诸如 AIM/ICQ、Yahoo、MSN 等网络都暴露在 dash 之下，只需打开终端并键入:

```
sudo add-apt-repository ppa:scopes-packagers/ppa
sudo apt-get update && sudo apt-get install unity-lens-pidgin
```

同时， **Dash-based calculator** 是一个使用 GCalctool(默认计算器)的 scope，能够处理您日常使用的典型数学运算…

![](img/2ab62674b54dbf883578886c9814cebe.png)

要安装此镜头，请打开终端并键入:

```
sudo add-apt-repository ppa:scopes-packagers/ppa
sudo apt-get update && sudo apt-get install unity-lens-utilities unity-scope-calculator
```

### 分支

你在寻找不同的东西吗？那为什么不从 **Unity 平面设计镜头**开始呢。这款实用的套装配备了 DeviantArt、Dribble 和 IconFinder 的几款瞄准镜，让您可以找到图标、画笔、调色板和更多日常创意灵感:

![](img/28ed967fb39e1dcd7d1905e0d8e278bf.png)

要安装此镜头，请打开终端并键入:

```
sudo add-apt-repository ppa:scopes-packagers/ppa
sudo apt-get update && sudo apt-get install unity-lens-graphicdesign unity-scope-colourlovers unity-scope-deviantart unity-scope-dribbble unity-scope-iconfinder
```

或者，Unity WordReference Lens 将在线词典发送到 dash，从而消除了访问网站的需要。

要安装此镜头，请打开终端并键入:

```
sudo add-apt-repository ppa:atareao/lenses
sudo apt-get update && sudo apt-get install unity-lens-wordreference
```

同样，我们有**维基百科统一镜头**:

```
sudo add-apt-repository ppa:scopes-packagers/ppa
sudo apt-get update && sudo apt-get install unity-lens-wikipedia
```

对于新闻爱好者(像我一样)，我们有 **Unity 新闻镜头**:

![](img/10b27c3ce1cd4e2194b3a282b37a6045.png)

要安装此镜头，请打开终端并键入:

```
sudo add-apt-repository ppa:scopes-packagers/ppa
sudo apt-get update && sudo apt-get install unity-lens-news
```

对于那些对金融信息感兴趣的人，我们邀请您使用 **Unity Stock Ticker Lens** ,它只需输入以下内容，就可以报道来自著名金融来源的各种经济符号和头条新闻:

```
sudo add-apt-repository ppa:scopes-packagers/ppa
sudo apt-get update && sudo apt-get install unity-stock-ticker-lens
```

最后， **Unity Cooking Lens** 通过提供从冰淇淋到汤的大量食物食谱，同时提供所需烹饪时间和难度的初步知识，将家庭风味引入桌面。

要安装此镜头，请打开终端并键入:

```
sudo add-apt-repository ppa:scopes-packagers/ppa
sudo apt-get update && sudo apt-get install unity-lens-cooking
```

> 请记住，如上所述，桌面不会自动检测新的镜头和范围后，你安装它们。在您退出当前会话并重新登录以重新启动 Unity 界面之前，它们不会出现。

### 最后订单

我希望这篇文章是对镜头和望远镜世界的有益介绍，但在我离开之前，我想提一下 **Unity 二进制时钟镜头**。这是一个以二进制格式显示时间的时钟。它非常有趣，是所有时间爱好者和钟表爱好者(或者只是那些想以非常不同的方式看待时间的人)的必备之物。

要安装此镜头，请打开终端并键入:

```
sudo add-apt-repository ppa:markjtully/ppa
sudo apt-get update && sudo apt-get install unity-binaryclock-lens
```

我希望你继续享受使用 Ubuntu 12.04 LTS 精确穿山甲。直到下一次…

## 分享这篇文章