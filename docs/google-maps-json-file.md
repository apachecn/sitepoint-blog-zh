# Google Maps JSON 文件示例

> 原文：<https://www.sitepoint.com/google-maps-json-file/>

*这个[文章系列](https://www.sitepoint.com/10-example-json-files/)在 2017 年年中用最新的信息和新鲜的例子重写。*

谷歌地图是谷歌于 2005 年推出的在线地图服务。它提供多种服务，包括路线规划、卫星图像、实时交通更新等。就我个人而言，我经常用它来寻找会议地点的方向。

在手机领域，谷歌地图已经成为最受欢迎的智能手机应用之一，根据 GlobalWebIndex 进行的[调查，谷歌地图在 2013 年排名第一。它还有一个 API，允许开发者把它嵌入到他们的网站上。](http://www.businessinsider.com/google-smartphone-app-popularity-2013-9?IR=T#infographic)

在这个例子中，我将向您展示如何在谷歌地图上设置多个标记。我们可以使用下面的 JSON 格式，通过它的 API 向 Google Maps 提供标记信息。这些数据可以存储在可以远程访问的文件中，或者存储在可以通过本地 API 服务检索的数据库中。

**标记 JSON 数据:**

```
{
  "markers": [
    {
      "name": "Rixos The Palm Dubai",
      "position": [25.1212, 55.1535],
    },
    {
      "name": "Shangri-La Hotel",
      "location": [25.2084, 55.2719]
    },
    {
      "name": "Grand Hyatt",
      "location": [25.2285, 55.3273]
    }
  ]
} 
```

如果你正在实现一个长期的解决方案，你将需要一个 [API 密匙](https://developers.google.com/maps/documentation/javascript/adding-a-google-map#key)。一旦你有了它，你可以像这样在你的代码中使用它:

**API 密钥用法**:

```
<script async defer
  src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=loadMap">
</script> 
```

对于这个例子，我们将在没有密钥的情况下访问谷歌地图 API。预计会得到一个警告(在控制台中)。为了简单起见，我将在一个 HTML 文件中展示完整的工作代码。

**HTML + JavaScript** :

```
<!DOCTYPE html>
<html>
  <head>
   <title>Dubai Hotels</title>
  </head>

  <body onload = "loadMap()">
    <h2>Dubai Hotels</h2>
    <div id = "map" style = "width:640px; height:480px;"></div>
      <script> // fake JSON call
        function getJSONMarkers() {
          const markers = [
            {
              name:  "Rixos The Palm",
              location: [25.1212, 55.1535]
            },
            {
              name: "Shangri-La Hotel",
              location: [25.2084, 55.2719]
            },
            {
              name: "Grand Hyatt",
              location: [25.2285, 55.3273]
            }
          ];
          return markers;
        }

        function loadMap() {
          // Initialize Google Maps
          const mapOptions = {
            center:new google.maps.LatLng(25.2048, 55.2708),
            zoom: 11
          }
          const map = new google.maps.Map(document.getElementById("map"), mapOptions);

          // Load JSON Data
          const hotelMarkers = getJSONMarkers();

          // Initialize Google Markers
          for(hotel of hotelMarkers) {
            let marker = new google.maps.Marker({
              map: map,
              position: new google.maps.LatLng(hotel.location[0], hotel.location[1]),
              title: hotel.name
            })
          }
        } </script>
      <script src = "https://maps.googleapis.com/maps/api/js"></script>
  </body>
</html> 
```

要了解这里使用的 Google Map 类的更多信息，您可以查看以下参考手册:

*   [地图类](https://developers.google.com/maps/documentation/javascript/3.exp/reference#Map)
*   [标记等级](https://developers.google.com/maps/documentation/javascript/3.exp/reference#Marker)。

如果你稍微钻研一下文档，你可以学习如何给你的谷歌地图标记添加动画和自定义图标。要了解更多，请查看教程[使用地理定位&谷歌地图 API](https://www.sitepoint.com/working-with-geolocation-and-google-maps-api/) 和[谷歌地图使用 GMaps](https://www.sitepoint.com/google-maps-made-easy-with-gmaps-js/) 变得简单。

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章