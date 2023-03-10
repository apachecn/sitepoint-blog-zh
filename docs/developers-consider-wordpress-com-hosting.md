# 为什么开发商应该考虑 WordPress.com 托管

> 原文：<https://www.sitepoint.com/developers-consider-wordpress-com-hosting/>

如果您可以忘记更新、安全补丁、延迟故障和带宽/磁盘空间问题，会怎么样？WordPress 背后的 Automattic 公司提供了如此全面的托管服务，甚至允许安装第三方插件和模板。

![A WordPress site on a laptop screen](img/55bea2f53aa97ca1b5e358c98cdfbf7b.png)

WordPress (WP)不仅是最受欢迎的内容管理系统，而且截至 2017 年 8 月，它为 28.5%的*所有网站*提供支持(参见“[网站内容管理系统的使用统计和市场份额](https://w3techs.com/technologies/overview/content_management/all/)”)。这很大程度上是因为网站所有者在他们自己的服务器上运行 WordPress.org[提供的免费软件。](https://wordpress.org/)

托管自己的 WP 副本让你可以自由添加插件、调整模板和定制软件的行为，但它也有不利的一面，比如必须处理大量的更新、安全问题以及我们稍后将讨论的其他问题。

WP 还在 WordPress.com 提供其软件的托管版本。开发人员倾向于避免这个选项，主要是因为它对定制的控制少得多，没有定制模板和插件。但同时也有优势，如自动更新，更严格的安全性和全球分布。最近，WP.com 已经解决了它的一些限制，并开始提供更多的控制方式来定制软件——甚至允许你安装插件和定制主题！

在这篇文章中，我们将看看你能做什么和不能做什么，我将提供你为什么会重新考虑 WP.com 的自托管选项的理由。

## SaaS！

SaaS 代表软件即服务。本质上，软件本身——在这里是 WordPress——就是服务。该软件还提供了运行它所需的所有基础设施，您不必担心服务器资源以及运行和管理 WP 安装所需的一切。

WP.com 是 Automattic 支持的官方主机，该公司于 2010 年将 WordPress 商标交给了 WordPress 基金会。WP 的创始人马特·莫楞威格是 Automattic 的首席执行官和创始人，该公司提供 WordPress 的所有内容。这是 *WP 在云端*。

使用像 WP.com 这样的服务，你得到的是内心的平静，即使你完全不关心，东西也不会坏。但是，让我们详细看看如何从共享主机计划切换到 WP.com 可以简化事情。

### 更新和升级

当运行你自己的，自我托管的 WP 时，你需要定期更新以保证你的安装安全。WP 的工程团队让这个过程变得非常顺利，所以如果你已经正确设置了某些写权限，这应该不会花你超过一两分钟的时间。尽管如此，你还是需要定期这样做。许多网站很快就过时了，这给黑客提供了很大的活动空间。

然后你有升级。考虑这些场景:

*   比如说，从版本 4.8 到 4.8.1 的*更新*
*   从 4.7 到 4.8 的升级
*   从 3.9 版本到 4.0 版本的重大升级。

第一种，“更新”类型通常是平稳的，只涉及安全性和性能问题。

由于引入了新功能，升级有时会有问题。

但是主要的升级几乎肯定会带来痛苦，模板和插件需要重写，以便与升级后的 API 一起正常工作。

由于 WP.com 是由维护 WP.org 代码库的同一批人运行的，所有的更新和升级都是平稳地同步进行的。

### 负载平衡和全球分布

今天，性能比以往任何时候都重要。由于开发人员面临着快速交付网站的压力，将网站放在共享主机上并就此罢休是不够的。在建立网站时，像负载平衡和 cdn 这样的问题已经成为一个重要的考虑因素。

如果您的网站拥有国际受众，您总是可以让数据中心附近的访问者非常快速地访问您的网站，但是您越是远离数据中心，您的网站的响应速度就会越慢(例如，您的网站托管在美国西海岸，并从欧洲访问它。)

**WP.com 提供开箱即用的负载平衡**。不管你的网站现在有多大，不管它有多大的流量，或者你的受众有多分散，WP.com 都能处理好，甚至更多。它还带来了云托管服务的好处(参见“[共享和云托管的比较，以及如何选择](https://www.sitepoint.com/shared-vs-cloud-hosting-how-to-choose/)”)，包括成为分布在全球的网络的一部分，这使得您的网站几乎可以立即访问，无论您是从上海、罗马、约翰内斯堡、纽约市还是里约热内卢访问。

### 服务器升级和维护

WP 赖以运行的软件——PHP 和 MySQL 也是如此。每隔一段时间，你的托管公司就会经历一次重大升级，有大量的通知邮件、文档和关于如何过渡的教程，让公司的论坛火起来。同样，服务器升级并不经常发生，但当它们发生时，这是一个耗时的过程，涉及到重新设置、测试，以及如果您未能正确完成过渡，您的站点会有崩溃的风险。

如果你安装了几个 WP，维护时间会增加，你需要有条不紊地避免中长期的问题。您的客户越多样化，您需要关注的服务器就越多，您能够简化的更新就越少。

WP.com 的工程师为您维护所有的基础设施，甚至重大升级(软件和硬件)也能顺利进行。这就是 SaaS 的魅力，软件本身就是服务，除了使用它，你不需要担心其他任何事情。

因为最好的 [DevOps 实践](https://www.sitepoint.com/devops-by-example-tools-pros-and-cons-of-a-devops-culture/)，你永远不会发现基础设施发生了如此重大的变化。这不会影响你网站的[正常运行时间](https://en.wikipedia.org/wiki/Uptime)，即使在主要的软件和硬件升级期间，正常运行时间也是 100%。

### 安全性

不及时更新的一个问题是，你最终会安装一个不安全的 WP，任何脚本小子都可以通过扫描网络上易受攻击的 WP 版本来控制你的网站。

此外，即使您的自托管 WP 实例是最新的，它也只需要一个更熟练的黑客接管服务器上无人值守的漏洞。虽然这并不总是发生，但这是我们许多人都痛苦经历过的事情。

每个补丁一旦在 WP.org 可用，就会被应用到 WP.com。此外，由于云的全球分布式特性， [DDoS 攻击](https://en.wikipedia.org/wiki/Denial-of-service_attack#Distributed_DoS)的影响被大大降低。即使 WP.com 的[零日漏洞](https://en.wikipedia.org/wiki/Zero-day_(computing))被利用来危害其网站，一旦检测到攻击，工程师们将立即撤销伪造的更改并修补安全漏洞。

## 已经在专用服务器或托管计划上？再想想！

专用服务器是共享主机计划的加强版，或者我应该说，它是一个没有“共享”部分的“共享主机计划”。

实际上，您将拥有更多“专用”资源，如带宽、CPU、RAM 内存、磁盘空间。但本质上，你仍然没有解决上述问题中的任何一个问题，所以从这个意义上来说，专用服务器给 T2 带来的附加值很少。

而且你会为此付出很大代价！拥有专用服务器的**长期成本**可能在每年 1000 美元左右。只需三台专用服务器运行几个 WP 网站，你就可以轻松达到 500 美元/月的大关。

结果是**一个更具成本效益的解决方案是可用的**，它不仅会为您分配尽可能多的带宽、CPU、RAM 和您可能需要的空间，而且还会同时解决上述所有问题。

## WordPress.com 计划

截至 2017 年 8 月，WP.com 提供了 4 层计划结构。你可以在[https://wordpress.com/pricing/](https://wordpress.com/pricing/)看到全部细节，但这里是大图:

| 自由的 | 个人的 | 溢价 | 商业 |
| --- | --- | --- | --- |
| $0 | 每个月 4 美元 | 每月 8.25 美元 | 每月 24.92 美元 |
| 3 GB | 6 GB | 13 GB | 无限存储。 |
| WordPress.com 子域 | 自定义域 | 自定义域 | 自定义域 |
| 通过论坛提供支持 | 电子邮件和实时聊天支持 | 电子邮件和实时聊天支持 | 电子邮件和实时聊天支持 |
| 广告(ad 的复数) | 没有广告 | 没有广告 | 没有广告 |
|   |   | 无限制的高级主题 | 无限制的高级主题 |
|   |   | CSS 定制 | CSS 定制 |
|   |   | 通过 [WordAds](https://wordads.co/) 投放广告 | 通过 [WordAds](https://wordads.co/) 投放广告 |
|   |   |   | 安装插件 |
|   |   |   | 上传主题 |
|   |   |   | 谷歌分析 |
|   |   |   | 删除 WordPress.com 品牌 |

请注意，价格按月计算，但**按年计费**。

### 第三方插件和自定义主题

是的，我们正要说到这个！你一直在说“看，这一切都很好，这是一个很好的服务，但我需要插件和定制模板，WP.com 就是不满足。”好吧，他们已经听说了你，从 2017 年 8 月开始[，这是商业计划的一个特点:](https://en.blog.wordpress.com/2017/08/07/wordpress-com-business-now-supports-plugins-and-third-party-themes/)

[https://www.youtube.com/embed/vufNLGmUHzo?rel=0](https://www.youtube.com/embed/vufNLGmUHzo?rel=0)

<br>

这是对阻碍许多开发者和设计者使用 WP.com 的一个重大改进。

然而，也有一些限制。你仍然不能改变 WP 核心，这项服务是针对 WP.org 官方支持的插件的。但是这种变化为广泛的需求带来了很大的灵活性。让我们面对它，如果作为一个开发者你需要改变 WP 核心，你对[WordPress API](https://codex.wordpress.org/WordPress_APIs)不够熟悉，不能正确地编写插件。

## 何时坚持定期托管计划

如果你有一个修改过的 WP 安装(我已经评论过这通常不是一件好事)，你需要一个运行它的地方，所以坚持一个常规的托管计划是必要的。

另一个你需要一个常规托管计划的情况是**如果你有定制的或者修改过的插件**不在 WP.org。

最后，如果你**评估了成本和功能**之后，仍然不想为使用插件或上传自定义模板支付 25 美元，那没问题，这由你决定。毕竟，仅仅四个具有这些功能的站点每月就要花费大约 100 美元。但是，考虑一下，你可以把这个成本转移给你的客户。不要说“主机是 5 美元/月”，你应该说“主机是 25 美元/月，它包括自定义支持，负载平衡，不断更新，全球分发”…等等。你明白了。

### 终极选择:管理下的 WordPress.com

WP 运行着近三分之一的互联网，一个完整的商业生态系统围绕它蓬勃发展也就不足为奇了。这包括“托管”解决方案:也就是说，你付钱给一家公司为你运行软件。

虽然这些公司中的许多公司将会看到他们的商业模式因 WP.com 最近增加的插件和主题支持而受到严重损害，但有些公司仍然提供有竞争力的价格，允许你在一台服务器上运行几个 WP 网站，并允许你修改整个 WP 安装，同时为你处理其他一切。

其中包括:[梦幻主机](https://www.dreamhost.com/promo/wpbeginner/)、[飞轮](https://getflywheel.com/)、 [GoDaddy](https://www.godaddy.com/hosting/wordpress-hosting) 、[媒体模板](https://mediatemple.net/wordpress-cloud-hosting/)、[页面模板](https://pagely.com/)、[可压](https://pressable.com/)、[压力模板](https://pressidium.com/)、[场地](https://www.siteground.com/wpbeginner-managed)和 [WPEngine](https://wpengine.com/) 。

更多信息请参见“[托管 WordPress 主机:利弊](https://www.sitepoint.com/managed-wordpress-hosting-pros-and-cons/)”。

## 包裹

更新、升级、安全性、正常运行时间、负载平衡、分发……WP.com 解决了许多你可能很难处理的问题，即使是支付 150 美元/月的专用服务器。是的，它有一些 SaaS 交付模型固有的限制，比如不能修改软件的某些方面，尤其是代码。但是，它的灵活性涵盖了广泛的用途。

这是对额外几美元的心安理得，有时你可能能够转移到你的客户，有时不能。但在很多情况下，这也是完全忘记维护网站的合理代价。

## 分享这篇文章