# 你在哪里？用地理编码器 PHP 实现地理定位

> 原文：<https://www.sitepoint.com/implementing-geolocation-geocoder-php/>

对我来说，SitePoint 的美妙之处在于，你可以受到启发去尝试一些东西，或者被告知一些很酷的项目。互联网太大了，一个人无法独自探索。地理编码器对我来说是其中之一。我从未听说过它，但在作者的特雷罗论坛上偶然发现了它。

我喜欢处理地图和地理信息，我为一个客户做的项目大量使用(反向)地理编码；[索道](http://www.cabletracks.com)。我们实际上为此使用了付费服务，尽管不是所有的服务。付费结果比你从免费服务中获得的信息要多得多。我发现地理编码器 PHP 实际上是我所缺少的集成我们使用的各种服务的工具。

地理编码器 PHP 提供了:“地理编码操作的抽象层”。这个库分为三个部分:一个用于请求的 HttpAdapter、几个地理编码提供程序和各种用于输出格式化的格式化程序/转储程序。

### 装置

使用 composer 最容易安装地理编码器。将以下内容添加到您的 **composer.json** 中:

```
{
    "require": {
        "willdurand/geocoder": "@stable"
    }
}
```

或者从 [Geocoder PHP 网站](http://geocoder-php.org/)获取其中一个存档。

### 地理编码

我们首先来看看地理编码地址。这种输入通常是街道地址，但也可以是邻居、区域、地点、州或国家。地理编码输出应该有一个很好的指针，指示您应该在地球上的哪个位置查找您用作输入的内容。当然，结果取决于所用地理编码器的质量。

要使用 Geocoder，您首先需要一个 HttpAdapter 来触发 web 服务上的请求。HttpAdaper 是一个 web 客户端，它实际上有 5 种不同的 PhpGeocoder 版本。你可以使用随时可用的基于 [CUrl](http://php.net/manual/en/book.curl.php) 或 [Socket](http://php.net/manual/en/book.sockets.php) 的客户端。此外，您可以添加 PHP 客户端，如 [Buzz 浏览器](https://github.com/kriswallsmith/Buzz)、 [Guzzle PHP HTTP 客户端](http://guzzle.readthedocs.org/en/latest/)、 [Zend HTTP](http://framework.zend.com/manual/2.0/en/modules/zend.http.client.html) 和 [GeoIP2 webservice API](https://github.com/maxmind/GeoIP2-php) 。如果您想使用它们，您需要自己将它们添加到项目中。

有了 HTTP 适配器，您就可以使用地理编码器来完成这项工作。支持的地理编码器列表很长，非常长。他们可以分为两组；

*   地理编码员: [Google Maps](http://code.google.com/apis/maps/documentation/geocoding/) 、 [OpenStreetMap](http://nominatim.openstreetmap.org/) 、 [Bing Maps](http://msdn.microsoft.com/en-us/library/ff701715.aspx) 、 [MapQuest](http://open.mapquestapi.com/) 、 [Yandex](http://api.yandex.com.tr/maps/doc/geocoder/desc/concepts/About.xml) 、 [GeoPlugin](http://www.geoplugin.com/webservices) 、 [Geonames](http://www.geonames.org/) 、 [ArcGIS Online](http://resources.arcgis.com/en/help/arcgis-online-geocoding-rest-api/) 、 [Google Maps for Business](https://developers.google.com/maps/documentation/business/webservices) 、 [TomTom](http://developer.tomtom.com/docs/read/Geocoding) 、 [OIORest](http://geo.oiorest.dk/) (仅限丹麦)【丹麦】
*   IP 地理编码器: [FreeGeoIp](http://freegeoip.net/static/index.html) ， [HostIp](http://www.hostip.info/) ， [IpInfoDB](http://www.ipinfodb.com/) ， [Geoip](http://php.net/manual/book.geoip.php) ， [GeoIPs](http://www.geoips.com/developer/geoips-api) ， [MaxMind web service](http://dev.maxmind.com/geoip/legacy/web-services) ， [IpGeoBase](http://ipgeobase.ru/) ， [DataScienceToolkit](http://www.datasciencetoolkit.org/)

大多数可以免费使用，有些需要你注册一个 API-key。大多数人会对你可以搜索到的查询数量有某种比率或每日限额。例如，谷歌将允许你每天处理 2500 个请求。地理编码服务的质量各不相同。一些支持特定的国家，通常对这些国家更好。但这不是我们在这里要评估的。这是你想自己测试的东西，因为结果将取决于你地理编码的地方。

关于所有的可能性说得够多了，让我们开始工作吧。我们先从一个简单的例子开始。

```
$lookfor = 'Laan van Meerdervoort, Den Haag, Nederland';

$adapter  = new \Geocoder\HttpAdapter\CurlHttpAdapter();
$geocoder = new \Geocoder\Geocoder();
$geocoder->registerProvider(new \Geocoder\Provider\GoogleMapsProvider($adapter));

$result = $geocoder->geocode($lookfor);
```

简单吧？这是你将得到的结果:

```
Geocoder\Result\Geocoded#1
(
    [*:latitude] => 52.0739343
    [*:longitude] => 4.2636776
    [*:bounds] => array
    (
        'south' => 52.0610866
        'west' => 4.2262026
        'north' => 52.0868446
        'east' => 4.3008719
    )
    [*:streetNumber] => null
    [*:streetName] => 'Laan Van Meerdervoort'
    [*:cityDistrict] => null
    [*:city] => 'The Hague'
    [*:zipcode] => null
    [*:county] => 'The Hague'
    [*:countyCode] => 'THE HAGUE'
    [*:region] => 'South Holland'
    [*:regionCode] => 'ZH'
    [*:country] => 'The Netherlands'
    [*:countryCode] => 'NL'
    [*:timezone] => null
)
```

这其实是 Geocoder PHP 最美的地方；无论您使用哪种地理编码器，无论您在哪个方向进行地理编码(正向/反向)，您都会得到相同的结果集。如果你想换一个旧的地理编码器，这是非常宝贵的。

关于我的地址选择；Laan van Meerdervoort 是荷兰最长的街道之一(长约 5800 米)。我漏掉了一个门牌号以得到完整的街道。纬度/经度和边界的差异使这一点现在变得很明显。在这种情况下，这些边界包含整条街道的边界框。这是非常实际的，你在你的结果中得到两者；街道的中心和边界。

大多数地理编码器可以被要求对地点或区域敏感，Geocoder 很好地将这一点与一个`LocaleAwareProviderInterface`集成在一起。例如，假设你是俄罗斯人，你需要去上面这条街上的和平宫，你可以这样使用它:

```
$geocoder->registerProvider(new \Geocoder\Provider\GoogleMapsProvider($adapter, 'Ru'));
$result = $geocoder->geocode('Laan van Medevoort 7, Den Haag, Nederland');
```

并获得:

```
Geocoder\Result\Geocoded#1
(
  [*:latitude] => 52.0739343
  [*:longitude] => 4.2636776
  [*:bounds] => array
  (
    'south' => 52.0610866
    'west' => 4.2262026
    'north' => 52.0868446
    'east' => 4.3008719
  )
  [*:streetNumber] => 7
  [*:streetName] => 'Laan Van Meerdervoort'
  [*:cityDistrict] => null
  [*:city] => 'Гаага'
  [*:zipcode] => null
  [*:county] => 'Гаага'
  [*:countyCode] => 'ГААГА'
  [*:region] => 'Южная Голландия'
  [*:regionCode] => 'ZH'
  [*:country] => 'Нидерланды'
  [*:countryCode] => 'NL'
  [*:timezone] => null
)
```

当然，您希望从地理编码中获得最佳结果。如果你在美国，你可能想使用一个专门支持美国的编码器，另一个支持美国以外的编码器。美妙之处在于 Geocoder 支持地理编码器的链接。然后，您可以向一组编码者抛出您的请求，第一个有效的结果将被返回。这很好，因为否则你将不得不尝试/捕捉一整套编码，直到你得到一个结果。
这样的一个例子:

```
$adapter  = new \Geocoder\HttpAdapter\CurlHttpAdapter();
$geocoder = new \Geocoder\Geocoder();
$chain    = new \Geocoder\Provider\ChainProvider([
  new \Geocoder\Provider\OpenStreetMapProvider($adapter),
  new \Geocoder\Provider\GoogleMapsProvider($adapter),
  new \Geocoder\Provider\BingMapsProvider($adapter, $bingApiKey),
]);
$geocoder->registerProvider($chain);
```

请注意，Geocoder 将返回第*个*有效结果。那可能不是你能得到的最好的！所以，在你把你的编码者链接在一起的时候，要注意顺序！

### 反向地理编码

反向地理编码是当你在地球上得到一个点时，通常是一个 GPS ( [WGS84](http://en.wikipedia.org/wiki/World_Geodetic_System) )坐标。如果你的输入在另一个投影中，你可以使用 PHP 版本的 [Proj4](http://sourceforge.net/projects/proj4php/) 来转换它。
一旦你有了一个地理编码器的实例，语法还是很简单的。你唯一需要记住的是，首先是纬度，然后是经度(如果你也不小心切换了纬度和经度，请举手……)。确保你的地理编码器支持反向地理编码，因为不是所有的都支持。我将对荷兰的地理中心进行地理编码:

```
$result = $geocoder->reverse(52.155247, 5.387452);
```

导致了这样的地理编码结果。

```
Geocoder\Result\Geocoded#1
(
  [*:latitude] => 52.1551992
  [*:longitude] => 5.3872474
  [*:bounds] => array
  (
    'south' => 52.1551992
    'west' => 5.3872474
    'north' => 52.1551992
    'east' => 5.3872474
  )
  [*:streetNumber] => '30'
  [*:streetName] => 'Krankeledenstraat'
  [*:cityDistrict] => 'Stadskern'
  [*:city] => 'Amersfoort'
  [*:zipcode] => '3811 BN'
  [*:county] => 'Amersfoort'
  [*:countyCode] => 'AMERSFOORT'
  [*:region] => 'Utrecht'
  [*:regionCode] => 'UT'
  [*:country] => 'The Netherlands'
  [*:countryCode] => 'NL'
  [*:timezone] => null
)
```

### 地理编码 IP 地址

我个人使用 IP 地址的地理编码来决定用什么语言显示一个网站(当然结合浏览器接受)。您将用户的 IP 地址(通常来自`$_SERVER['REMOTE_ADDR']`)提供给地理编码器，并获得一个结果。需要考虑的是您的服务器是否支持 IPv6，是否能够通过 IPv6 提供服务。如果是这样，那么您需要使用支持地理编码 IPv6 的服务，因为并非所有服务都支持地理编码 IPv6。

有几种可用的 IP 地理编码器，使用它们和使用其他的一样简单:

```
$adapter  = new \Geocoder\HttpAdapter\CurlHttpAdapter();
$geocoder = new \Geocoder\Geocoder();
$result   = $geocoder->registerProvider( new \Geocoder\Provider\FreeGeoIpProvider($adapter) )
                     ->geocode($_SERVER['REMOTE_ADDR']);
```

结果还是一个`Geocoded`结果。我就不举例子了，因为你会失望的。IP 地理编码器的结果在质量上差异很大。大多数情况下，你只会得到一个国家，其他什么都没有。如果你对你的 IP 地址的结果感兴趣，我为地理编码 IP 地址设置了一些[例子。](https://www.slatius.nl/experiments?id=geocoder)

上一个例子中需要注意的另一件事是，您应该*总是*在 try/catch 块中嵌套您的地理编码尝试。根据您使用的地理编码器的输出，地理编码器能够向您抛出几个异常。大多数都很明显，但可以通过添加几个捕捉条件来很好地处理:

```
try {
    $result = $geocoder->registerProvider( new \Geocoder\Provider\FreeGeoIpProvider($adapter) )
                       ->geocode($_SERVER['REMOTE_ADDR']);
} catch (NoResultException $e) {
    $result = "No result was returned by the geocoder";
} catch (QuotaExceededException $e) {
    $result = "We met our daily quota";
} catch (Exception $e) {
    $result = "Error: " . $e->getMessage();
}
```

### 输出格式

我想谈的最后一件事是输出格式。Geocoder 有几个输出格式器，允许您轻松地将其结果集成到应用程序中。有两种类型的输出:一个字符串`Formatter`，它有一个类似于`sprintf`的接口和`Dumpers`，允许特定语言的输出。

支持的输出转储器有 XML，如 GPS 交换格式(GPX)，用于与 Google Earth 接口的 Keyhole 标记语言(KML)，用于地理空间数据库的知名二进制(WKB)和知名文本(WKT)，以及用于简单 JavaScript 接口的 GeoJSON。

比如说；获取地理编码的结果，并将其提供给格式化程序或转储程序；

```
$adapter  = new \Geocoder\HttpAdapter\CurlHttpAdapter();
$GeoJSON  = new \Geocoder\Dumper\GeoJsonDumper;
$geocoder = new \Geocoder\Geocoder();
$geocoder->registerProvider( new \Geocoder\Provider\OpenStreetMapProvider($adapter) );
echo $GeoJSON->dump( $geocoder->reverse(52.155247, 5.387452) );
```

这将输出一个 GeoJSON 结果，该结果对于 [OpenLayers](https://www.sitepoint.com/openlayers-alternative-google-maps/) 非常有用，例如:

```
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [
            5.3872095538856,
            52.1551691
        ]
    },
    "properties": {
        "streetName": "Lieve Vrouwekerkhof",
        "zipcode": "3811 AA",
        "city": "Amersfoort",
        "cityDistrict": "Amersfoort",
        "region": "Utrecht",
        "country": "Nederland",
        "countryCode": "NL"
    }
}
```

### 结论

不得不称赞一下 Geocoder 中伟大的职业结构。这是一个真正的 OOPHP 库，它很好地利用了名称空间和接口。它非常容易使用。扩展它或者为它建立你自己的地理编码器应该是非常容易的。

如果直接的地理编码是你正在寻找的，那么这个库将为你工作。我个人认为更好的地理编码器还有发展空间，因为它有更强大的功能。如果您能够对地理编码链的请求设置条件，以便从所有编码者那里获得最佳结果，那就太好了。例如，结果集中的“至少返回一个街道名称”或“置信度> 75%”(某些地理编码器提供此信息)或“最大地址误差小于 500 米”(可以通过输出地址和输入坐标的差值计算得出)。当然，所有这些都取决于单个地理编码器的可能性，但简单的过滤肯定是可以做到的。

如果你对地理编码器本身感兴趣；我已经建立了一个[小演示](https://www.slatius.nl/experiments?id=geocoder)来检查各种地理编码器的结果。

你曾经使用过地理编码器吗，需要一个像这样的库，或者可能已经使用过了？请和我们分享你的想法。

## 分享这篇文章