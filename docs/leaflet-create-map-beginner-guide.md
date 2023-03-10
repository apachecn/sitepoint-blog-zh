# 使用 fleed . js 创建地图的初学者指南

> 原文：<https://www.sitepoint.com/leaflet-create-map-beginner-guide/>

**fleet . js 是目前最流行的[地图库](https://leafletjs.com/)之一。这是一个灵活、轻量级、开源的 JavaScript 库，用于创建交互式地图。**

传单是展示地图数据的框架。数据和底图图层必须由开发人员提供。地图由切片图层以及浏览器支持、默认交互性、平移和缩放功能组成。你也可以添加更多的自定义层和插件，以及传单中的所有映射。这个地图库将您的数据转换为地图图层，并具有出色的支持，使其成为大多数开发人员的首选。它在主要的桌面和移动平台上都能很好地工作，使它成为移动和大屏幕地图的完美 JavaScript 库。

在本教程中，我将向你展示如何用 HTML，CSS 和传单创建一个漂亮的南太平洋交互式地图，突出最受欢迎的海滩。我从猫途鹰网站上收集了数据，并整理了 2021 年旅行者之选投票中排名的南太平洋十大海滩。

您是否看到了一些有趣的在线地图，并希望自己创建一个？跟随这一激动人心的旅程，我会向你展示如何绘制一个很酷的地图，并使用传单突出十大海滩。

![Leaflet map gif showing the different layers](img/a21533564afca748dcd472ba0d95f30d.png)

## 分四步创建基本传单地图

用传单构建简单地图的过程很简单。一些 HTML 和 JavaScript 的背景知识是有益的，但是如果你是一个完全的初学者也不用担心。使用这个 JavaScript 库非常简单，在我创建这个令人惊叹、见解深刻的地图时，我将带您浏览每一行代码。

### 创建基本的 HTML 页面

首先，我创建一个 HTML 页面来呈现地图对象。然后，我添加一个`</div>`来保存地图，并给它一个类似于`map`的 ID，以便以后引用。接下来，我添加一些样式细节，将宽度和高度指定为`100vw`和`100vh`。这将使地图占据整个页面:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Leaflet Map</title>
    <style type="text/css"> body{
        margin: 0;
        padding: 0;
      }
      #map {
        width: 100vw;
        height: 100vh;
      } </style>
  </head>
  <body>
    <div id="map"></div>
  </body>
</html> 
```

### 参考开源传单 JavaScript 库的文件

因为我在使用传单库，所以我需要包含这个库的必要的 JavaScript 和 CSS 文件。您可以[直接下载文件，使用 JavaScript 包管理器(npm)在本地安装文件，或者从他们的 CDN 使用托管版本](https://leafletjs.com/download.html):

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Leaflet Map</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css" integrity="sha512-xwE/Az9zrjBIphAcBb3F6JVqxf46+CDLwfLMHloNu6KEQCAWi6HcDUbeOfBIptF7tcCzusKFjFw2yuvEpDL9wQ==" crossorigin="" />
    <style type="text/css"> body{
        margin: 0;
        padding: 0;
      }
      #map {
        width: 100vw;
        height: 100vh;
      } </style>
  </head>
  <body>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js" integrity="sha512-gZwIG9x3wUXg2hdXF6+rVkLF/0Vi9U8D2Ntg4Ga5I5BZpVkVxlJWbSQtXPSiUTtC0TjtGOmxa1AJPuV0CPthew==" crossorigin=""></script>
    <script> // All the code for the leaflet map will come here </script>
  </body>
</html> 
```

*注意:`integrity`属性允许浏览器检查获取的脚本，以确保如果源代码被操纵，代码不会被加载。*

### 准备数据

为了绘制任何地图，我需要像纬度和经度这样的坐标值。我整理了这个网站[这里](https://www.latlong.net/)的每个数据点的纬度和经度。此外，对于传单开发，我还需要基础层，我从一个名为 [OpenStreetMap](https://www.openstreetmap.org/#map=4/-19.64/142.38) 的网站获得。

### 设置传单地图

现在有趣的部分来了，通过写几行代码来创建地图。你不会相信用传单创建全功能地图只需要几行代码。这种易于开发的特性，以及 fleet 是一个开源 JavaScript 库的事实，使它在映射库列表中名列前茅。

所以，首先，记住这个 JavaScript 库中的所有东西都是通过字母“L”来访问的，所有的函数都是通过它来扩展的。

#### 初始化地图

我做的第一件事是声明 map 变量并用传单地图初始化它。第一个参数是之前定义的`<div>`的 ID。第二个是你希望地图的中心在哪里。最后是缩放级别。我把变焦调到了 3.5 倍，但是你可以把它调到你喜欢的任何值。

我在我的地图上使用这些参数，但是有很多不同的选项可以用来设置地图、交互、动画和事件的状态，你可以在这里查看:

```
const map = L.map('map', {
  center: [-29.50, 145],
  zoom: 3.5
}); 
```

#### 添加基础层

接下来，我添加了瓷砖层，这将是传单地图的基础层。切片图层是通过直接 URL 请求在服务器上访问的一组切片。该切片图层将地理边界添加到地图中。

请务必包括属性文本，因为大多数开发人员忘记了这一点:

```
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', { attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors' }).addTo(map); 
```

#### 添加默认标记

为了标明海滩，我添加了标记。传单默认提供此功能。因为我需要显示 10 个海滩，所以我将添加一个标记，每个标记带有相应海滩的纬度和经度值:

```
const marker1 = L.marker([-37.699450, 176.279420]).addTo(map);
const marker2 = L.marker([-27.643310, 153.305140]).addTo(map);
const marker3 = L.marker([-33.956330, 122.150270]).addTo(map);
const marker4 = L.marker([-34.962390, 117.391220]).addTo(map);
const marker5 = L.marker([-17.961210, 122.214820]).addTo(map);
const marker6 = L.marker([-16.505960, 151.751520]).addTo(map);
const marker7 = L.marker([-22.594400, 167.484440]).addTo(map);
const marker8 = L.marker([-37.977000, 177.057000]).addTo(map);
const marker9 = L.marker([-41.037600, 173.017000]).addTo(map);
const marker10 = L.marker([-37.670300, 176.212000]).addTo(map); 
```

瞧啊。一个绝对可爱又实用的传单地图已经准备好了。传单的制作不是轻而易举的吗？

下图显示了目前为止这一切的样子。

![Leaflet map initial version](img/cd815468163215f9a19342960743c513.png)

您可以在这支笔中找到完整的代码[:](https://codepen.io/SitePoint/pen/jOGrLbY?editors=0010)

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
)的 Pen [传单 Top South Pacific Beaches-Initial](https://codepen.io/SitePoint/pen/jOGrLbY)。