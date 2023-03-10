# 我们来比较一下:RunCloud vs Forge vs ServerPilot

> 原文：<https://www.sitepoint.com/lets-compare-runcloud-vs-forge-vs-serverpilot/>

当你的网站或网络应用程序已经超出了你的共享主机账户的时候，是时候把它转移到云或者虚拟专用服务器(VPS)了。如今，VPS 提供商在 CPU 和 RAM 资源方面比共享主机提供商提供了更好的价值。然而，我记得几年前当我想把我的网站转移到亚马逊网络服务时，我花了几天时间来部署服务器，并准备好 PHP、MySQL 和 Apache。

幸运的是，这不是今天的情况，因为我们有一些很酷的工具和第三方服务的帮助。

我查看了三个类似的服务——run cloud、ServerPilot 和 Laravel Forge——并对它们进行了比较，看哪一个表现最好。这些 SaaS 工具可以帮助部署、配置、管理和监控任何云托管提供商(如 AWS、Linode、Vultr、Digital Ocean 等)上的 VPS。

## [RunCloud.io](https://runcloud.io)

![RunCloud](img/c91935a065d661d83ad230ab65eed552.png)

注册很容易。你不需要信用卡，甚至可以使用你的脸书、谷歌或 GitHub 账户。如果你不喜欢使用你的社交媒体账户，那就使用你的电子邮件地址。

有了 RunCloud，你需要做的就是在几乎任何 VPS 托管提供商上部署全新的 Ubuntu 16.04 LTS VPS。在整个教程中，我使用了每月 5 美元的 Linode VPS。

当你的服务器准备好了，你回到 RunCloud，输入你的服务器细节，比如 IP 地址，来获得你的连接命令。

![Connecting a Server on RunCloud](img/6e054254382cb65895be286974c8517d.png)

您将得到一个非常长的单行命令，如下所示。

![Command for RunCloud](img/c5e1b0e93d47719b6f60821ba881bd69.png)

您需要做的就是以 root 用户的身份在您的服务器中运行该命令，并让它运行。这个过程可能需要 15 到 20 分钟才能完成，一旦完成，您就可以从 web 面板中开始管理您的服务器。

### 管理您的服务器

接下来，您需要创建一个 Web 应用程序。这就像 web 服务器中的虚拟主机。当您这样做时，您可以选择您的 PHP 版本，并且您可以将您的域名分配给 Web 应用程序。您可以分配多个。

用户界面非常友好和直观，你可以很快很容易地找到你需要的任何东西。我最基本的需求是能够创建虚拟主机(Web 应用程序)、分配域名和创建数据库，并且可能从 GitHub 或任何其他 Git 服务器部署我的应用程序。还有一个脚本安装程序，支持 WordPress、Joomla、Concrete5、PHPMyAdmin 和一些更常见的 PHP 工具。

如果你做 PHP 开发并使用 Git，RunCloud 支持从 GitHub、Bitbucket、Gitlab 以及你自己定制的 Git 服务器安装部署你的脚本。

![A server overview](img/fad288032cfed5d479defa6ec4e00b4a.png)

![Managing a web application](img/1e3eb0aa029050bd355e65829690757e.png)

我们稍后将研究性能。

* * *

## [服务器飞行员](https://serverpilot.io)

![ServerPilot](img/6df0ddc0ad5d42547c5588efff66c10b.png)

ServerPilot.io 可能是这三个网站中最老的，也是营销得最好的。当我询问周围是否有人在使用服务器管理工具时，很可能是 ServerPilot.io。

ServerPilot.io 有一个免费帐户，你可以使用有限的功能，还有蔻驰(10 美元/月)和商业(49 美元/月)计划。请记住，这些价格是针对服务器部署的，而不是针对 ServerPilot 的每个帐户。

蔻驰有更多的功能，如通过 LetsEncrypt 免费 SSL，服务器健康统计和其他。业务包更像是一个迷你的 NewRelic 或者一个应用性能监控服务。您可以看到缓慢的脚本请求、应用程序资源统计等等。

当然，免费套餐已经足够好了，但是与他们的蔻驰和商业选择相比还是有局限性的。

### 连接服务器

这个感觉和 RunCloud 差不多。还需要一个全新的 Ubuntu 16.04 VPS 服务器才能启动。如果您有服务器的 root 密码，就不需要通过 SSH 复制和粘贴安装程序命令。您可以输入您的服务器 IP 地址和 root 密码，ServerPilot 将在几分钟内准备好您的服务器。

但是，如果您不想在 ServerPilot 中输入服务器的 root 密码，那么您可以选择不这样做。您可以获得 install run 命令，并通过 SSH 将其复制并粘贴到您的服务器中。

![Connecting A Server on ServerPilot.io](img/90388072ede4efa17ca318052ad7949f.png)

老实说，服务器部署过程感觉非常顺利和快速。我对此感到惊讶。一旦完成，我马上被重定向到创建一个应用程序。在这里，应用程序就像虚拟主机。一旦完成，就可以选择安装 WordPress。我想我应该试试。如果你愿意，你也可以选择你的 PHP 版本。我选的是 PHP 7.1。

![Creating an app on ServerPilot.io](img/ae7a4e4c88f80d7ac2d13ef8e84b34e0.png)

### 管理您的服务器

从注册到部署服务器，再到启动并运行，这种体验真的很好很快。你马上就能让服务器运行起来。

当 WordPress 是一个安装选项时，我希望看到其他 PHP 工具如 Joomla 和 Concrete5 也可以使用。不幸的是，它们不存在，而且 ServerPilot.io 也没有提供选项来设置 web 根目录应该在除了`/public`以外的地方。

我希望看到的另一件事是从 GitHub 部署我的应用程序的方法。我找不到那个。我从文档中发现，如果我想通过 Git 部署我的脚本，我仍然必须通过 SSH。

调度或 crontab 也是如此。如果您熟悉 CPanel，您会希望将调度内置到 ServerPilot 的 web 面板中。不幸的是，情况并非如此。尽管如此，您仍然可以通过 SSH 管理自己的 crontab。

![Application settings in ServerPilot.io](img/3c377a4dfe42bd6e4cbed5c9e1c133cd.png)

## 拉勒维尔锻造厂

![Laravel Forge](img/d18c7b9753508ac8cc5666e584bf14be.png)

不幸的是，如果你只是想尝试一下，没有免费级别的帐户。成长型客户的起价为每月 15 美元，企业客户的起价为每月 39 美元。好消息是，这适用于无限数量的服务器部署。然而，无论你选择哪个账户，都有 5 天的免费试用期。如果您订阅年度订阅，您可以从增长帐户中获得 40 美元的折扣，从商业帐户中获得 69 美元的折扣。

这两者之间的区别主要在于将团队成员添加到您的帐户中，以帮助您管理服务器。增长账户主要面向个人用户，而企业账户允许你添加团队成员来帮助你。

### 连接服务器

与 ServerPilot 和 RunCloud 不同，Laravel Forge 将通过 API 连接到您的云托管提供商，并代表您在那里部署服务器。有了这个，你就不必先在云托管提供商那里部署你的服务器，然后在 Laravel Forge 中输入细节。拉弗尔锻造厂会为你制造它们。目前，Laravel Forge 支持数字海洋、AWS 和 Linode。

对于目前不支持的任何云托管提供商，您仍然可以在自定义 VPS 选项下将它们添加到 Laravel Forge 中。它的工作方式非常类似于服务器试点和 RunCloud。你需要一个新的 Ubuntu 16.04 64 位 VPS，然后在 Laravel Forge 中启动连接程序。就像 RunCloud 一样，您需要通过 SSH 输入安装脚本命令。

![Connecting a Server on Laravel Forge](img/e5544aab7dc69f754e8a47ee2512fd61.png)

![Connecting a Server on Laravel Forge](img/0e2097028c6078a8625048b18996c3ec.png)

### 管理您的服务器

Laravel Forge 在管理服务器方面有很多内容。当你的服务器准备好了，你创建一个站点(虚拟主机)。因为 Laravel Forge 是由 PHP 开发人员创建的，所以 Git 实际上是部署文件的主要方法。或者，你可以从安装 WordPress 开始。

创建网站时，您可以选择添加域名。您可以设置是否启用通配符子域选项。但是，一个网站不能有多个域名。

还有调度，MySQL 数据库管理等。您还可以将您的服务器与 Blackfire.io 集成，用于应用程序性能监控，与 paper trail(https://papertrailapp.com/)集成，用于日志分析。

但是没有太多关于服务器健康的信息。

总而言之，Laravel Forge 主要用于部署基于 Laravel 的 PHP 应用程序，并支持后来添加的其他 PHP 框架和脚本。

![Connecting a Server on Laravel Forge](img/6c26bc170e6dfb63108712f7fc0e5cdd.png)

![Connecting a Server on Laravel Forge](img/6b4c60b61e421d232acc7b92641a1c8a.png)

## 性能试验

因为我使用这三种工具部署了三台服务器，所以我想我应该对它们进行测试。为了让事情变得有趣，我想我应该添加另一个 LAMP 堆栈，它是我用 Ubuntu 16.04 和`tasksel lamp-server`命令部署的。

我知道我并没有真正将服务器推向极限，但是我认为这是一个很好的指标，可以看出它们彼此之间的性能如何。

为了测试服务器，我安装了带有默认主题和数据的 WordPress。然后，我在一个单独的服务器上安装了围攻，看看服务器可以接受多少请求和点击。我的围攻设置了 25 个并发客户端设置和一(1)分钟的测试时间。

顺便提一下，我部署了四个 Linode 1GB VPSes，每个工具一个。它们都具有相同的规格，并且位于相同的数据中心。围攻在第五台服务器上。

这是测试的结果:

#### 首轮放映

| 结果 | 润云 | 服务器引导 | 拉勒韦尔锻造厂 | 任务灯 |
| --- | --- | --- | --- | --- |
| 交易(点击次数) | Seventeen thousand seven hundred and ten | Twelve thousand three hundred and four | Eighteen thousand two hundred and seventy-eight | Ten thousand seven hundred and three |
| 可用性(%) | One hundred | One hundred | One hundred | One hundred |
| 经过的时间(秒) | Fifty-nine point four five | Fifty-nine point eight five | Fifty-nine point one one | Fifty-nine point three |
| 传输的数据(MB) | Six hundred and thirty-two point six one | Four hundred and thirty-nine point five seven | Six hundred and fifty-two point nine seven | One hundred and ninety-eight point one four |
| 响应时间(秒) | Zero point zero six | Zero point one | Zero point zero six | Zero point one one |
| 交易速率(交易/秒) | Two hundred and ninety-seven point nine | Two hundred and five point five eight | Three hundred and nine point two two | One hundred and eighty point four nine |
| 吞吐量(MB/秒) | Ten point six four | Seven point three four | Eleven point zero five | Three point three four |
| 并发 | Sixteen point nine five | Nineteen point six six | Seventeen point two six | Twenty point four two |
| 成功的交易 | Seventeen thousand seven hundred and ten | Twelve thousand three hundred and four | Eighteen thousand two hundred and seventy-eight | Ten thousand seven hundred and three |
| 失败的交易 | Zero | Zero | Zero | Zero |
| 最长交易 | One point three six | One point eight three | One point five seven | One point six six |
| 最短的交易 | Zero | Zero | Zero | Zero |

#### 二轮放映

| 结果 | 润云 | 服务器引导 | 拉勒韦尔锻造厂 | 任务灯 |
| --- | --- | --- | --- | --- |
| 交易(点击次数) | Seventeen thousand eight hundred and twenty | Thirteen thousand one hundred and ten | Eighteen thousand three hundred and eighty-six | Ten thousand and seventy-seven |
| 可用性(%) | One hundred | One hundred | One hundred | One hundred |
| 经过的时间(秒) | Fifty-nine point five | Fifty-nine point six three | Fifty-nine point five seven | Fifty-nine point five five |
| 传输的数据(MB) | Six hundred and thirty-six point six four | Four hundred and sixty-eight point four one | Six hundred and fifty-six point seven two | One hundred and eighty-six point five seven |
| 响应时间(秒) | Zero point zero six | Zero point zero nine | Zero point zero five | Zero point one two |
| 交易速率(交易/秒) | Two hundred and ninety-nine point five | Two hundred and nineteen point eight six | Three hundred and eight point six five | One hundred and sixty-nine point two two |
| 吞吐量(MB/秒) | Ten point seven | Seven point eight six | Eleven point zero two | Three point one three |
| 并发 | Seventeen point one eight | Nineteen point three one | Sixteen point nine seven | Twenty point five four |
| 成功的交易 | Seventeen thousand eight hundred and twenty-two | Thirteen thousand one hundred and ten | Eighteen thousand three hundred and eighty-six | Ten thousand and seventy-seven |
| 失败的交易 | Zero | Zero | Zero | Zero |
| 最长交易 | One point three three | One point seven seven | Zero point nine five | Two point three eight |
| 最短的交易 | Zero | Zero | Zero | Zero |

Laravel Forge 是最好的，在 25 个客户端并发的情况下，它能够在一分钟内接收超过 18000 次服务器点击。RunCloud 排名第二，服务器点击率约为 17000 次。ServerPilot 排名第三，点击量约为 13000 次。但这仍然比正常的 Tasksel LAMP 服务器安装有所改进，后者能够在 1 分钟内接受大约 10000 次服务器点击。

#### 绩效结果

1.  拉勒韦尔锻造厂
2.  润云
3.  服务器引导
4.  Tasksel 灯-服务器

## 结论

我相信每个人都有自己的优点和缺点。

### 润云

RunCloud 拥有最好的 web 面板，它提供了管理服务器所需的一切。这几乎就像你的共享托管账户的旧 CPanel。有 Git 部署选项、服务器健康信息、每个虚拟主机多个域、SSH 密钥、防火墙选项、数据库管理、文件管理器和编辑器，等等。显然，就功能而言，RunCloud 是最好的。

这也是最便宜的选择，而且物有所值。无限服务器部署每月仅需 10 美元。

缺少的是团队成员选项。此外，说到性能，Laravel Forge 比 RunCloud 好，但有些人可能可以接受这一点，因为差异并没有那么大。

### 服务器引导

我本来希望 ServerPilot 会表现良好，因为他们已经在市场上有一段时间了，但事实证明它可能比 Tasksel 好 25%到 30%，在 RunCloud 之后排名第三。

他们的网络面板界面也有点令人失望。它没有提供许多功能来帮助您管理服务器。

对我来说，没有 Git 部署和调度(Crontab)是服务器管理的两大缺点。

价格方面，在免费级别之后，ServerPilot 的每台服务器起价 10 美元，这是三个选项中最贵的。

我可以称赞 ServerPilot 的一点是服务器部署的速度有多快。

### 拉勒韦尔锻造厂

Laravel Forge 最好的一点就是他们的服务器性能。如果那是你想要的，那么这是你的选择。他们的服务器比其他的都强！

API 集成也有助于部署服务器，而无需做太多工作。一旦你的 Forge 账户通过 API 连接到 AWS、DigitalOcean 或 Linode，你就不必再去你的云托管提供商那里安装并运行服务器了。

然而，web 面板和功能非常基本，但比 ServerPilot 要好。您有 Git 部署和调度，但是没有服务器健康信息。虽然您可以轻松实现通配符子域，但您在 Forge 上的站点并没有为您提供多个域名的选项。

要获得更多功能和更多有用信息，您可以集成第三方选项，如 Blackfire.io 和 Papertrail。然而，那会让你付出额外的代价。

您使用这些提供商中的哪一个？还是根本没有？为什么？

## 分享这篇文章