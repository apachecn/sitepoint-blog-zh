# 调整 WordPress 来处理高流量

> 原文：<https://www.sitepoint.com/tuning-wordpress-to-handle-higher-amounts-of-traffic/>

性能调整过去只保留给高端网站，但是在这个网站是客户对公司的第一印象的世界里，确保您和您的客户拥有一个高效的网站是至关重要的。

在 SitePoint，我们定期报道网络性能，包括诸如[如何加速你的 WordPress 站点](https://www.sitepoint.com/speed-wordpress/)和[对性能的感知](https://www.sitepoint.com/the-perception-of-performance/)等话题。但是，如果你仍然认为速度不重要，[kiss metrics](http://blog.kissmetrics.com/loading-time/)的一篇文章提供了关于网站延迟如何导致转化率损失的多项数据。

## 什么会影响性能

在探讨提高 WordPress 网站性能的方法之前，了解影响典型 WordPress 网站性能的因素是很重要的。根据官方 [WordPress Codex](http://codex.wordpress.org/WordPress_Optimization) 的一些最大的领域包括:

*   托管:这是一个可以跨越页面的话题，但是简单地说，对于任何关键任务，你至少应该使用虚拟专用服务器(VPS)或专用服务器。虽然共享主机非常便宜，但性能往往很差，因为你要与数百(如果不是数千)用户共享资源。在 VPS 和专用机器上，资源是专用的，这意味着你可以得到主机所承诺的保证。
*   服务器数量:对于高流量的网站，你需要多台服务器来分担负载，而不是让一台服务器来承担访问者的压力。负载平衡可能是一个复杂的话题，但简单地说，您需要记住，对于高流量设置，多个低端机器比单个高端机器更好。
*   硬件性能:使用固态硬盘(SSD)和高性能服务器可以大大提高您的网站的性能。如果您不想购买固态硬盘，为您的服务器购买额外的 RAM 是提高性能的更实惠的选择。

## 检查你的网站是否有瓶颈

在调整你的 WordPress 安装之前，你需要确保你知道你的站点需要改进的地方。通过使用像 YSlow 这样的工具，你可以确定你需要采取的精确步骤来提高你的站点性能。YSlow 是一笔巨大的财富，因为它提供了可操作的步骤，你可以遵循这些步骤来提高你的绩效。

![YSlow](img/4d57a2e45bda5d10da10fdcdf073bcd6.png)

YSlow 浏览器扩展

另一个可以用于性能测试的工具是 [Pingdom](http://tools.pingdom.com/fpt/) 。虽然它没有为您提供与 YSlow 相同的指导，但 Pingdom 有助于确定哪些脚本、页面元素或代码段的加载时间最长。从那里，你可以优化特定的元素来提高你的速度。

## 提高性能的简单方法

虽然性能调优是一个庞大的主题，但您可以通过遵循一些简单的步骤来改进您的网站。

### 在网站上启用缓存

缓存是提高站点性能的最简单的方法之一，因为只需轻轻一按开关，改变一些设置就可以实现。我们已经在一篇关于[托管 WordPress 主机](https://www.sitepoint.com/managed-wordpress-hosting-pros-and-cons/)的文章中讨论了缓存，所以我们在这里不再深入讨论。简而言之，如果你想在你的网站上启用缓存功能[，W3 Total Cache](https://wordpress.org/plugins/w3-total-cache/) 就是你的工具。

![W3 Total Cache](img/b76c95f78b311af681b564ff983e5579.png)

W3 总缓存 WordPress 插件

如果你真的想让你的缓存更上一层楼，你应该研究一下反向代理，比如 [Varnish Cache](https://www.varnish-cache.org/) 。虽然这不是最容易安装在你的网站上的系统，但如果你在运营一个高流量的网站，这是一项你应该研究的技术。

### 集成 CDN

通过使用之前提到的 W3 Total Cache 或流行服务 [Cloudflare](https://www.cloudflare.com/) ，您可以确保通过从离您的访问者最近的服务器部署内容来减少您的网站加载时间。因为这已经在之前提到的关于 WordPress 主机的文章中提到过，我们将继续讨论其他的技巧来帮助你的网站性能。

### 少用插件

WordPress 最大的优点之一是它的可扩展性，然而就像任何电脑一样，如果你不加选择地添加软件，性能会显著下降。虽然使用插件来修改 WordPress 很容易，但是作为一个经验法则，你应该只安装一个插件，如果它增加了重要的价值，而这是通过一些简单的代码调整无法做到的。如果你现在没有使用一个插件，但是你认为你将来会需要它，那么禁用它。

## 主题优化

虽然主题优化可能是一个复杂的话题，但下面是从关于这个话题的 [WordPress Codex](http://codex.wordpress.org/WordPress_Optimization/WordPress_Performance) 中摘录的一些要点。

*   图像文件
    *   避免不必要的图像，如使用图片，而不是使用标准类型。
    *   确保所有图像都针对 web 进行了优化(Photoshop 为此提供了特定的导出选项)，并确保使用 JPEG/GIF/PNG 作为文件格式。
    *   使用插件，如 [WP Smush.it](http://wordpress.org/plugins/wp-smushit/) 来压缩添加到媒体库的图像。
*   文件数量和大小
    *   如果可能的话，尽量减少显示网站所需的文件数量。
    *   在适用的情况下，将 CSS 和 JavaScript 文件合并成一个文件。
    *   尽可能缩小脚本文件。前面提到的 W3 Total Cache 插件内置了这个功能。
*   查询减少/优化
    *   如果可能的话，试着在你网站的静态区域，比如你的字符集和网站标题中硬编码。
    *   如果你的菜单很少改变，尽可能的硬编码。

## 更进一步

虽然本指南已经为你提供了足够的技巧来帮助你开始调整 WordPress 以获得最佳性能，但是它还远远不够全面。如果你计划运行一个有大量持续流量的网站，你会想咨询一个有经验的服务器管理员来帮助确保你尽你所能从你的网站中榨取每一点性能。你可能还想考虑托管 WordPress 主机的利弊，因为托管 WordPress 主机通常能够回答其他主机不会触及的 WordPress 特定问题。如前所述，我们已经在 SitePoint 上写了大量关于 web 性能的文章，包括[一篇关于 WordPress 性能优化的详细文章](https://www.sitepoint.com/speed-wordpress/)。

对于一般的 WordPress 网站来说，遵循前面提到的提示对于你的日常操作应该是足够的。

## 分享这篇文章