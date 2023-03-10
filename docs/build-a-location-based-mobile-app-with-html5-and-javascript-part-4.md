# 用 HTML5 和 Javascript: 4 构建一个基于位置的移动应用

> 原文：<https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-4/>

在我们系列的前一部分[中，您已经看到了如何配置“我在哪里停车”来调整主题，修改页面的过渡，以及在显示页面加载器小部件时使用显示文本的选项。感谢网络存储 API，我们现在能够存储汽车的位置以备将来使用，并随时查看位置的历史。本文将解释我如何使用 Google Maps API 在地图上显示存储的位置，并绘制从用户当前位置到汽车的路线。我们还将深入研究将应用程序的所有组件放在一起的实用函数。
](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-3/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 3")

### 谷歌地图应用编程接口

详细解释 Google Maps API 超出了本文的范围，但是如果你想了解这个 API，你可以阅读我在[JSPro.com](https://www.sitepoint.com)上发表的关于[使用地理定位和 Google Maps API](https://www.sitepoint.com/working-with-geolocation-and-google-maps-api/) 的系列文章。

我们正在构建的应用程序向 Maps API 发出三个不同的请求:显示汽车的位置，找到从用户当前位置到汽车的路线，并检索汽车位置的地址。正如您在系列的[前一部分中看到的`Position`类，我将为它的所有相关方法创建一个`Map`类。这个类，你可以在文件`maps.js`中找到，并不真正需要属性。所有的方法都是静态的，所以`Map`类的起点简单如下:](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-3/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 3")

```
function Map()
{
}
```

#### 显示地图

显示地图的方法称为`displayMap()`，将接受两个参数:用户的位置(`userPosition`)和汽车的位置(`carPosition`)。如果只提供第一个参数(当用户已经调用该方法来设置他的初始停放位置时)，图标将相应地显示。您将看到的 Google Maps API 的第一个类是`google.maps.LatLng`，它用纬度和经度表示地理坐标。我将使用这个类将使用地理位置 API 检索到的坐标转换成 Maps API 可以理解和使用的东西。`displayMap()`将使用`google.maps.Map`类显示地图，然后使用`google.maps.Marker`类设置一个标记来指定用户的位置。在设置了正确的图标之后，如前所述，该函数用`google.maps.Circle`类在位置周围画了一个圆，以显示地理定位的准确性。如果两个参数都给定了，那么两个位置都会显示出来，然后调用函数来计算两个位置之间的路线。

下面列出了这个函数的完整源代码。

```
/**
 * Display the map showing the user position or the latter and the car position
 */
Map.displayMap = function(userPosition, carPosition)
{
   var userLatLng = null;
   var carLatLng = null;

   if (userPosition != null)
      userLatLng = new google.maps.LatLng(userPosition.coords.latitude, userPosition.coords.longitude);
   if (carPosition != null)
      carLatLng = new google.maps.LatLng(carPosition.position.latitude, carPosition.position.longitude);

   var options = {
      zoom: 20,
      disableDefaultUI: true,
      streetViewControl: true,
      center: userLatLng,
      mapTypeId: google.maps.MapTypeId.ROADMAP
   }

   var map = new google.maps.Map(document.getElementById('map'), options);
   var marker = new google.maps.Marker({
      position: userLatLng,
      map: map,
      title: 'Your position'
   });
   // If carLatLng is null means that the function has been called when the
   // user set his current position and that is when he parked the car so the
   // icon will be shown accordingly.
   if (carLatLng == null)
      marker.setIcon('images/car-marker.png');
   else
      marker.setIcon('images/user-marker.png');
   var circle = new google.maps.Circle({
      center: userLatLng,
      radius: userPosition.coords.accuracy,
      map: map,
      fillColor: '#70E7FF',
      fillOpacity: 0.2,
      strokeColor: '#0000FF',
      strokeOpacity: 1.0
   });
   map.fitBounds(circle.getBounds());

   if (carLatLng != null)
   {
      marker = new google.maps.Marker({
         position: carLatLng,
         map: map,
         icon: 'images/car-marker.png',
         title: 'Car position'
      });
      circle = new google.maps.Circle({
         center: carLatLng,
         radius: carPosition.position.accuracy,
         map: map,
         fillColor: '#70E7FF',
         fillOpacity: 0.2,
         strokeColor: '#0000FF',
         strokeOpacity: 1.0
      });

      // Display route to the car
      options = {
         suppressMarkers: true,
         map: map,
         preserveViewport: true
      }
      this.setRoute(new google.maps.DirectionsRenderer(options), userLatLng, carLatLng);
   }

   $.mobile.loading('hide');
}
```

#### 检索路线

一旦用户设定了他的位置，他就可以走到任何他想去的地方。之后，他需要回到他的车上，这就是下一个函数将被使用的地方。为了找到从用户当前位置到汽车的路线，我将创建一个名为`setRoute()`的函数。它将使用`google.maps.DirectionsService`和`google.maps.DirectionsRequest`类来请求谷歌计算路线。使用`google.maps.DirectionsTravelMode`和`google.maps.UnitSystem`，我将请求 Google 检索一条*步行*路径(我们知道用户将步行)，这样用户也可以使用单行道，并以米为单位查看步行距离。总之，该函数将使用`google.maps.DirectionsStatus`类来测试是否找到了至少一个结果，并且它将使用`google.maps.DirectionsRenderer`类来实际显示路线。

下面列出了完整的方法代码。

```
/**
 * Calculate the route from the user to his car
 */
Map.setRoute = function(directionsDisplay, userLatLng, carLatLng)
{
   var directionsService = new google.maps.DirectionsService();
   var request = {
      origin: userLatLng,
      destination: carLatLng,
      travelMode: google.maps.DirectionsTravelMode.WALKING,
      unitSystem: google.maps.UnitSystem.METRIC
   };

   directionsService.route(
      request,
      function(response, status)
      {
         if (status == google.maps.DirectionsStatus.OK)
            directionsDisplay.setDirections(response);
         else
         {
            navigator.notification.alert(
               'Unable to retrieve a route to your car. However, you can still find it by your own.',
               function(){},
               'Warning'
            );
         }
      }
   );
}
```

#### 检索地址

我将描述的最后一个方法是`requestLocation()`，它将用于检索汽车位置的地址，并更新包含最后位置的持久存储对象，这样当用户看到位置的历史时，他们将看到有意义的文本，而不是 GPS 坐标。为了实现这个目标，我将使用`google.maps.Geocoder`类，让您从`LatLng`对象转换到地址，反之亦然。后者只有一个方法，叫做`geocode()`，它接受一个`GeocoderRequest`对象作为参数和一个用于读取结果的回调函数。

图示函数的代码如下。

```
/**
 * Request the address of the retrieved location
 */
Map.requestLocation = function(position)
{
   new google.maps.Geocoder().geocode(
      {
         'location': new google.maps.LatLng(position.coords.latitude, position.coords.longitude)
      },
      function(results, status)
      {
         if (status == google.maps.GeocoderStatus.OK)
         {
            var positions = new Position();
            positions.updatePosition(0, positions.getPositions()[0].coords, results[0].formatted_address);
         }
      }
   );
}
```

### 效用函数

本节描述了名为`functions.js`的文件中的几个函数，这是支持我们的移动应用程序的最后一个 JavaScript 文件。

#### 需求测试

“我把车停在哪里”对正常工作没有太多要求——只需要一个正常工作的互联网连接，就可以使用谷歌地图 API 来执行前面几节中解释的功能。如果连接不可用，我们将使用本系列第三部分中的[所示的](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-3/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 3") [Cordova 通知 API](http://docs.phonegap.com/en/2.2.0/cordova_notification_notification.md.html#Notification) ，向用户显示一条警告消息，并停止该方法的执行。这个测试是使用 [Cordova 连接 API](http://docs.phonegap.com/en/2.2.0/cordova_connection_connection.md.html#Connection) 完成的，这个对象可以访问设备的蜂窝和 wifi 连接信息。这个 API 只有一个名为`type`的属性，用于检查正在使用的活动网络连接，并可以采用以下(常量)值:

*   联系。未知的
*   联系。以太网
*   联系。无线局域网（wireless fidelity 的缩写）
*   联系。单元格 _2G
*   联系。CELL_3G
*   联系。CELL_4G
*   联系。没有人

正如我们在讨论 Web 存储 API 时所看到的，`type`属性有一些古怪之处，在[连接 API 官方文档](http://docs.phonegap.com/en/2.2.0/cordova_connection_connection.md.html#connection.type)中有详细描述。

既然您已经学习了如何使用 API，我可以向您展示检查需求功能(检查互联网连接)是什么样子的。

```
function checkRequirements()
{
   if (navigator.network.connection.type == Connection.NONE)
   {
      navigator.notification.alert(
         'To use this app you must enable your internet connection',
         function(){},
         'Warning'
      );
      return false;
   }

   return true;
}
```

#### 根据屏幕大小更新图标

过去几年最热门的话题之一无疑是响应式网页设计。<q cite="http://en.wikipedia.org/wiki/Responsive_web_design">引用维基百科，响应式网页设计是一种网页设计方法，其中网站被精心制作以提供最佳的浏览体验——容易阅读和导航，最小化尺寸调整、平移和滚动——跨越多种设备。</q>我不会深入 RWD 的细节，但是在使用 jQuery Mobile 创建移动应用程序界面后，我对这个概念很熟悉。我试图使用一个简单的方法来改进应用程序的默认界面，该方法检查设备的屏幕大小，并决定页眉和页脚中的按钮是否应该显示按钮的文本。这是使用属性`data-iconpos="notext"`完成的。在第一种情况下，文本是隐藏的，而在第二种情况下，它是显示的。默认情况下，我将值`notext`赋予页眉或页脚的所有按钮。作为屏幕断点，我使用了值 480px，因此屏幕宽度小于 480 像素的设备不会显示按钮的文本。

下面列出了所示函数的代码。

```
function updateIcons()
{
   if ($(window).width() > 480)
   {
      $('a[data-icon], button[data-icon]').each(
         function()
         {
            $(this).removeAttr('data-iconpos');
         }
      );
   }
   else
   {
      $('a[data-icon], button[data-icon]').each(
         function()
         {
            $(this).attr('data-iconpos', 'notext');
         }
      );
   }
}
```

### 结论

在本系列的第四部分中，您已经学会了使用 Google Maps API 来显示地图并检索用户位置和他的汽车之间的路线。我们使用了`google.maps.Geocoder`类来更新最后存储的对象。在本文的第二部分，我们学习了一些您将在文件`function.js`中看到的函数。在下一篇文章中，我将解释并展示该文件的其余方法。

## 分享这篇文章