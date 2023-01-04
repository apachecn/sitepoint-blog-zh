# 使用地理定位和谷歌地图创建折线

> 原文：<https://www.sitepoint.com/create-a-polyline-using-the-geolocation-and-the-google-maps-api/>

在[使用谷歌地图 API](https://www.sitepoint.com/find-a-route-using-the-geolocation-and-the-google-maps-api/ "Getting Directions Using the Google Maps API") 获取路线中，我解释了如何创建一个服务，让用户找到从一个地址到另一个地址的路线。在本文中，您将了解如何使用地理定位 API 和 Google Maps API 在地图上显示连接几个点的折线。


本文假设你熟悉[中的材料使用地理定位和谷歌地图 API](https://www.sitepoint.com/working-with-geolocation-and-google-maps-api/ "Working with Geolocation and the Google Maps API") 和[使用谷歌地图 API](https://www.sitepoint.com/find-a-route-using-the-geolocation-and-the-google-maps-api/ "Getting Directions Using the Google Maps API") 获取方向。如果你还没有读过，我建议你现在就去读。

## 使用谷歌地图 API 做更多事情

在前两篇文章中，您已经看到了许多类、方法和属性，然而，它们只是 Google Maps API 难题的一小部分。在本节中，您将看到我们将在最终演示中使用的另一部分 API。

我要介绍的第一个类是`google.maps.Polyline`。它使用传递给构造函数的选项(一个`google.maps.PolylineOptions`对象)绘制一条连接几个点的线。这个类的方法只是 getters 和 setter，所以我不会说得太详细，但是请记住，最重要的 setter 是定义连接点的`setPath()`和设置绘制线条的地图的`setMap()`。

`google.maps.PolylineOptions`类充满了属性，您可以使用这些属性来调整折线以满足您的需求。最重要的两个是之前描述的 setters 的底层——`map`和`path`。笔画属性也值得注意，因为它们将在演示中使用。顾名思义，`strokeColor`设置笔画颜色，默认为`#000000`(黑色)。`strokeOpacity`是一个介于 0.0 和 1.0 之间的数字，用于设置笔触不透明度。`strokeWeight`是以像素为单位设置笔画宽度的数字。我建议阅读 [PolylineOptions 官方文档](https://developers.google.com/maps/documentation/javascript/reference#PolylineOptions)来了解其他有用的属性。

该演示还利用了`google.maps.LatLngBounds`类。[引用官方文献](https://developers.google.com/maps/documentation/javascript/reference#LatLngBounds)， <q cite="https://developers.google.com/maps/documentation/javascript/reference#LatLngBounds">a `LatLngBounds`表示地理坐标中的一个矩形，包括与 180 度经线</q>相交的矩形。它的构造函数最多接受两个参数，如果给定的话，这些参数必须是`LatLng`实例。第一个用作矩形的西南点，第二个用作东北点。你在演示中看到的唯一方法是`extend()`，它接受一个`LatLng`点，并扩大当前矩形的边界以包括它。`LatLngBounds`类的另一个方法是`contains()`，它测试`LatLng`坐标是否在边界内。这个类也有其他有用的方法来处理多个矩形。事实上，您可以合并(`union()`)或相交(`intersects()`)矩形，但请记住，一次只能对两个`LatLngBounds`实例运行该操作。

## 构建演示

为了查看所展示的类的运行情况，让我们构建一个演示程序，保存用户的动作，并在地图上绘制一条连接它们的折线。因为我们必须跟踪用户的移动，所以演示使用了地理定位方法`watchPosition()`而不是`getCurrentPosition()`。这些位置将被存储在一个名为`path`的变量中，该变量被初始化为一个空数组。

```
// Save the positions' history
var path = [];
```

当`watchPosition()`方法运行成功回调函数时，用户的纬度和经度被用来构建一个`google.maps.LatLng`对象。该对象将被[插入到`path`数组](https://singlethread.io/post/find-the-intersection-of-two-arrays/)中。并且，对于每一个新添加的点，地图将被刷新以显示用户的移动。

```
// Save the current position
path.push(new google.maps.LatLng(position.coords.latitude, position.coords.longitude));
```

我们还需要调整地图视图，使其包含折线的所有点。这是通过一个存储在名为`latLngBounds`的变量中的`LatLngBounds`对象完成的。我们需要遍历所有保存的点，一次一个地将它们传递给`extend()`方法。请注意，目前我们只准备了使用`fitBounds()`方法的数据，所以目前地图不符合边界。此外，我们还将使用`Marker`对象标记每个点，以便您可以轻松定位每个位置。下面列出了实现这一点的代码。

```
// Create the LatLngBounds object that will be used to fit the view to the points range and
// place the markers to the polyline's points
var latLngBounds = new google.maps.LatLngBounds();
for(var i = 0; i < path.length; i++) {
  latLngBounds.extend(path[i]);
  // Place the marker
  new google.maps.Marker({
    map: map,
    position: path[i],
    title: "Point " + (i + 1)
  });
}
```

一旦有了要显示的点，我们需要使用前面讨论的`Polyline`和`PolylineOptions`类来构建折线。这非常容易，因为你只需要创建一个带有所需选项的新的`Polyline`对象。在下面的代码中，线条的笔触被更改为一个像素宽的蓝色线条，透明度为 70%。

```
// Creates the polyline object
var polyline = new google.maps.Polyline({
  map: map,
  path: path,
  strokeColor: '#0000FF',
  strokeOpacity: 0.7,
  strokeWeight: 1
});
```

唯一剩下的步骤是确保地图视图包含折线的所有点。这是通过将`latLngBounds`变量传递给 map 的`fitBounds()`方法来实现的，如下所示。

```
// Fit the bounds of the generated points
map.fitBounds(latLngBounds);
```

### 添加预设点

使用上面的代码，我们有一个完全正常工作的演示。但是，如果您在桌面环境中测试代码，`watchPosition()`方法将只运行一次，因此您不会看到任何绘制的线条。为了避免这个问题，您可以复制下面这段代码，并将其粘贴到循环遍历`path`数组并构建`LatLngBounds`对象的代码块之前。这段代码将使用用户的当前位置创建一小组随机生成的点，并将其插入到`path`数组中。

```
// Create the polyline's points
for(var i = 0; i < 5; i++) {
  // Create a random point using the user current position and a random generated number.
  // The number will be once positive and once negative using based on the parity of i
  // and to reduce the range the number is divided by 10
  path.push(
    new google.maps.LatLng(
      position.coords.latitude + (Math.random() / 10 * ((i % 2) ? 1 : -1)),
      position.coords.longitude + (Math.random() / 10 * ((i % 2) ? 1 : -1))
    )
  );
}
```

## 演示页面

使用前面几节中显示的代码，最终的工作演示页面如下所示。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Create a polyline using Geolocation and Google Maps API</title>
    <script src="http://maps.google.com/maps/api/js?sensor=true"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
    <script>
      $(document).ready(function() {
        // If the browser supports the Geolocation API
        if (typeof navigator.geolocation == "undefined") {
          $("#error").text("Your browser doesn't support the Geolocation API");
          return;
        }
        // Save the positions' history
        var path = [];

        navigator.geolocation.watchPosition(function(position) {
          // Save the current position
          path.push(new google.maps.LatLng(position.coords.latitude, position.coords.longitude));

          // Create the map
          var myOptions = {
            zoom : 16,
            center : path[0],
            mapTypeId : google.maps.MapTypeId.ROADMAP
          }
          var map = new google.maps.Map(document.getElementById("map"), myOptions);

          /*
          Uncomment this block if you want to set a path

          // Create the polyline's points
          for(var i = 0; i < 5; i++) {
            // Create a random point using the user current position and a random generated number.
            // The number will be once positive and once negative using based on the parity of i
            // and to reduce the range the number is divided by 10
            path.push(
              new google.maps.LatLng(
                position.coords.latitude + (Math.random() / 10 * ((i % 2) ? 1 : -1)),
                position.coords.longitude + (Math.random() / 10 * ((i % 2) ? 1 : -1))
              )
            );
          }
          */

          // Create the array that will be used to fit the view to the points range and
          // place the markers to the polyline's points
          var latLngBounds = new google.maps.LatLngBounds();
          for(var i = 0; i < path.length; i++) {
            latLngBounds.extend(path[i]);
            // Place the marker
            new google.maps.Marker({
              map: map,
              position: path[i],
              title: "Point " + (i + 1)
            });
          }
          // Creates the polyline object
          var polyline = new google.maps.Polyline({
            map: map,
            path: path,
            strokeColor: '#0000FF',
            strokeOpacity: 0.7,
            strokeWeight: 1
          });
          // Fit the bounds of the generated points
          map.fitBounds(latLngBounds);
        },
        function(positionError){
          $("#error").append("Error: " + positionError.message + "<br />");
        },
        {
          enableHighAccuracy: true,
          timeout: 10 * 1000 // 10 seconds
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
    <h1>Create a polyline</h1>
    <div id="map"></div>
    <p id="error"></p>
  </body>
</html>
```

## 结论

本文解释了用于绘制连接地图上几个点的折线的类、属性和方法。正如您在这一系列文章中所看到的，这些 API 可用于构建许多增强用户体验的优秀服务。当然，您可以做得比这里展示的更多。通过探索 Google Maps API 中的其他类，可能性实际上是无限的。

## 分享这篇文章