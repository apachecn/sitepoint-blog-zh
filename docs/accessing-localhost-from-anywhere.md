# 从任何地方访问本地主机

> 原文：<https://www.sitepoint.com/accessing-localhost-from-anywhere/>

你花了几个小时在本地电脑上工作，让新的网站设计看起来恰到好处。您的 CMS 已经很好地设置了示例内容，并且您希望在一些移动设备和平板电脑上进行测试。您的客户也想看一看——但是您没有时间将它全部迁移到公共服务器上，让他们快速浏览一下。

如果你是一名网站开发人员，你可能已经经历过很多次这种情况了。您可能花了很多时间熬夜将东西迁移到面向公众的服务器上，希望有更简单的方法。

对于那些疲惫的 web 开发人员，我在这里告诉你一个更简单的方法已经到来！事实上，有大量的应用程序和服务可以通过提供从 Web 到本地主机的访问来帮助简化这个过程。我将在本文中讨论的是:

*   [ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/)
*   [PageKite](https://pagekite.net/)
*   [局部隧道](https://github.com/localtunnel/localtunnel)
*   [boringproxy](https://boringproxy.io/)
*   [浏览器堆栈](http://www.browserstack.com/)

## 怎么可能从任何地方访问本地主机？

有可能通过[安全隧道](https://en.wikipedia.org/wiki/Tunneling_protocol)服务在线！这些服务为你提供了一个从互联网到你的机器的通道。

也就是说，它们提供一个可公开访问的 URL，监视该 URL 上的调用，并将这些调用转发到您的本地主机服务器。所有令人困惑的设置已经为您完成；您所需要做的就是安装一个应用程序并将其指向您的本地主机。

## 关于使用虚拟主机的说明

在我的本地主机上，我已经设置了虚拟主机。如果没有下面列出的每个服务的地址作为`ServerAlias`，我的服务器将无法正常运行。接下来是几个小时的混乱。如果你在同一条船上，给你的`httpd.conf`加一个`ServerAlias`，用你正在使用的服务的地址替换你在下面看到的 ngrok 地址:

```
<VirtualHost *:80>
  DocumentRoot "/Users/patcat/Web"
  ServerName mytestsite.test
  ServerAlias 350c0f8e.ngrok.com
</VirtualHost> 
```

## ngrok

ngrok 是一个用于 Windows、macOS 和 Linux 的应用程序，它可以创建一个隧道，但也允许您检查通过隧道的所有流量，并重放这些流量进行测试。

您可以免费使用基本功能，然后注册使用更多高级功能。为了使用自定义子域和保留域，您需要注册付费计划。否则，您将能够使用该服务，但是会得到一个随机的域，比如上面虚拟主机示例中的`350c0f8e.ngrok.com` URL。

Ngrok 截至 2021 年 5 月的计划如下:

![ngrok plans as of May 2021](img/3dffcef017c1c8112a5fed57edb521c4.png)

ngrok 的一个主要优势是它没有依赖性。您安装 ngrok 并运行它。其他什么都不需要。

### 如何使用 ngrok

去 ngrok.com，下载存档文件并解压到你想让 ngrok 在你电脑上存在的任何地方。下面是基本步骤，但是如果你想了解更多细节，我们还有另一篇文章关于“[如何使用 ngrok 共享本地开发站点](https://www.sitepoint.com/use-ngrok-test-local-site/)”

打开一个命令行终端，转到您将 ngrok 解压到的文件夹。运行以下命令，在端口 80 上创建到本地主机的隧道:

```
./ngrok http 80 
```

您将看到这样的屏幕:

![ngrok running on port 80](img/0053d55a988029fe11dd62f53752bdee.png)

在上面的例子中，我们得到了一个随机生成的 http://42e474ef9799.ngrok.com ngrok 地址的流量，它被转发到 127.0.0.1:80 上的本地主机。

在任何有互联网连接的设备上打开这个 URL，你就可以从网上访问你的本地主机了！

ngrok 提供的更独特的功能之一是检查过去的流量。为此，请在您的计算机上访问 [http://localhost:4040/](http://localhost:4040) 。您将访问显示入站请求的仪表板。您可以单击左侧的每个请求，并在右侧查看标题和其他数据的详细信息。我最喜欢的用法是查看请求返回的 JSON 数据。加载一个简单的 HTML 页面时，最基本的布局是这样的:

![Viewing ngrok inbound requests](img/c4ae9a4da62dd7a7acc9a457e544263c.png)

您甚至可以通过点击右侧的 **Replay** 按钮来重播请求。它会通过隧道为你重新运行一个请求。

### ngrok 的高级功能

**密码保护**允许您阻止随机的公众成员访问您的网站:

```
ngrok http -auth="username:password" 80 
```

当你不想记住随机生成网址时，可以使用自定义子域。如果你注册了一个付费的 ngrok 计划，你可以保留一个子域，这样别人就拿不到了。以下内容允许您在 nogophersinmytunnel.ngrok.com 上查看您的本地主机:

```
ngrok http -subdomain nogophersinmytunnel 80 
```

**自定义域名**适用于您不希望在隧道项目的 URL 中包含“ngrok”的情况。付费计划允许您使用自己的域名，而不需要使用 ngrok.com:

```
ngrok http -hostname="tunnel.yourdomain.com" 80 
```

您甚至可以通过隧道连接到本地网络上的一个 IP 地址，否则外部世界将无法访问该地址:

```
ngrok http 192.168.0.27:80 
```

或者，您可以为其他非 HTTP 服务创建隧道:

```
ngrok tcp 22 
```

您还可以做许多其他事情，从同时运行多个隧道到创建一个配置文件来保存所有这些设置以备将来使用。

要阅读所有可能的内容，请参见 [ngrok 的文档](https://ngrok.com/docs)。

## 风筝纸

PageKite 是一个基于 Python 的“基于动态隧道的反向代理”,可以在 Windows、macOS、Linux 甚至 Android 设备上工作！它与 ngrok 非常相似，但已经存在了相当长的时间，而且它似乎在更大范围的使用中经历了更多的考验。他们甚至让它与《我的世界》协议一起工作，允许人们在他们的本地机器上运行《我的世界》服务器。

可以报名免费试用一个月，2.5GB 的转账额度。之后，它有一个按需付费的系统(每月最低 4 美元，或者如果你使用免费的开源软件则免费)。在这种情况下，你需要付出更多才能得到更多，那些付出更多的人会得到更大的配额，更长的服务期限，自定义子域等等。

PageKite 似乎没有 ngrok 这样的流量检查员，但它确实有一些令人难以置信的功能，如内置防火墙，可以阻止对常见攻击目标的访问，如`/wp-admin`、`/xampp`、`phpMyAdmin`页面等。如果你不介意这些被公开，它可以被禁用。

他们甚至在澳大利亚的悉尼有一个前端中继服务器，所以这可能会为澳大利亚的开发者提供更快的速度。

### 如何使用 PageKite

去[PageKite.net](https://pagekite.net/)下载你的操作系统版本。对于 Windows 用户，您需要确保首先安装 Python。对于 macOS 和 Linux，您可以使用一个简单的 cURL 命令直接从命令行安装它。

下载后，运行此命令运行到本地主机服务器的隧道。您选择每次使用的特定子域(而不是 ngrok 中的随机分配)。我在这里选择了`hurrygetintothefancytunnel`:

```
pagekite.py 80 hurrygetintothefancytunnel.pagekite.me 
```

最近在我的 Mac 上，我需要在开始时用`python3`运行，所以如果上面的方法不起作用，试试看:

```
python3 pagekite.py 80 hurrygetintothefancytunnel.pagekite.me 
```

如果你还没有注册，它会自动运行并帮你注册。然后，您就可以让 localhost 开始运行了！

![PageKite running on port 80](img/8eb18b46dbbb4d773e7bb9545ee85359.png)

## PageKite 的高级功能

PageKite 有一些更令人印象深刻的附加功能。

一个例子:不需要网络服务器。如果您没有运行服务器，它有一个内置的 web 服务器，可以运行您的静态文件，如下所示:

```
pagekite.py /path/to/folder igotthattunnelvision.pagekite.me 
```

像 ngrok 一样，您可以通过密码来限制访问:

```
pagekite.py 80 terelekkayatuneli.pagekite.me +password/username=password 
```

或者通过 IP 地址限制访问:

```
pagekite.py 80 arcadefirecamethroughmywindow.pagekite.me +ip/1.2.3.4=ok +ip/4.5.6=ok 
```

如上所述，你甚至可以在手机上运行这一切。我安装了一个名为 [kWS](https://play.google.com/store/apps/details?id=org.xeustechnologies.android.kws) 的 Android 网络服务器，然后运行 PageKite 将其暴露在网络上:

![PageKite and kWS running on my phone](img/a127bc5fccc7b211180e047d427b5096.png)

![PageKite delivering a website from my phone](img/300847e779d80c6300d1c2b910d4ffe1.png)

这肯定不是我每天都要做的事情，但是看到这种事情现在可以只在智能手机上实现，真是太棒了！

就像 ngrok 一样，PageKite 可以一次做多个隧道，它支持拥有自己的域，并且支持为你的隧道设置一个配置文件。关于这方面的更多信息，请查看他们的[快速入门指南](https://pagekite.net/support/quickstart/)和更全面的[操作页面](https://pagekite.net/wiki/HowTo/)。

## 本地隧道

[localtunnel](https://github.com/localtunnel/localtunnel) 是一个隧道服务，最初构建在 Node 中，但也有基于 Go 和 C#/的。网络客户端。

### 如何使用本地隧道

您可以像大多数 npm 软件包一样在您的设备上全局安装它:

```
npm install -g localtunnel 
```

为了在端口 80 上启动到本地主机的隧道，您可以运行以下命令:

```
lt --port 80 
```

它会像 ngrok 和 PageKite 一样运行！它给了你一个类似于我得到的子域:`https://loud-ladybug-21.loca.lt`。

![localtunnel running on port 80](img/07542eed5f1b8ab10b2021ca75d4bfa6.png)

当第一次运行 localtunnel 时，您会收到一个友好的提醒，提示您此操作正在将您的资产向 Web 公开。就打**点击继续**。

![localtunnel friendly warning](img/6cc24bf680177e0d5827e4de97b85dca.png)

### 本地隧道的高级功能

在这里获得一个免费的自定义子域是完全可行的，只要这个子域没有被占用。要使用自定义子域，只需用`--subdomain`参数运行它:

```
lt --port 80 --subdomain platypusestunneltoo 
```

localtunnel 提供的一个非常方便的特性是一个节点 API，它允许您通过 JavaScript 生成 local tunnel，以便在自动化测试中使用:

```
const localtunnel = require('localtunnel');

(async () => {
  const tunnel = await localtunnel({ port: 3000 });

  // Your tunnel URL will appear as tunnel.url

  tunnel.on('close', () => {
    // Do something once the tunnel is closed
  });
})(); 
```

## 钻孔代理

boringproxy 是一个更新的选项，在麻省理工学院的许可下是完全免费和开源的！它的主要目的似乎是让人们更容易在自己的电脑上托管网站。它是一个单独的可执行文件，可以同时作为服务器和客户端。有一个适用于一系列 Linux 系统和 Windows 的可执行文件。macOS 被列为“未经测试”。我个人还没能让它在我的 Mac 上运行，但是如果你有一个 Linux 盒子或者一个 Raspberry Pi，这可能就是你的解决方案。都是用 Go 写的，对贡献者开放。

其特点简述如下:

*   在麻省理工学院许可下 100%自由开放源码
*   可以完全自主管理
*   具有内置的反向代理
*   支持自定义域/子域

### 如何使用 boringproxy

主要步骤在 [boringproxy 文档](https://boringproxy.io/installation/)中最容易遵循，因为不同的平台有所不同。

总的来说，它包括:

*   通过`curl`(例如`curl -LO https://github.com/boringproxy/boringproxy/releases/download/v0.6.0/boringproxy-linux-x86_64`)下载服务器实例
*   `chmod`下载的可执行文件(例如，`chmod +x boringproxy-linux-x86_64`
*   设置绑定到端口 80 和 443(例如，`sudo setcap cap_net_bind_service=+ep boringproxy-linux-x86_64`)
*   也在客户端设置可执行文件

boringproxy 创建者的这段视频讲述了如何做到最好:

[https://www.youtube.com/embed/-kACP0X6E-I](https://www.youtube.com/embed/-kACP0X6E-I)

<br>

### boringproxy 的高级功能

**自动 HTTPS 证书**通过 Let's Encrypt 为您自动管理。没有必要强调获得测试证书，因为它发生在幕后。

**自动设置用于配置**的 Web 界面，允许您管理用户、访问令牌和隧道。

您可以**轻松地隧道化 web 应用程序**。自己托管像 Etherpad 这样的 web 应用(如上视频所示)，给它们自己的子域，然后你可以从任何地方访问它们。

## 浏览器堆栈

[BrowserStack](http://www.browserstack.com/) 提供自动截图和虚拟机，以针对一系列设备和浏览器测试您的网站。如果您想访问 localhost 的原因是为了测试，BrowserStack 可能会让您感兴趣。

您可以使用上述方法在 BrowserStack 虚拟机中测试本地主机站点，但它也有一个针对 Chrome 和 Firefox 的浏览器扩展，允许您从它们的服务器访问您的本地主机。

然而，该功能仅适用于 BrowserStack 服务，公众无法使用。BrowserStack 提供免费试用，在此之后，您将需要支付月费才能使用这项服务。如果您访问 localhost 的唯一原因是进行设备测试，这将非常方便。

### 如何使用浏览器堆栈

报名参加在[BrowserStack.com](https://www.browserstack.com/)的试用。事实上，他们在自己的网站上很好地记录了本地测试的过程，所以如果你对这条路线感兴趣的话，[可以读一下这个](http://www.browserstack.com/local-testing)。

不过，最终，它将允许您在通过 Web 运行的虚拟机中测试本地主机站点，如下所示:

![BrowserStack viewing a page on my localhost](img/724d86aa0cf6d441606489ff13e2dd52.png)

## 播放收藏夹

在玩转了所有这些选项之后，我个人最喜欢的应该是 ngrok 和 PageKite。

PageKite 似乎是最具多面性的解决方案，具有巨大的潜力。多年来，它已经被开发和扩展以适应各种用途，使它成为一个真正令人印象深刻的应用程序！

ngrok 的简单性和流量检查员也很棒。对于大多数希望从 web 访问本地主机的 Web 开发人员来说，它有足够多的功能。

## 结论

如果你在一台有活跃互联网连接的计算机上工作，你现在可以在几分钟内从任何地方设置对你的本地主机的访问！开始使用非常简单，正如您所看到的，不缺少解决方案！

你使用过这些服务吗？你还有其他喜欢的吗？我很想知道你在 Twitter 上的想法！

## 分享这篇文章