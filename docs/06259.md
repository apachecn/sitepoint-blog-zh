# 使用地理编码器 PHP 和 Leaflet.js 制图

> 原文：<https://www.sitepoint.com/mapping-geocoder-php-leaflet-js/>

web 应用程序中的交互式地图有很多很好的用途。从可视化数据到突出显示感兴趣的点，人们期望地图能够轻松地在位置环境中交流思想。

然而，最困难的部分是将数据转换成地图可以理解的坐标。幸运的是， [Geocoder PHP](http://geocoder-php.org/Geocoder/) 允许我们连接到不同的地理编码提供商。结合简单的 Javascript 库[fleet . js](http://leafletjs.com/)，创建地图轻而易举。

## 开始

用 [Composer](http://getcomposer.org) ，包括 Geocoder PHP 库很简单:

```
{
  "require": {
    "willdurand/geocoder": "*"
  }
}
```

让我们在一个简单的`index.php`文件中添加一些 html 来包含 Bootstrap，这样我们就有一个漂亮的环境来显示我们的地图:

```
<?php
require 'vendor/autoload.php';

?>
<!DOCTYPE html>
<html>
<head>
    <title>A simple map with Geocoder PHP and Leaflet.js</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css">
</head>
<body>
<div class="container">
    <div class="row">
        <div class="col-lg-12 page-header">
            <h1 id="header">A simple map with Geocoder PHP and Leaflet.js</h1>
        </div>
        <div class="row-fluid">
            <div class="col-lg-8">

            </div>
        </div>
    </div><!-- /row -->
</div> <!-- /container -->
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script src="//netdna.bootstrapcdn.com/bootstrap/3.0.0/js/bootstrap.min.js"></script>
</body>
</html>
```

## 设置地理编码器

Geocoder 宣称自己是 PHP 中缺失的 geocoder 库。它可以在三个简单的步骤中使用:

1.  注册适配器
2.  注册提供商
3.  地理编码！

### 注册适配器

适配器充当通过 API 连接到您选择的提供者并获取数据的机制。一些适配器使用 PHP 5.3+中的内置功能，如 cURL 和 sockets。其他的，如 Buzz、Guzzle 和 Zend HTTP Client，使用第三方开源库，只需要您将它们的依赖项添加到您的 composer 文件中。

地理编码器库的美妙之处在于适配器步骤的抽象。如果您的需求发生变化，它允许您更换适配器，而不需要您重写应用程序接收数据的方式。

对于这个例子，我们将使用 cURL 和地理编码器 PHP 库中包含的 CurlHTTPAdapter 类。

在我们的`index.php`文件中，让我们添加适配器:

```
// Setup geocoder adapter.
$adapter = new \Geocoder\HttpAdapter\CurlHttpAdapter();
```

### 注册提供商

地理编码器 PHP 库支持许多现成的地理编码提供程序，包括[谷歌地图](http://code.google.com/apis/maps/documentation/geocoding/)、[必应地图](http://msdn.microsoft.com/en-us/library/ff701715.aspx)、[通过 Openstreetmap 进行命名](http://wiki.openstreetmap.org/wiki/Nominatim)，以及 [TomTom](http://developer.tomtom.com/docs/read/Geocoding) 。

完整的列表可以在 Geocoder PHP 仓库的 [README](https://github.com/geocoder-php/Geocoder/blob/master/README.md) 中找到。

由各自的类表示的每个提供程序都有自己的选项。根据您的需要，您可以针对各种情况注册多个提供商。如果您的应用程序需要使用 Openstreetmap 绘制哥斯达黎加圣何塞的特定街道地图，并使用百度查找中国北京的快捷路线，这可能会很有用。

对于这个例子，我将简单地使用 Google Maps，但是以这样一种方式注册它，如果我将来想要添加另一个提供者，我只需要将它添加到一个数组中:

```
// Create a chain of providers.
// Be sure to include my previously created adapter.
$chain = new \Geocoder\Provider\ChainProvider(
    array(
        new \Geocoder\Provider\GoogleMapsProvider($adapter),
    )
);

// Instantiate the geocoder.
$geocoder = new \Geocoder\Geocoder();

// Register my providers.
$geocoder->registerProvider($chain);
```

### 地理编码

我们现在可以将地址传递给新实例化的`$geocoder`对象中的`geocode()`方法。这将返回一个通过前面选择的提供者实例化的结果对象。这个结果对象有我们可以使用的`getLatitude()`和`getLongitude()`方法。

```
// Demo locations
$locations = array(
    array(
        'address' => '3324 N California Ave, Chicago, IL, 60618',
        'title' => 'Hot Dougs',
    ),
    array(
        'address' => '11 S White, Frankfort, IL, 60423',
        'title' => 'Museum',
    ),
    array(
        'address' => '1000 Sterling Ave, , Flossmoor, IL, 60422',
        'title' => 'Library',
    ),
    array(
        'address' => '2053 Ridge Rd, Homewood, IL, 60430',
        'title' => 'Twisted Q',
    )
);

foreach ($locations as $key => $value) {
    // Try to geocode.
    try {
        $geocode = $geocoder->geocode($value['address']);
        $longitude = $geocode->getLongitude();
        $latitude = $geocode->getLatitude();

    } catch (Exception $e) {
        echo $e->getMessage();
    }
}
```

## 与 fleed . js 集成

fleed . js 是一个强大的 javascript 库，它使得映射变得非常容易。

地图由三部分组成:

1.  瓷砖
2.  交互层(通常通过 Javascript 和 CSS)
3.  数据点

单幅图块是显示地图细节的 256 x 256 像素的正方形。像 Mapbox 和 Cloudmade 这样的服务允许你创建自己的 tilesets。对于这个例子，我已经用 Cloudemade 创建了一个免费帐户，并将使用给定的 API 键来显示来自其托管服务的切片。

交互层由 fleed . js 处理。我只是将 fleed Javascript 和 CSS 库包含到我们的 starter HTML 模板中:

```
<link rel="stylesheet" href="//cdn.leafletjs.com/leaflet-0.6.4/leaflet.css" />
<script src="//cdn.leafletjs.com/leaflet-0.6.4/leaflet.js"></script>
```

数据点已经用我的地理编码器代码创建过了。我只需按照传单期望的方式格式化数据数组。

在这个简单的例子中，我将创建单独的标记作为 Javascript 变量，这些变量将通过 PHP 生成的字符串包含在我的地图中。

传单可以选择将这些数据通过 [geoJSON](http://leafletjs.com/examples/geojson.html) 格式传递给更大、更动态的数据集。

```
$mapdata = $marker_group = array();

foreach ($locations as $key => $value) {
    // Try to geocode.
    try {
        $geocode = $geocoder->geocode($value['address']);
        $longitude = $geocode->getLongitude();
        $latitude = $geocode->getLatitude();

        // Create map data array
        $mapdata[] = markerCreator($latitude, $longitude, $value['title'], $key);

        // Marker grouping array
        $marker_group[] = "marker{$key}";

    } catch (Exception $e) {
        echo $e->getMessage();
    }
}

function markerCreator($lat, $long, $label, $key) {
    return "var marker{$key} = L.marker([{$lat}, {$long}]).addTo(map);
    marker{$key}.bindPopup(\"{$label}\");";
}
```

因为 fleet 将地图代码注入到现有的 DOM 元素中，所以我们首先必须在 HTML 中定义该元素。我们可以通过创建一个具有唯一 id 的 div 来实现这一点:

`<div id="map"></div>`

然后，我们可以通过调用内置的`map()` Javascript 方法在传单中定位 id，并在页脚中传递我们的 id:

```
var map = L.map('map');
```

现在，我们构建地图的三个部分。要注册图块，我们只需调用内置的`tileLayer()`方法，定义属性和缩放级别(如果需要的话),然后添加`addTo()`方法:

```
L.tileLayer('//{s}.tile.cloudmade.com/41339be4c5064686b781a5a00678de62/998/256/{z}/{x}/{y}.png', {maxZoom: 18}).addTo(map);
```

最后，我们使用前面定义的 PHP 数组打印地图数据，并通过将这些数据点定义为一个组来确保地图以这些数据点为中心。总而言之，页脚中的 Javascript 应该是:

```
<script>
    var map = L.map('map');

    L.tileLayer('//{s}.tile.cloudmade.com/41339be4c5064686b781a5a00678de62/998/256/{z}/{x}/{y}.png', {maxZoom: 18}).addTo(map);

    <?php print implode('', $mapdata); ?>

    var group = new L.featureGroup([<?php print implode(', ', $marker_group); ?>]);
    map.fitBounds(group.getBounds());
</script>
```

如果你已经走了这么远，你会发现什么都没发生。

这是因为 fleet 没有在 map div 的高度或宽度上注入属性，允许您随意设置样式和调整大小。只需给你的 div 一个高度和宽度，你的地图就应该出现了！

## 结论

您可以使用 Geocoder PHP 库和 fleet . js 创建漂亮的交互式地图。请务必查看每个项目的相应文档，因为还有许多更高级的自定义功能。

[查看这篇文章的演示](http://geocoder.fredric.is-a-geek.org/)或者[在 Github 上把它](https://github.com/fmitchell/geocoder-demo)交出来。

## 分享这篇文章