# CDNs 之战:比较 CloudFlare 和 Incapsula

> 原文：<https://www.sitepoint.com/battle-of-cdn-comparing-cloudflare-incapsula/>

更快的内容交付和安全性是任何网站的两个关键方面。两者兼得是锦上添花。 [CloudFlare](https://www.cloudflare.com/your-websites-initial) 和 [Incapsula](http://www.incapsula.com/) 以免费增值模式提供 CDN 和安全即服务。我们将根据两个提供商的产品和基本测试来比较他们的服务。如果您必须在两者之间做出选择，这种比较应该为您提供一个基本的工具集来评估您的需求！我们将评估免费计划中可用的服务，但这对大多数站长来说应该足够了。

## 位置

CloudFlare 有 18 台边缘服务器，其中 10 台位于美国，4 台位于欧洲，香港、悉尼、新加坡和东京各有一台。Incapsula 在美国和欧洲有 9 个边缘服务器，分别位于新加坡和东京。您的受众分布应决定选择，因为印加普拉在亚洲的影响力有限。

## **缓存**

CDN 极大地提高了网站的性能，但是这些提供商也提供脚本的缩小和图像的压缩。CloudFlare 具有 Rocket Loader 功能，可以缓存来自不同站点的静态和动态内容和资源，并异步加载它们。Incapsula 也提供了类似的功能。

## **带宽限制**

Incapsula 的带宽上限为 50GB/月，而 CloudFlare 对带宽没有任何限制。CloudFlare 的一个好处是，您可以用同一个帐户拥有一个免费计划网站和另一个付费计划网站。

## **分析**

CloudFlare 详细记录访问者、爬虫、机器人的访问，并识别任何可能的威胁。它还提供有关节省的带宽和其他操作指标的数据。与谷歌分析报告相比，访问者访问与爬虫和机器人访问的比较是一个重要的优势。统计数据基本一致，只有微小的变化。

![](img/d270abd6b8ede8efa59d9dd95190b878.png)

```
Fig. 1: Analytics with CloudFlare
```

![](img/37dbf0aa8c55742126f5aacdcf8a621a.png)

```
Fig. 2: Analytics with Incapsula
```

# **设置**

为你的网站配置这些提供商是相当容易的。配置包括提供您的网站名称，并对 DNS 设置进行小的更改。不需要安装或上传网站。您必须使用自己的或托管的 DNS，而 CloudFlare 提供免费的负载平衡 DNS 服务。

## 最佳配置

CloudFlare 有大量选项可供配置，我们将了解一些关键点。

![](img/5e0c9a24c9692bd7f431b224bc29ae87.png)

```
Fig. 3 : Performance settings in CloudFlare
```

配置性能设置时，您可以使用预设或定制配置文件，如图 3 所示。我使用 CDN +完全优化，但 TTL 为 4 小时。

![](img/3649b64a88dd062a73180eb41e6f6d39.png)

图 4:封装内的性能设置

Incapsula 的性能配置相当简单，如图 4 所示。您可以将加速模式更改为“关闭”或“标准”(基本缓存)，您可以设置 www。版本作为标准，并显示一个印章，让您的访客知道，在 capsula 是保护它。 

# 试验结果

现在让我们对它们进行测试，并比较结果。我做了速度测试(Webpagetest.org)、负载测试(LoadImpact.com)和 YSlow / Google 页面速度测试(GTMetrix.com)。这些测试是在网站加入 CDN 几天后进行的，以获得准确的结果。测试站点位于荷兰的 VPS 上，服务于英国市场。这是一个相当优化的网站，启用了 HTML 缓存。让我们看看结果:

## 速度测试(Webpagetest.org)

Webpagetest.org 使用“第一次浏览”和“重复浏览”，来复制访问者第一次访问你的网站时发生的事情，以及以后的访问。我使用这项服务测量了 4 个不同地点的加载时间:美国迈阿密、惠灵顿(在新西兰，距离最远的地点之一)、伦敦(在英国，我的目标市场)和阿姆斯特丹(在荷兰，我的网站所在的地方)。

<colgroup><col style="width: 210px"> <col style="width: 118px"> <col style="width: 130px"> <col style="width: 165px"> <col style="width: 144px"></colgroup>
|  | 美国迈阿密 | 新西兰惠灵顿 | 英国伦敦 | 荷兰阿姆斯特丹 |
| 第一次看到云闪 | 2，1s | 778 秒 | 197 秒 | 172 秒 |
| 胶囊中的第一个视图 | 5.6 秒 | 9.89 | 5.01 秒 | 3.81 秒 |
| 重复查看云耀斑 | 0.98 秒 | 4.21 秒 | 0.91 秒 | 0.97 秒 |
| 封装中的重复视图 | 2.93 秒 | 293 秒 | 3.8 | 1.83 米 |

CloudFlare 为新老客户提供了一致、快速的全球体验。Wellington 在这两家服务提供商方面表现不佳。

## 负载测试(LoadImpact.com)

LoadImpact.com 让你有机会看到你的网站是如何同时支持多个访问者的。我们测试了 50 个并发用户。这两项测试都没有出现在测试服务器的负载统计中，这证实了 CDN 正在交付内容，而没有加载网站托管的服务器。

正如您在下面的图表中看到的，CloudFlare 开始时加载时间较长，但在 10 个并发用户时下降，并在多达 50 个用户时保持一致。

![](img/5ebf83b927d5576d2bd5e309ab46f136.png)

Incapsula 显示了一个稳定的图表，但是加载时间更长，如下图所示。Incapsula 的糟糕结果可能是由于 LoadImpact 从以色列服务器而不是东京服务器运行测试造成的。

![](img/f0db2ef53ebbb881b7a9bfde8fbbcf7c.png)******

 *## YSlow /谷歌页面速度测试(GTMetrix.com)

我总是用 GTMetrix.com 来衡量我的雅虎 YSlow 和谷歌页面速度得分。他们会给你的网站打分，并给你一些提高分数的建议。Incapsula 的 YSlow 级别较低是因为它没有被 GTMetrix.com(cloud flare)识别为 CDN。但这不是《Incapsula》得分较低的唯一原因。CloudFlare 积极采纳谷歌和雅虎的建议，这些建议改善了它们的优化，给了它更高的评级

![](img/eb50f8b5625b00e9a547b7b712f392ab.png)

```
*Test performed when hosted at CloudFlare (GTMetrix.com)* 
```

*![](img/b54402a2342b5c08cb7bad3909647217.png)*

```
*Test performed when hosted at Incapsula (GTMetrix.com)*
```

# **结论**

就边缘位置而言，CloudFlare 具有更好的覆盖范围。在配置方面，它也提供了广泛的选择。另一方面，就配置而言，Incapsula 可能要简单得多。当然，我们已经比较了两家服务提供商提供的免费版本的基本选项。在做出最终决定之前，您一定要考虑高级版和企业版中提供的产品。

更新:我最近看到一篇关于这两个提供商的安全特性的优秀文章。[看这里](http://www.tourney.se/downloads/Full-Review.pdf) (PDF)。** 

## **分享这篇文章***