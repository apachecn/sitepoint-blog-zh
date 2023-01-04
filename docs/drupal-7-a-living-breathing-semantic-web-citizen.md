# Drupal 7——活生生的语义网公民

> 原文：<https://www.sitepoint.com/drupal-7-a-living-breathing-semantic-web-citizen/>

我最近才开始研究并实际使用 Drupal。我在 Linux 服务器上安装了版本 5，它非常简单。嗯，安装很简单。我仍然没有完全想好如何驾驶这辆车——尽管从目前的发展来看，我肯定做出了正确的选择…

Drupal 6 上个月刚刚发布，它有很多变化:

> …更多功能、更易于管理、更易于扩展、更易于主题化、更易于为更多人开发(开发/贡献)… [来源](https://www.lullabot.com/blog/drupalcon-state-dries-buytaert)

这些变化闻起来像是正在酝酿的大事，鉴于首席开发人员 [Dries Buytaert](https://buytaert.net/) 最近的言论，这似乎更加明显。昨天在[Drupal con 2008–Boston](https://boston2008.drupalcon.org/)上，Buytaert 发表了他的 Drupal 状态主题演讲:

来自网络世界:

> 他讨论的事情之一是试图定义 Drupal 和 Web 的下一步。他说，如果 Web 2.0 是关于用户管理和关系的，那么 Web 3.0 将是 Web 2.0 加上“无限的互操作性”。
> 
> 他说，对于 Drupal 来说，这意味着成为一个 RDF 仓库、导出者和导入者，这样数据就变得分散，人们和计算机可以根据要求以任何格式访问数据，无论是 XHTML、XML 还是 JSON。
> 
> “我们希望允许其他人重复使用我们的数据。…用大量数据做一些很酷的事情。在我看来，这很有趣。”

从[开始进入](https://www.lullabot.com/blog/drupalcon-state-dries-buytaert):

> …
> 对功能的关注将会减少——对数据的关注将会增加
> *整合来自不同来源的数据
> *允许其他人重复使用你的数据
> *分散数据，这样任何一方都不会拥有所有数据
> *你可以用大量数据做很酷的事情
> …
> 
> “未来就在这里。只是分布还不均匀。”
> 
>  [![DBPedia, US Census data, RDF Book Mashup, W3C WordNet, CIA factbook, FOAF, Musicbrainz.](img/28512aa3d914130b6ff10d8cb5bda5b5.png)
> ](https://esw.w3.org/topic/SweoIG/TaskForces/CommunityProjects/LinkingOpenData)
> 
> 机会在于，每一个 Drupal 站点都可以成为 RDF 库，我们可以开始将它融合到语义网中(在这些网站上覆盖 Drupal)
> 
> 社交图只是连接人们——我们有机会制作一个连接一切的图。
> 
> 我们正从万维网(WWW)转向[巨大的全球图表](https://dig.csail.mit.edu/breadcrumbs/node/215)。*蒂姆·伯纳斯·李*

这是非常令人兴奋的。一个强大且广泛使用的开源 CMS 采用了这样一种前瞻性的观点。我将密切关注并期待参与版本 7 的开发。未来的网站将成为内容分发者，超越简单的 RSS/ATOM 提要。

Drupal 7 看起来完全接受了这种链接数据网的想法，其中每个站点都成为自己的数据服务器——能够与其他站点和其他数据源进行交互和融合。数据库本身将向 Web 公开(根据您的需要使用或多或少的安全性),其中各个字段标记有语义含义；想 [hCard](https://microformats.org/wiki/hcard) 、 [hReview](https://microformats.org/wiki/hreview) 、 [hCalendar](https://microformats.org/wiki/hcalendar) 、 [FOAF](https://www.foaf-project.org/) 等。

 [![Linking Open Data logo](img/c8114219105826484021b8959090fc9b.png)](https://esw.w3.org/topic/SweoIG/TaskForces/CommunityProjects/LinkingOpenData)

如果你想了解更多关于 Drupal 7 的发展方向，看看[链接开放数据](https://esw.w3.org/topic/SweoIG/TaskForces/CommunityProjects/LinkingOpenData)项目(上图来自他们的网站)。这是一年多前由一个致力于快速发展的团体在 T2 发起的倡议。无论是人数还是实际关联数据。

我很想知道是否有人知道其他主要的 CMS 平台也在朝着类似的方向发展: [Joomla](https://joomla.org/) ， [Mambo](https://www.mamboserver.com/) 等等？随着数据可移植性和关联数据等问题变得越来越突出，看看其他人是否会效仿将是一件有趣的事情。

## 分享这篇文章