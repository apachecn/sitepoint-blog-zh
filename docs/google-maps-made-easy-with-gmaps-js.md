# GMaps.js 让谷歌地图变得简单

> 原文：<https://www.sitepoint.com/google-maps-made-easy-with-gmaps-js/>

事实证明，谷歌地图是一项非常成功的谷歌服务，整合了一系列宝贵的工具，如[街景](https://www.google.co.in/maps/streetview/)、[路线规划](https://www.google.com/maps/d/viewer?mid=zkGXyPnVyZmg.kzMXGP46cV-4&hl=en_US)和[谷歌交通](https://en.wikipedia.org/wiki/Google_Traffic)。许多公司和组织依靠谷歌地图来提供他们的服务——多亏了谷歌地图 API，他们才能做到这一点。

## 谷歌地图 API 和 GMaps

谷歌在 2005 年推出了谷歌地图 API。这使得开发人员可以创建带有地图的自定义应用程序。谷歌随后推出了用于 Android 和 iOS 应用开发的 API。

尽管 Maps APIs 很有用，但是使用它们需要一些技巧。这就是 GMaps.js 的用武之地。GMaps.js 是一个开源的、麻省理工学院许可的 JavaScript 库。由[古斯塔沃·莱昂](https://github.com/hpneo)编写的 GMaps 旨在简化最初的谷歌地图 JavaScript API。它负责处理大量的 API 代码，并提供适当的方法来处理地图。它更干净，更简洁，因此更容易使用。

在这篇文章中，我们将看看地图的组成部分，如标记，多边形和叠加。我们还将讨论静态地图，以及使用地理定位和地理编码来操作用户的位置。所有这些都将参考 GMaps。它可以帮助您使用简单易用的方法创建地图应用程序。我已经用它创建了一个示例应用程序( [Mapit](https://shivammg.github.io/mapit/) )，我将在文章的最后进一步讨论它。

## Google API 和 GMaps 比较

下面的例子来自[原始文档页面](https://developers.google.com/maps/documentation/javascript/examples/map-simple)。代码(仅 JavaScript)加载一张地图，其中心在纬度`-34.397`和经度`150.644`，缩放级别为`8`:

```
var map;
function initMap() {
  map = new google.maps.Map(document.getElementById('map'), {
    center: {lat: -34.397, lng: 150.644},
    zoom: 8
  });
}
```

`map`是加载地图的 HTML 元素 id。

我们可以用 GMaps 编写相同的基本应用程序，如下所示:

```
new GMaps({
  el: '#map',
  lat: -34.397,
  lng: 150.644,
  zoom: 8
});
```

本教程将引导您了解地图中最常用的一些组件，并用示例笔来演示每个组件。

## 入门指南

创建一个基本的 HTML 页面，并添加对 Maps API 的引用。您还需要包括 GMaps 库。所以去 [GMaps](https://hpneo.github.io/gmaps/) 下载 **gmaps.js** 。包括在你的网页中，你就可以开始了。

```
<!doctype html>
<html>
  <head>
    <style> #map {
        width: 400px;
        height: 400px;
      } </style>
  </head>
  <body>
    <div id="map"></div>

    <!-- Google Maps JS API -->
    <script src="https://maps.googleapis.com/maps/api/js"></script>
    <!-- GMaps Library -->
    <script src="gmaps.js"></script>
    <script> /* Map Object */
      var mapObj = new GMaps({
        el: '#map',
        lat: 48.857,
        lng: 2.295
      }); </script>
  </body>
</html>
```

这是一个基本的页面，我将在下面的示例片段中引用。在某些示例中，地图对象将被修改。

## 成分

地图 API 提供了各种自定义地图的组件。同样的组件可以用更少的代码添加到 GMaps 中。

### 事件

HTML DOM(文档对象模型)中的变化可以被描述为一个事件。您可以在地图上发生特定事件时调用一个函数(比如双击或鼠标悬停)。以下代码片段定义了`click`和`zoom_changed`事件的函数:

```
var mapObj = new GMaps({
  el: '#map',
  lat: 48.857,
  lng: 2.295,
  click: function(e) {
    alert('You clicked on the map');
  },
  zoom_changed: function(e) {
    alert('You zoomed the map');
  }
});
```

如果您愿意，可以添加其他事件。在[文档](https://developers.google.com/maps/documentation/javascript/reference#Map)的*事件*部分提供了所有地图事件的列表。一些有用的是:

| 事件 | 描述 |
| --- | --- |
| `dblclick` | 双击鼠标 |
| `mouseover` | 鼠标进入地图 |
| `mouseout` | 鼠标退出地图 |
| `drag` | 地图被拖动 |
| `rightclick` | 鼠标右键单击 |

[**例笔**](http://codepen.io/SitePoint/pen/aa82eb49220c499b2e1642ef9d833685/)

### 标记

标记是地图上的定位器。它通常显示为悬挂在标记位置上方的气球。GMaps 提供了添加标记的`addMarker()`方法。它接受具有以下标记属性的对象文字:

*   **lat** : Latitude
*   **lng** :经度
*   **标题**:鼠标悬停时显示的标题
*   **图标**:标记的自定义图像
*   **细节**:带额外数据的自定义对象
*   **信息窗口**:标记的信息

其中，必须给 **lat** 和 **lng** 赋值，而其他是可选的。 **infoWindow** 的值应该是另一个对象。该对象本身具有以下属性:

*   **内容** : HTML 内容
*   **maxWidth** :窗口的最大宽度。如果未设置，窗口将跨越其中文本的长度。

**信息窗口**支持部分[更多选项](https://developers.google.com/maps/documentation/javascript/reference#InfoWindowOptions)。

这个片段是`addMarker()`的一个有效例子:

```
var m = mapObj.addMarker({
  lat: 48.8583701,
  lng: 2.2944813,
  title: 'Eiffel Tower',
  infoWindow: {
    content: '<h4>Eiffel Tower</h4><div>Paris, France</div>',
    maxWidth: 100
  }
});
```

`addMarker()`也接受事件，例如，对鼠标悬停事件做出反应的标记:

```
mapObj.addMarker({
  lat: 48.8583701,
  lng: 2.2944813,
  mouseover: function(e) {
    alert('Triggered');
  }
});
```

[**例笔**](http://codepen.io/SitePoint/pen/f042bf6979a35fe95f6a3265283e9679/)

可使用`removeMarker()`方法移除标记。将标记对象作为参数传递给它(在我们的例子中是 T1 ):

```
mapObj.removeMarker(m);
```

`removeMarkers()`集体删除所有与地图对象相关的标记。

```
mapObj.removeMarkers();
```

### 地理编码

要定位地图上的任何一点，你需要有它的地理坐标(*纬度*和*经度*)。地理编码是根据给定的位置地址计算这些地理坐标。`geocode()`方法允许我们做同样的事情。它将位置地址作为输入，并处理该地址的坐标。

*   **地址**:位置地址串
*   **回调**:地理编码后调用的函数

让我们计算一下<q>巨石阵</q>位于<q>英国</q>的经纬度。下面的代码片段将计算给定地址的地理坐标，并将地图置于该位置的中心。如果没有找到结果，将显示一条消息:

```
GMaps.geocode({
  address: 'Stonehenge, United Kingdom',
  callback: function(results, status) {
    if (status == 'OK') {
      latlng = results[0].geometry.location;
      mapObj.setCenter(latlng.lat(), latlng.lng());
    } else if (status == 'ZERO_RESULTS') {
      alert('Sorry, no results found');
    }
  }
});
```

> `setCenter()`方法以经纬度为参数，将地图居中于该地理位置。它还接受一个可选的回调参数，这是一个在地图居中后触发的函数。

回调函数中有两个参数:`results`和`status`。

`results`是一个对象数组，存储定义了地址的所有地点的位置。因为可以有多个同名的地点，所以可以有多个结果。`results`存储每一个。使用`results[i].geometry.location`可以确定第 i <sup>个</sup>结果的位置。

如果没有为一个地址找到结果，`status`存储<q>零结果</q>，否则<q>确定</q>。

[**例笔**](http://codepen.io/SitePoint/pen/7ae7e4ad67716fdc839152bc42e6183c/)

### 地理定位

地理定位计算用户当前的地理位置。`geolocate()`方法让我们利用这个特性。它接受一个具有四个属性的对象文字，其中**总是**是可选的，其他都是必需的。每个属性都被定义为在特定情况下执行的函数:

*   **成功**:地理定位成功
*   **错误**:地理定位不成功
*   **不支持**:浏览器不支持地理定位
*   **始终**:在所有情况下执行

```
GMaps.geolocate({
  success: function(position) {
    mapObj.setCenter(position.coords.latitude, position.coords.longitude);
  },
  error: function(error) {
    alert('Geolocation failed: ' + error.message);
  },
  not_supported: function() {
    alert("Your browser does not support geolocation");
  },
  always: function() {
    alert("Always");
  }
});
```

[**例笔**](http://codepen.io/SitePoint/pen/d1c8c9fef44bb90b1054d1f03d1a84ce/)

### 多叉线

折线有助于在地图上描绘路径。通过连接不同点的坐标可以形成一条路径。方法为路径绘制一条折线。这个路径以坐标数组的形式提供(*纬度*和*经度*)。除了**路径**，您可以为折线指定其他属性。其中一些是:

*   **冲程重量**
*   **斯特洛克色**
*   **strokeOpacity**

这三者定义了多段线的样式。还有其他的[和](https://developers.google.com/maps/documentation/javascript/reference#PolylineOptions)，尽管我们不会在本文中涉及它们。

```
var path = [
  [-12.044012922866312, -77.02470665341184],
  [-12.05449279282314, -77.03024273281858],
  [-12.055122327623378, -77.03039293652341],
  [-12.075917129727586, -77.02764635449216],
  [-12.07635776902266, -77.02792530422971],
  [-12.076819390363665, -77.02893381481931],
  [-12.088527520066453, -77.0241058385925]
];

var pl = mapObj.drawPolyline({
  path: path,
  strokeColor: '#76ff03',
  strokeOpacity: 1,
  strokeWeight: 10
});
```

[**例笔**](http://codepen.io/SitePoint/pen/49d3f9052b954d167850814ebd9b6800/)

可使用`removePolyline()`方法删除折线。它将折线对象作为其参数。使用以下工具拆下`pl`聚酰亚胺:

```
mapObj.removePolyline(pl);
```

`removePolylines()`删除所有与地图对象相关的折线。

```
mapObj.removePolylines();
```

### 多边形

帮助你在地图上创建一个多边形。非常类似于`drawPolyline()`方法，您需要定义一个**路径**属性。多边形的笔画样式属性( **strokeWeight** 、 **strokeColor** 和 **strokeOpacity** )也是如此。它们定义了多边形的边界。除此之外，您还可以指定多边形的填充颜色和不透明度:

*   **填充颜色**
*   **填充不透明度**

其他多边形选项可在[文档](https://developers.google.com/maps/documentation/javascript/reference#PolygonOptions)中找到。

```
var path = [
  [-12.040397656836609, -77.03373871559225],
  [-12.040248585302038, -77.03993927003302],
  [-12.050047116528843, -77.02448169303511],
  [-12.044804866577001, -77.02154422636042]
];

var pg = mapObj.drawPolygon({
  paths: path,
  strokeColor: '#000000',
  strokeOpacity: 0.3,
  strokeWeight: 2,
  fillColor: '#00e676',
  fillOpacity: 0.4
});
```

**记住:**是`drawPolyline()`的**路径**属性，`drawPolygon()`的**路径**属性。

[**例笔**](http://codepen.io/SitePoint/pen/6ed6d65b715dfacdb9d4588bfb5436d8/)

要删除多边形`pg`，使用:

```
mapObj.removePolygon(pg);
```

删除在`mapObj`中定义的所有多边形:

```
mapObj.removePolygons();
```

### 环

用`drawPolygon()`创建圆形不可行。`drawCircle()`帮你搞定。其参数列表包括:

*   **lat** :中心纬度
*   **lng** :中心经度
*   **半径**:地球表面以米为单位的半径。

其他选项包括形状的笔画和填充样式(与多边形相同)，以及一些更多的。

```
var c = mapObj.drawCircle({
  lat: 51.178875,
  lng: -1.826259,
  radius: 300,
  fillColor: 'yellow',
  fillOpacity: 0.5,
  strokeWeight: 0,
  click: function(e){
    alert('You are inside 300m radius of Stonehenge')
  }
});
```

[**例笔**](http://codepen.io/SitePoint/pen/9e98d1c1ed4521c791b82a9d4ff23ad5/)

### 遮掩

叠加是地图上带有 HTML 内容的图层。GMaps 支持使用`drawOverlay()`方法叠加。它接受以下哈希:

*   **lat** : Latitude
*   **lng** :经度
*   **内容** : HTML 内容
*   **垂直对齐**:上、中、下
*   **水平褐藻胶**:左、中、右
*   **垂直偏移**
*   **水平偏移**

对准和偏移是相对于由**纬度**和**液化天然气**定义的点。

示例片段:

```
var ol = mapObj.drawOverlay({
  lat: 27.1733151,
  lng: 78.0409684,
  content: '<div class="overlay">Taj Mahal</div>',
  verticalAlign: 'top',
  horizontalOffset: -40
});
```

您可以为内容定义 CSS 样式。在我们的例子中，我们已经定义了`overlay`类。

```
.overlay {
  border: 1px solid #b71c1c;
  background: #d50000;
  padding: 5px;
  font-size: 17px;
  color: white;
}
```

[**例笔**](http://codepen.io/SitePoint/pen/f18136cf01ad2606ffc1b24a01765075/)

移除覆盖？你知道的。

```
mapObj.removeOverlay(ol);
```

移除地图对象的所有覆盖？你也知道:

```
mapObj.removeOverlays();
```

### 静态地图

静态地图是地图的图像，可以独立地嵌入到网站中。GMaps 允许您生成静态地图的 URL。然后，可以将该 URL 作为源添加到图像中。

`staticMapURL()`取以下参数后生成所需的地图 URL:

*   **size** :以像素为单位的宽度和高度数组
*   【T0 年】T1 年
*   **lng**
*   **缩放**

代码片段:

```
url = GMaps.staticMapURL({
  size: [500, 500],
  lat: 51.178882,
  lng: -1.8284037,
  zoom: 16
});
```

[**例笔**](http://codepen.io/SitePoint/pen/6927aa0eb0f2240bbbef9ae72cceb1f8/)

在我们的例子中，我们创建了一个`img`元素，并将 URL 添加到它的`src`中:

```
var map = document.getElementById('map');
var static_img = document.createElement('img');
static_img.src = url;
map.appendChild(static_img);
```

我们也可以将标记和折线应用于静态地图。

[**例笔**](http://codepen.io/SitePoint/pen/ad85256759f32f1dc3639e4570c0a356/)

## 示例应用程序(Mapit)

[**Mapit**](https://shivammg.github.io/mapit/) ( [在 GitHub 上查看源](https://github.com/sitepoint-editors/mapit))为源和目的地之间的路线创建静态地图。缩小到地图上的一个地址，并放置两个标记(源和目的地)。Mapit 会追踪从一个标记到另一个标记的路线。它将使用当前配置创建静态映射的 url。您可以预览静态地图并下载图像。

## 结论

这篇文章涵盖了地图的基本组成部分。我确信它已经成为 GMaps 和交互式地图应用的入门指南。但不应该就此打住。使用 [GMapsjs](https://hpneo.github.io/gmaps/) 你可以做更多的事情。

你用过 GMaps 吗？如果有，你有什么经验。如果您有任何意见或问题，请加入下面的讨论。

## 分享这篇文章