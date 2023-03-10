# GeoIP JSON 示例

> 原文：<https://www.sitepoint.com/geoip-json-example/>

*这个[文章系列](https://www.sitepoint.com/10-example-json-files/)在 2017 年年中用最新的信息和新鲜的例子重写。*

GeoIP 是一种地理定位软件，用于通过 IP 地址推断一个人或一个物体的地理位置。有许多免费和付费的地理定位数据库提供这些 GeoIP 服务。这些数据库的准确性通常从国家、州、城市到邮政编码级别不等。这种技术通常用于地理营销、区域定价、欺诈检测和刑事调查。

在本例中，我们将查看一个在线数据库，**[【IP-API.com】](http://ip-api.com)**，它以 JSON 格式返回 GeoIP 数据。只需打开 http://ip-api.com/json/54.148.84.95[就会返回如下 JSON 结果:](http://ip-api.com/json/54.148.84.95)

```
{
  "as": "AS16509 Amazon.com, Inc.",
  "city": "Boardman",
  "country": "United States",
  "countryCode": "US",
  "isp": "Amazon",
  "lat": 45.8696,
  "lon": -119.688,
  "org": "Amazon",
  "query": "54.148.84.95",
  "region": "OR",
  "regionName": "Oregon",
  "status": "success",
  "timezone": "America\/Los_Angeles",
  "zip": "97818"
} 
```

要以 JSON 格式查看自己的地理位置数据，只需打开[http://ip-api.com/json/](http://ip-api.com/json/)。

构建利用该服务的应用程序非常简单。您可以快速浏览一下[文档](http://ip-api.com/docs/api:json)，看看如何实现回调和其他特性。如果出现错误，将返回以下 JSON 响应:

```
{
  "status": "fail",
  "message": "ERROR MESSAGE",
  "query": "IP ADDRESS USED FOR QUERY"
} 
```

你要注意，这项服务不是绝对免费的。如果你需要每分钟执行超过 150 个请求，你必须注册一个专业帐户。

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章