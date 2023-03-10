# WordPress 在云中使用 Amazon EC2 和微软网络平台文章

> 原文：<https://www.sitepoint.com/wordpress-in-the-cloud-with-ec2-and-wpi/>

我经常从 web 开发人员那里听到一个说法，特别是那些提供网站设计和咨询服务的人，他们需要快速启动运行速度快的网站，并让他们完全控制服务器，以获得最大的灵活性和可伸缩性。这是一个关于如何做到这一点的快速教程，有一个关于如何在亚马逊的 EC2 云和微软网络平台上运行 WordPress 的向导。WordPress 是世界上最受欢迎的博客引擎。可以说它也是有史以来最受欢迎的 PHP 应用程序。有了成千上万的程序和插件，以及世界范围内一个名副其实的专门从事基于它的网站建设的网页设计师和顾问大军，难怪互联网上有数百万个基于 WordPress 的网站。亚马逊的 EC2 已经采取了一种替代的方法来进行网络托管。作为一名 web 开发者，你可能听说过*云计算*，那么你有机会尝试 EC2 吗？这是对虚拟主机的另一种选择，让你控制你安装的服务器，你只需为你使用的东西付费。如果你曾经在亚马逊买过一本书或其他商品，你可以用同一个账户购买一台服务器，并试用几个小时。都是为了几块钱的总价。WordPress 和 EC2 以一种非常巧妙的方式结合在一起，在几分钟内给你一个超快的博客或网站。如果您已经有了 EC2 帐户，您可以跳过下面的第一部分，直接进入[的“启动 EC2 实例”部分](#section_start_instance "Starting an EC2 Instance")。

## 创建 EC2 账户

首先，浏览到[http://aws.amazon.com/](http://aws.amazon.com/)。点击创建 AWS 账户链接，如图[图一，“创建 AWS 账户”](#fig_create_account "Figure 1. Creating an AWS account")。

**图一。创建 AWS 帐户**

![Creating an AWS account](img/e2030ce6fcc7fbb1d8e8a853423bd256.png)

如果你过去曾在亚马逊购物，你可以使用你现有的账户信息，如图[图 2，“使用你现有的亚马逊登录”](#fig_new_account "Figure 2. Using your existing Amazon sign-in")所示。如果没有，选择我是新用户选项，并按照说明进行操作。

**图二。使用您现有的亚马逊登录信息**

![Using your existing Amazon sign-in](img/2209f54d0c292b5e25acb5c1fe73a614.png)

注册过程相当简单；在此之后，只需指定您的联系信息，你就大功告成了！接下来，您需要注册 Amazon EC2。为此，浏览到[http://aws.amazon.com/ec2/](http://aws.amazon.com/ec2/)，点击注册亚马逊 EC2 链接。这将带您进入定价详细信息页面，您可以在页面底部查看定价选项并使用信用卡支付。

**note:** Pricing

在 EC2 上全职运行一个月的服务器大约需要 80 美元或更多，这取决于你使用多少存储、带宽和 CPU 能力。虽然存在更便宜的选择，但你确实可以完全控制服务器，能够轻松地快速创建新的实例，并受益于亚马逊令人难以置信的网络带宽和连接性。

亚马逊现在想要验证你是一个真正的人。他们通过拨打您指定的电话号码，并要求您输入他们通过电话在网站上提供的个人识别码来做到这一点。输入一个电话号码，大约十秒钟后看到你的电话铃响，在友好的亚马逊女士提示你后输入一个四位数的号码，然后看着网页刷新显示你已经通过认证，这是非常巧妙的。干得好，亚马逊，一点都不痛！

**图 3。亚马逊的电话验证**

![Amazon’s telephone verification](img/f446afd481abd497747dd6e0ae0418a0.png)

点击本页底部的继续，然后点击完成注册进入下一页。Amazon 现在将激活您的订阅，确保您的帐户有权创建新实例。几分钟后，您将收到一封电子邮件，告知激活已完成，您可以启动您的第一台云服务器了！

## 启动 EC2 实例

为此，您需要浏览 AWS 管理控制台:[http://console.aws.amazon.com/ec2/](http://console.aws.amazon.com/ec2/)。您可能会被要求再次登录；如果是这样，请提供您的凭证，然后您将到达亚马逊 EC2 控制台仪表板，如图[图 4 所示，“EC2 控制台仪表板”](#fig_dashboard "Figure 4. The EC2 Console Dashboard")。

**图 4。EC2 控制台仪表板**

![The EC2 Console Dashboard](img/fca749ec5103ca10dbd260cb9638ec30.png)

首先，你需要确保所选择的地区(在左边栏的顶部)是美国西部——这是必要的，这样你就可以访问我们将在本教程中使用的 WordPress 图片。然后点击 LaunchInstance 。Amazon 从快速启动选项卡中提供了一个预配置映像列表——orAMIs(Amazon 机器映像)。这些是普通的 Windows 和 Linux 映像，您可以使用它们从头开始启动服务器。为了使设置服务器实例的任务变得更加容易，微软向亚马逊提供了一些预配置的映像，这些映像已经安装了微软 Web 平台安装程序，随时可以使用。点击社区 AMIs 标签，搜索“微软网络平台”，如图[图 5，“微软网络平台 AMIs”](#fig_request_instances "Figure 5. Microsoft Web Platform AMIs")。

**图 5。微软网络平台 AMIs**

![Microsoft Web Platform AMIs](img/f4a7eeb0da8de02d7901f433f5cdd19e.png)

微软提供了四种服务器映像:

The Media Server image

This image has the new IIS Media Services 3.0 platformpreconfigured with some smooth streaming content already uploadedand ready to go. This is the same server that powered the 2010Olympics and NFL Sunday Night Football.

The DotNetNuke Image

The DotNetNuke image has Windows, IIS, SQL, and DotNetNukepreconfigured and ready to run. DotNetNuke is the leading webcontent management application on .NET.

The Full Web Server Development Stack

This image has the entire Microsoft Web Platform installed,including Visual Studio, IIS, ASP.NET, and SQL Express.

The WordPress Image

This has everything preconfigured and ready to kick off aWordPress install. It’s the one we’ll be using for the rest of thistutorial, so choose this if you want to follow along.

现在，您将在一系列步骤(总共五个步骤)的指导下，在开始之前配置您的映像。首先指定要创建的服务器(实例)数量，并选择实例类型。有两种可供选择:“小型”有一个核心和有限的内存，而“高 CPU 中型”有更多的核心和更大的内存，但价格更高。

**图 6。实例详细信息**

![Instance details](img/9512abdb3460588a2433d423dcc5da3f.png)

下一步为您的实例提供了更广泛的配置选项，但是我发现默认设置非常好。现在，系统会提示您创建一个新的密钥对(或者，如果您已经创建了一个，也可以选择一个现有的密钥对)。只需输入一个名字，然后点击创建&下载您的密钥对，如图[图 7，“创建新的密钥对”](#fig_keypair "Figure 7. Creating a new Key Pair")所示。密钥对是一个包含 Amazon 用来验证您的信息的文件。你应该把它保存在你电脑上的一个安全的地方。

**图 7。创建新的密钥对**

![Creating a new Key Pair](img/86386108b13b09fc47071da9db6c512a.png)

接下来我们需要指定防火墙信息。这一点很重要——如果没有这一步，任何人都无法访问您的网站，您也无法通过远程桌面连接到您的计算机！点击创建一个新的安全组，然后打开所需的端口，如图[图 8，“为我们的新实例配置防火墙”](#fig_firewall "Figure 8. Configuring the firewall for our new instance")所示。您将需要 HTTP 和 HTTPS 作为您的 web 服务器，并且您可能希望打开 MySQL 以便您可以远程管理您的 MySQL 数据库(如果您喜欢在本地管理它，您可以跳过这一步)。我们还将添加 RDP，这样我们就可以远程访问服务器的桌面。

**图 8。为我们的新实例配置防火墙**

![Configuring the firewall for our new instance](img/2c1aeacb042a8177a4dca026d028aaa9.png)

最后一个屏幕，如图 9[所示，“查看选择的配置”](#fig_review "Figure 9. Reviewing the chosen configuration")，总结了您的所有配置信息。如果你对设置满意，只需点击屏幕底部的启动。

**图 9。查看选择的配置**

![Reviewing the chosen configuration](img/f8f6d1f6007ce9d9281ef04cb42b787c.png)

现在我们要做饭了！单击实例页面上的查看您的实例链接。在这里，您将看到您创建的实例列表。启动这台新机器需要几分钟时间，所以现在是吃点心的好时机。

## 分享这篇文章