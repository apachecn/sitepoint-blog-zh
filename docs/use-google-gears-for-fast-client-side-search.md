# 使用 Google Gears 进行快速客户端搜索

> 原文：<https://www.sitepoint.com/use-google-gears-for-fast-client-side-search/>

六周前，当谷歌的 [Gears](https://gears.google.com/) 离线网络应用程序 API 满一岁时，谷歌宣布，它已经与 Gears 潮流的最大外部用户 MySpace 签约。这家社交网络没有使用 Gears 来创建该网站的完全离线版本，但它确实使用了谷歌 API 来添加其消息应用程序搜索这一长期以来一直缺失的功能。

MySpace 使用 Gears 允许用户离线存储他们的信息档案，然后实时搜索。数据可以通过 Gears 在后台同步，即使搜索是在本地进行的，搜索仍然可以感觉像是在网上进行的。因为一切都发生在客户端，所以你可以在用户输入时实时返回搜索结果——当请求需要通过网络发送时，这是很难做到的，尤其是在大规模的情况下。

Gears 能够做到这一点是因为全文搜索功能被捆绑到了 API 附带的 SQLite 数据库中。谷歌工程师布拉德·纽伯格本周发布了 [PubTools](https://code.google.com/p/gears-pubtools/) ，这是一个 JavaScript 文件的开源集合，它使用 Gears 和 Dojo Toolkit 来完成 MySpace 所做的客户端快速搜索。他还发表了一篇[长文](https://gears-pubtools.googlecode.com/svn/trunk/docs/fast_search_article.html)，详细介绍了如何使用 Gears 创建一个快速的客户端搜索引擎，并将其嵌入到网站或 web 应用程序中。

Ajaxian 的 Dion Almaer 就这个项目采访了 Neuberg。视频在下面。

<object width="425" height="344"><param name="movie" value="https://www.youtube.com/v/VwEkp7TKHUk"><param name="wmode" value="transparent"><embed src="https://www.youtube.com/v/VwEkp7TKHUk" type="application/x-shockwave-flash" wmode="transparent" width="425" height="344"></object>

请让我们知道您是否在您的任何项目中使用过 Gears，如果是，是如何使用的。

## 分享这篇文章