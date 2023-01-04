# 使用 Google Maps API 获取路线

> 原文：<https://www.sitepoint.com/find-a-route-using-the-geolocation-and-the-google-maps-api/>

在 [*使用地理定位和谷歌地图 API*](https://www.sitepoint.com/working-with-geolocation-and-google-maps-api/ "Working with Geolocation and the Google Maps API") 中，您学习了如何确定用户的物理位置并将其显示在地图上。在这篇后续文章中，您将创建一个服务，让用户获得从一个地址到另一个地址的方向。为了增强用户体验，该服务还将允许用户自动使用他们的当前位置作为起点。

本文假设读者熟悉前一篇文章的内容。说到这里，让我们开始吧。

## 进一步探索 Google Maps API

在演示中，你将重新认识你的老朋友`Map`、`LatLng`和`Geocoder`。你也会交到一些新朋友。第一个是`google.maps.DirectionsService`，计算两个或多个地点之间的方向(或路线)。这个类非常简单。它的构造函数不带参数，只有一个方法`route()`，用于计算方向。这个方法接受两个参数，一个`google.maps.DirectionsRequest`对象和一个回调函数。

`google.maps.DirectionsRequest`对象用于设置路由请求必须满足的选项。这个对象唯一需要的属性是`origin`、`destination`和`travelMode`。前两个属性定义了路径的起点和终点，而`travelMode`定义了您的交通方式。可能的值是骑自行车、开车、步行和公交(使用公共交通)。需要注意的一点是`origin`和`destination`可以使用一个`LatLng`实例或者一个包含地址的字符串。

正如我所说的，请求还可以包括几个选项，如`unitSystem`，要求使用特定的单位系统返回距离。可能的值有公制(`google.maps.UnitSystem.METRIC`)和英制(`google.maps.UnitSystem.IMPERIAL`)。默认值是根据原产国选择的。您还可以使用`waypoints`属性指定一组中间点。此外，您可以约束方向。例如，如果可能的话，您可以通过将属性`avoidHighways`设置为`true`来请求一条不使用高速公路的路线。您也可以通过将`avoidTolls`属性设置为`true`来避免收费公路。

`DirectionsService`的回调函数返回两个值，一个`google.maps.DirectionsResult`对象和一个`google.maps.DirectionsStatus`类的可能值(实际上是属性)。前者只有一个属性`routes`，这是一个 [`DirectionsRoute`](https://developers.google.com/maps/documentation/javascript/reference#DirectionsRoute "DirectionsRoute specifications") 的数组，包含计算出的每条路径的信息。`DirectionsStatus`表示请求的最终状态，可以表示成功(`DirectionsStatus.OK`)、没有结果(`DirectionsStatus.ZERO_RESULTS`)或错误(如`DirectionsStatus.INVALID_REQUEST`或`DirectionsStatus.REQUEST_DENIED`)。

我们的另一个新朋友是`google.maps.DirectionsRenderer`班。它<q cite="https://developers.google.com/maps/documentation/javascript/reference#DirectionsRenderer">以从`DirectionsService` <q>中检索到的`DirectionsResult`对象的形式呈现检索到的方向。这个类只包含 getters 和 setters，所以我们不会进一步探究。唯一值得注意的是它的构造函数，它接受一个允许您设置几个选项的`google.maps.DirectionsRendererOptions`对象。后者的关键属性是`directions`和`map`，它们设置要显示的路线(使用`DirectionsService`检索)和用于显示路线的地图对象。</q></q>

## 让我们开始编码吧

既然您已经看到了本文中使用的所有新类，是时候深入研究代码了。为了使用户能够询问从起点到终点的路径，首先需要一个表单。这将非常简单，因为它只需要两个`<input>`元素和一个提交按钮。然而，为了增强用户体验，该页面还将提供用用户的当前位置自动填充一个`<input>`的机会。为了实现这个目标，我会在每个`<input>`下面放一个链接，一旦点击，就会使用地理定位和谷歌地图 API 检索用户的地址。实现该特性的表单如下所示。

```
<form id="calculate-route" name="calculate-route" action="#" method="get">
  <label for="from">From:</label>
  <input type="text" id="from" name="from" required="required" placeholder="An address" size="30" />
  <a id="from-link" href="#">Get my position</a>
  <br />

  <label for="to">To:</label>
  <input type="text" id="to" name="to" required="required" placeholder="Another address" size="30" />
  <a id="to-link" href="#">Get my position</a>
  <br />

  <input type="submit" />
  <input type="reset" />
</form>
```

现在我们将转移到演示背后的业务逻辑。与第一篇文章不同，我们将利用 jQuery 快速选择 DOM 中的元素，并以跨浏览器的方式附加处理程序。因为所有的工作都是在客户端完成的，所以我们需要做的第一件事是阻止表单的默认行为，并运行一些额外的 JavaScript，我们将在后面介绍。为了实现这个目标，我们将为表单的`submit`事件附加一个处理程序。处理程序使用 jQuery `preventDefault()`方法，然后调用`calculateRoute()`函数。实现它的代码如下所示。

```
$("#calculate-route").submit(function(event) {
  event.preventDefault();
  calculateRoute($("#from").val(), $("#to").val());
});
```

最重要的代码包含在`calculateRoute()`函数中。该函数将接受两个参数，`from`和`to`，分别代表起始地址和目的地址。第一步是创建`map`实例，如下面的代码所示。

```
// Center initialized to Naples, Italy
var myOptions = {
  zoom: 10,
  center: new google.maps.LatLng(40.84, 14.25),
  mapTypeId: google.maps.MapTypeId.ROADMAP
};
// Draw the map
var mapObject = new google.maps.Map(document.getElementById("map"), myOptions);
```

在我们进一步讨论之前，我想讨论一些考虑事项。正如你所看到的，我使用了一个静态的位置来开始地图的中心。正如您在上一篇文章中了解到的，`center`属性是必需的。在本演示中，您可能想省略它，因为如果路径请求成功，地图会相应地重绘并居中。您不应该这样做，因为如果请求失败，您将看到一个灰色填充区域。一种替代方案可以是最初基于用户的当前位置将地图居中。这需要额外的地理位置查找，因此您可能会认为这是一种资源浪费。

接下来，您需要创建`DirectionsService`实例和`directionsRequest`对象，如下所示。这个演示只使用了`unitSystem`选项，但是你可以进一步扩展。

```
var directionsService = new google.maps.DirectionsService();
var directionsRequest = {
  origin: from,
  destination: to,
  travelMode: google.maps.DirectionsTravelMode.DRIVING,
  unitSystem: google.maps.UnitSystem.METRIC
};
```

最后一步，是使用`route()`方法运行请求。我们还必须编写回调函数，该函数使用响应来设置和显示计算出的路径。在回调函数中，我们将检查请求是否成功，在这种情况下，我们将显示路线，或者不成功，在这种情况下，我们将显示错误。这由下面的代码实现。

```
directionsService.route(
  directionsRequest,
  function(response, status)
  {
    if (status == google.maps.DirectionsStatus.OK)
    {
      new google.maps.DirectionsRenderer({
        map: mapObject,
        directions: response
      });
    }
    else
      $("#error").append("Unable to retrieve your route<br />");
  }
);
```

## 把所有的放在一起

上一节解释了演示的关键组件。现在，是时候将这些片段组合成最终的结果了，如下所示。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Find a route using Geolocation and Google Maps API</title>
    <script src="http://maps.google.com/maps/api/js?sensor=true"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
    <script>
      function calculateRoute(from, to) {
        // Center initialized to Naples, Italy
        var myOptions = {
          zoom: 10,
          center: new google.maps.LatLng(40.84, 14.25),
          mapTypeId: google.maps.MapTypeId.ROADMAP
        };
        // Draw the map
        var mapObject = new google.maps.Map(document.getElementById("map"), myOptions);

        var directionsService = new google.maps.DirectionsService();
        var directionsRequest = {
          origin: from,
          destination: to,
          travelMode: google.maps.DirectionsTravelMode.DRIVING,
          unitSystem: google.maps.UnitSystem.METRIC
        };
        directionsService.route(
          directionsRequest,
          function(response, status)
          {
            if (status == google.maps.DirectionsStatus.OK)
            {
              new google.maps.DirectionsRenderer({
                map: mapObject,
                directions: response
              });
            }
            else
              $("#error").append("Unable to retrieve your route<br />");
          }
        );
      }

      $(document).ready(function() {
        // If the browser supports the Geolocation API
        if (typeof navigator.geolocation == "undefined") {
          $("#error").text("Your browser doesn't support the Geolocation API");
          return;
        }

        $("#from-link, #to-link").click(function(event) {
          event.preventDefault();
          var addressId = this.id.substring(0, this.id.indexOf("-"));

          navigator.geolocation.getCurrentPosition(function(position) {
            var geocoder = new google.maps.Geocoder();
            geocoder.geocode({
              "location": new google.maps.LatLng(position.coords.latitude, position.coords.longitude)
            },
            function(results, status) {
              if (status == google.maps.GeocoderStatus.OK)
                $("#" + addressId).val(results[0].formatted_address);
              else
                $("#error").append("Unable to retrieve your address<br />");
            });
          },
          function(positionError){
            $("#error").append("Error: " + positionError.message + "<br />");
          },
          {
            enableHighAccuracy: true,
            timeout: 10 * 1000 // 10 seconds
          });
        });

        $("#calculate-route").submit(function(event) {
          event.preventDefault();
          calculateRoute($("#from").val(), $("#to").val());
        });
      });
    </script>
    <style type="text/css">
      #map {
        width: 500px;
        height: 400px;
        margin-top: 10px;
      }
    </style>
  </head>
  <body>
    <h1>Calculate your route</h1>
    <form id="calculate-route" name="calculate-route" action="#" method="get">
      <label for="from">From:</label>
      <input type="text" id="from" name="from" required="required" placeholder="An address" size="30" />
      <a id="from-link" href="#">Get my position</a>
      <br />

      <label for="to">To:</label>
      <input type="text" id="to" name="to" required="required" placeholder="Another address" size="30" />
      <a id="to-link" href="#">Get my position</a>
      <br />

      <input type="submit" />
      <input type="reset" />
    </form>
    <div id="map"></div>
    <p id="error"></p>
  </body>
</html>
```

## 结论

本文向您介绍了 Google Maps API 中的几个新类和属性。您使用这些类开发了一个基本服务，允许用户从一个地址到另一个地址获取方向。在下一篇文章中，您将学习如何创建一条折线来连接地图上的几个点。

## 分享这篇文章