# WordPress JSON 示例

> 原文：<https://www.sitepoint.com/wordpress-json-example/>

*这个[文章系列](https://www.sitepoint.com/10-example-json-files/)在 2017 年年中用最新的信息和新鲜的例子重写。*

在这个 WordPress JSON 例子中，我将展示如何从一个现代的 WordPress 安装中获得一个帖子列表。为了更好的用户体验，使用 [Postman](https://www.getpostman.com/) 与 WordPress 的 REST API 交互。

WordPress 是 2003 年推出的开源内容管理系统(CMS)。它实际上是另一个项目 b2/cafelog 的分支，这个项目可以追溯到 2001 年。今天，WordPress 被认为是最受欢迎的 CMS 平台，根据 WPManage 的数据，它目前占据了全球网络的 26%。它还拥有大约 **60%** 的市场份额，是使用最多的 CMS。

在最近的 WordPress 版本中，REST API 作为一个内置特性被引入，为无穷无尽的新可能性打开了大门。开发人员现在可以编写新的应用程序，通过发送和接收 JSON 数据来与站点进行远程交互。您不再局限于 PHP——您可以自由使用您最喜欢的语言，只要它能与 JSON 交互。

你可以从这个链接启动邮差:
[![Run in Postman](img/46540439e43e214e885022556a69a94a.png)](https://www.getpostman.com/run-collection/39470d10b78a47070620)

启动后，将此链接粘贴到 URL 字段:

```
https://www.sitepoint.com/wp-json/wp/v2/posts?per_page=3 
```

确保选择了**获取**命令。点击发送按钮，几秒钟后，您将收到一个 JSON 响应。下面是它看起来的部分结果:

```
[
  {
      "id": 157538,
      "date": "2017-07-21T10:30:34",
      "date_gmt": "2017-07-21T17:30:34",
      "guid": {
          "rendered": "https://www.sitepoint.com/?p=157538"
      },
      "modified": "2017-07-23T21:56:35",
      "modified_gmt": "2017-07-24T04:56:35",
      "slug": "why-the-iot-threatens-your-wordpress-site-and-how-to-fix-it",
      "status": "publish",
      "type": "post",
      "link": "https://www.sitepoint.com/why-the-iot-threatens-your-wordpress-site-and-how-to-fix-it/",
      "title": {
          "rendered": "Why the IoT Threatens Your WordPress Site (and How to Fix It)"
      },
      "content": {
         ...
      },
      "excerpt": {
          ...
      },
      "author": 72546,
      "featured_media": 157542,
      "comment_status": "open",
      "ping_status": "closed",
      "sticky": false,
      "template": "",
      "format": "standard",
      "meta": [],
      "categories": [
          6132
      ],
      "tags": [
          1798,
          6298
      ],

      }
  ] 
```

我已经截断了内容和摘录，以向您展示一篇 WordPress 帖子的 JSON 响应结构的清晰概述。要了解更多关于 WordPress REST API 的知识，你应该看看这个教程和[这个教程](https://www.sitepoint.com/wp-api/)。

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章