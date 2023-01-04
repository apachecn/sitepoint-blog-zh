# 使用地理定位和 Google Maps API

> 原文：<https://www.sitepoint.com/working-with-geolocation-and-google-maps-api/>

当今最常用的 API 之一是地理定位 API。地理定位允许应用程序确定您的物理坐标，并为您量身定制应用程序。这有可能极大地增强您的用户体验。本文将向您展示如何将地理定位 API 与 Google Maps API 结合使用。

Google Maps API 允许您将 Google Maps 服务与您的网站集成在一起。本文中的例子使用的是 3.10 版的 API。该服务是完全免费的，但是您应该订阅 API 控制台网站以获得您的 API 密钥，并在您向该服务发送请求时使用它。如果您需要有关如何获取密钥的信息，您可以阅读[入门指南](https://developers.google.com/maps/documentation/javascript/tutorial "Google Maps API getting start guide")。API 密匙允许你监控你的应用程序的使用，并在必要时给谷歌与你联系的机会。请记住，谷歌地图 API 有[使用限制](https://developers.google.com/maps/documentation/javascript/usage#usage_limits "Google Maps API usage limits")，你可以付费增加。本文还假设对地理定位有一个基本的了解，你可以通过阅读使用 HTML5 地理定位 API 的[来获得。](https://www.sitepoint.com/using-the-html5-geolocation-api/ "Using the HTML5 Geolocation API")

## 谷歌地图应用编程接口概述

本节涵盖了 Google Maps API 的主要类和方法。还有许多其他可用的对象和方法，但是讨论 API 的每个方面超出了本文的范围。本库最重要和最常用的类是`google.maps.Map`。 [`Map`](https://developers.google.com/maps/documentation/javascript/reference#Map "google.maps.Map documentation") 类接受两个参数，一个是包含地图的 HTML 元素，另一个是 [`google.maps.MapOptions`对象](https://developers.google.com/maps/documentation/javascript/reference#MapOptions)。`MapOptions`对象有许多属性，但只需要下面三个。

*   `center`:一个`google.maps.LatLng`对象(稍后会详细介绍)用于设置初始地图中心。
*   `mapTypeId`:一个`google.maps.MapTypeId`对象，用于设置地图的类型。例如，您可以将地图视为路线图或卫星地图。
*   `zoom`:设置地图初始缩放比例的正数。

除了类属性的几个 setters 和 getters 之外，还有两个方法值得一提。第一个是`fitBounds()`，它将一个`LatLngBounds`对象作为其唯一的参数。`LatLngBounds`表示通过指定西南点和东北点构建的矩形。这会将地图视图设置为包含给定的边界。第二个方法是`panTo()`，它接受一个`LatLng`对象并改变地图的中心。

另一个重要的类是`google.maps.LatLng`，用经纬度表示地理坐标。这个类几乎用在 Maps API 的每个方法中。例如，我们将使用它在用户的位置上使地图居中。然而，它们也用于追踪路线或在地图上绘制折线。

许多地图包含指定兴趣点的标记，例如目的地。要在地图上放置标记，必须使用`google.maps.Marker`类。它使用传递给构造函数的选项(一个`google.maps.MarkerOptions`对象)创建一个标记，并将其应用于指定的地图。唯一需要的选项是`position`，它设置标记的位置。

在向您展示示例之前，我将描述的最后一个类是`Geocoder`。它可以让你从一个地址转换成一个对象，反之亦然。它有一个空的构造函数和一个方法`geocode()`，该方法将一个`GeocoderRequest`对象作为参数，还有一个回调函数，您将在其中读取结果。

## 一个基本例子

使用上面描述的几个类，您现在能够编写一个简单而漂亮的应用程序。这个例子并不难理解，但是仍然使用了所有讨论过的类和方法。该示例检索用户的位置，并使用地图上的标记显示它。`Geocoder`服务用于检索用户的完整地址，并将其显示在页面上。我们还将使用检索位置的`accuracy`参数在该位置周围画一个蓝色圆圈。

使用 Maps API 的第一步是实际包含它。这非常简单，只需在您的 web 页面中添加以下代码行。

```
<script src="http://maps.google.com/maps/api/js?key=YOUR-KEY-GOES-HERE&sensor=true"></script>
```

由于 Google Maps 在没有密钥的情况下仍然可以工作，为了避免示例中的长行，只需编写以下代码。

```
<script src="http://maps.google.com/maps/api/js?sensor=true"></script>
```

注意，这两行都包含一个`sensor`参数。如果你使用的是传感器(比如 GPS ),它必须包含在 URL 中并设置为`true`,否则设置为`false`。你可以在[加载地图应用程序接口](https://developers.google.com/maps/documentation/javascript/tutorial?hl=en#Loading_the_Maps_API "Loading the Maps API")上读到更多。

为了构建这个例子，我们将使用两个函数。第一个使用用户的位置创建地图，并在用户周围画一个圆。第二个函数查询 Google，根据用户的位置检索用户的地址。我们来看看两者的重点。

```
var userLatLng = new google.maps.LatLng(position.coords.latitude, position.coords.longitude);
```

这条简单的线使用一个`Position`对象的纬度和经度来创建一个`LatLng`对象。我们将在函数的其余部分使用这个对象。事实上，它将在`MapOptions`中用来设置我们正在建造的`Map`的中心。作为地图的容器，我将使用一个 id 为`map`的`<div>`元素。

```
var myOptions = {
zoom : 16,
center : userLatLng,
mapTypeId : google.maps.MapTypeId.ROADMAP
}
var mapObject = new google.maps.Map(document.getElementById("map"), myOptions);
```

现在是时候在地图上添加标记来表示用户的位置了。我们将使用 Google Maps 的默认图标，但是您可以使用`icon`属性或`setIcon()`方法指定您喜欢的图像。

```
new google.maps.Marker({map: mapObject, position: userLatLng });
```

此时，您可以关闭该功能，让地图用一个简单的标记显示用户位置。然而，由于在桌面环境中，准确性可能非常低，所以最好将其可视化。我们将使用`Circle`类来完成这个任务，正如你可能猜到的，它在地图上画了一个圆。它有很多属性来设置颜色、不透明度、半径等等，但是最重要的属性是它所应用的 map 对象。

```
var circle = new google.maps.Circle({
center: userLatLng,
radius: position.coords.accuracy,
map: mapObject,
fillColor: '#0000FF',
fillOpacity: 0.5,
strokeColor: '#0000FF',
strokeOpacity: 1.0
});
mapObject.fitBounds(circle.getBounds());
```

请注意，最后一条语句调用了`fitBounds()`方法，其边界取自圆。这样做是为了确保如果圆超出缩放视图，地图将相应缩小以显示整个圆。

第二个函数名为`writeAddressName()`，它接受一个`LatLng`对象并设置`location`属性来查询`Geocoder`服务。在回调函数中，我们将测试是否有错误发生。如果出现错误，将会显示出来。否则，显示格式化的地址。

```
function writeAddressName(latLng) {
var geocoder = new google.maps.Geocoder();
geocoder.geocode({
"location": latLng
},
function(results, status) {
if (status == google.maps.GeocoderStatus.OK)
document.getElementById("address").innerHTML = results[0].formatted_address;
else
document.getElementById("error").innerHTML += "Unable to retrieve your address" + "<br />";
});
}
```

## 把所有的放在一起

给定上一节中显示的代码，最终的完全有效的代码如下所示。

```
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<title>Geolocation and Google Maps API</title>
<script src="http://maps.google.com/maps/api/js?sensor=true"></script>
<script>
function writeAddressName(latLng) {
var geocoder = new google.maps.Geocoder();
geocoder.geocode({
"location": latLng
},
function(results, status) {
if (status == google.maps.GeocoderStatus.OK)
document.getElementById("address").innerHTML = results[0].formatted_address;
else
document.getElementById("error").innerHTML += "Unable to retrieve your address" + "<br />";
});
}

function geolocationSuccess(position) {
var userLatLng = new google.maps.LatLng(position.coords.latitude, position.coords.longitude);
// Write the formatted address
writeAddressName(userLatLng);

var myOptions = {
zoom : 16,
center : userLatLng,
mapTypeId : google.maps.MapTypeId.ROADMAP
};
// Draw the map
var mapObject = new google.maps.Map(document.getElementById("map"), myOptions);
// Place the marker
new google.maps.Marker({
map: mapObject,
position: userLatLng
});
// Draw a circle around the user position to have an idea of the current localization accuracy
var circle = new google.maps.Circle({
center: userLatLng,
radius: position.coords.accuracy,
map: mapObject,
fillColor: '#0000FF',
fillOpacity: 0.5,
strokeColor: '#0000FF',
strokeOpacity: 1.0
});
mapObject.fitBounds(circle.getBounds());
}

function geolocationError(positionError) {
document.getElementById("error").innerHTML += "Error: " + positionError.message + "<br />";
}

function geolocateUser() {
// If the browser supports the Geolocation API
if (navigator.geolocation)
{
var positionOptions = {
enableHighAccuracy: true,
timeout: 10 * 1000 // 10 seconds
};
navigator.geolocation.getCurrentPosition(geolocationSuccess, geolocationError, positionOptions);
}
else
document.getElementById("error").innerHTML += "Your browser doesn't support the Geolocation API";
}

window.onload = geolocateUser;
</script>
<style type="text/css">
#map {
width: 500px;
height: 500px;
}
</style>
</head>
<body>
<h1>Basic example</h1>
<div id="map"></div>
<p><b>Address</b>: <span id="address"></span></p>
<p id="error"></p>
</body>
</html>
```

## 结论

本文解释了 Google Maps API 中最重要的几个类的方法和属性。此外，我们还展示了如何将它们与地理定位 API 结合使用来构建一个完整的功能性服务，以跟踪用户的位置并在地图上找到他们。当然，您可以做更多的事情，正如您将在下一篇关于这个令人惊叹的 API 的文章中看到的那样。

## 分享这篇文章