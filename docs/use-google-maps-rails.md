# 将谷歌地图与 Rails 配合使用

> 原文：<https://www.sitepoint.com/use-google-maps-rails/>

地图是导航世界的方式。它们提供了检查地球上每一个位置的能力，为路线和土壤成分等服务开辟了道路。在本文中，我将介绍 Google Maps API(和一些替代产品)并将其与 Rails 集成。

## 教程源存储库

源代码可以从 [Github 库](https://github.com/NouranMahmoud/GoogleMapTut)获得。每个特性都是一个单独的提交，所以您可以使用`git checkout <commit_hash></commit_hash>`来跟踪教程进度。

## 先决条件

*   使用 [RailsBricks](http://www.railsbricks.net/) 的 Ruby on Rails 基础知识。
*   JavaScript 的中级知识
*   web 地图的工作原理。如果你是游戏新手，这篇文章值得一读。

我们的目标是使地图与 Rails 的集成变得简单。本教程介绍了集成 Google Maps 的具体步骤，涵盖了一些有用的 Rails 精华。最后，谷歌地图的替代品，如流行的开源库*fleet . js*和 *MapBox* ，将会得到一个简短的介绍。

## **初始化地图**

在过去，谷歌地图需要一个 API 键，然而，**这个 API 键在 V3** 上不再是强制的。如果您使用的是另一个版本，请按照下列步骤操作:

> **如果您使用谷歌地图 V3，请跳过这一部分**

1.  转到[谷歌 API 控制台](https://console.developers.google.com/project/48179675199?authuser=0&redirected=true)

2.  单击 API & auth-> API。

3.  通过点击状态按钮激活 Google Maps JavaScript API v3。

4.  点击凭证->创建新密钥->浏览器密钥

5.  确保`ACCEPT REQUESTS FROM THESE HTTP REFERERS (WEB SITES)`为空或包含您的域，如下图所示。
    ![Create a browser key and configure allowed referers](img/451fcf210c3537abfe9e6756c3064d1c.png)

> **注:**
> 下面的材料并没有涵盖地图的每一个方面。更多详细信息，可以访问[谷歌地图 JavaScript 文档](https://developers.google.com/maps/documentation/javascript/tutorial)。

### 加载地图

> 首先，用 RailsBrick 创建项目(**你可以自由地使用任何工具来构建 rails 应用程序**)。这是一个 rails 应用程序创建器，具有开箱即用的基本功能。你可以从简介视频[这里](http://www.railsbricks.net/)了解如何使用。

为了初始化我们主页中的地图，在主视图( **/app/views/home.erb** )中创建一个 id 为`map-canvas`的 div。将这个 div 包装在另一个 id 为`map-container`的 div 中，这将用于为地图添加一些样式。

**首先:**这里是 home 视图的代码:

```
<% title("Home Page") %>
<h1>Google Maps Tut</h1>
<div id="map-container">
  <div id="map-canvas"></div>
</div>
```

补充一些 CSS。打开名为 **framework *和* overrides.css.scss** 的文件，这是 RailsBricks 样板文件的一部分，用于覆盖引导样式。

**第二:**添加以下样式:

```
#map-container {
   height: 400px;
   border-radius: 16px 16px;
   border-color: #fff;
   border-style: solid;
   box-shadow: 2px 2px 10px #B1B1B1;
   margin-top: 25px;
   border-width: 7px;
 }
 #map-canvas {
   height: 384px;
   width: 100%;
 }
```

正如你在上面的 CSS 中看到的，我们将**地图容器**设置为固定高度的 **400 像素**，并添加了一些边框样式。获得初始工作地图的最后一步是在**app/assets/JavaScript/map**中创建一个名为**“地图”**的文件夹，并添加一个名为 **gmap.js** 的文件。现在地图应该是这样的:

![Initializing the map](img/e33f75d2a9027b5924190eaba4f1ccb9.png)

> **注意:**
> 如果地图缩放控制器显示不正确，这与图像和标签的引导样式有冲突。只需将以下覆盖添加到您的 CSS ( **框架*和* overrides.css.scss** )文件中:

```
/* Bootstrap Css Map Fix*/
#map-container #map-canvas img {
  max-width: none;
}
/* Bootstrap Css Map Fix*/
#map-container #map-canvas label {
  width: auto; display:inline;
}
```

## **在地图上绘图**

### *基本标记*

Google Maps API 包含一个标记对象，允许您轻松创建简单的标记。标记对象包含标记*位置*，标记*标题*，标记所在的*地图*等属性。

为了避免重复代码，创建一个名为`createMarker`的函数，带有参数`coords`、`map`和`title`:

```
var marker;
function createMarker(coords, map, title){
  marker = new google.maps.Marker({
    position: coords,
    map: map,
    title: title
  });
}
```

### 自定义标记

标记对象有一个“图标”属性，它可以接受一个*路径*或*图像*对象。我们将创建两个函数:一个用于创建*图像*，另一个用于*创建自定义标记*。要使标记可拖动，只需添加属性 **draggable** ，其值为 **true** 。此外，Google Maps API 支持标记的两种类型的动画:`DROP`和`BOUNCE`。

创建一个名为`createImage`的函数，它将返回我们的自定义标记使用的图像对象。其大小为 32×32 像素，原点为(0，0)。

```
function createImage(url){
  var image = {
    url: url,
    // This marker is 32 pixels wide by 32 pixels tall.
    size: new google.maps.Size(32, 32),
    // The origin for this image is 0,0.
    origin: new google.maps.Point(0,0),
    // The anchor for this image is the base of the flagpole at 0,32.
    anchor: new google.maps.Point(0, 32)
  };
  return image;
}
```

接下来，创建一个名为`createCustomMarker`的函数来完成创建标记对象的实际工作。它接受坐标、地图对象和标记的标题。使用函数`createImage`返回图标的正确图像。

```
function createCustomMarker(coords,map,title){
  marker = new google.maps.Marker({
    position: coords,
    map: map,
    title: title,
    icon: createImage("/assets/icon.png")
  });
}
```

### Infowindow

Infowindow 是用于显示内容(文本或图像)的工具提示。您可以在标记或指定的`longitude`和`latitude`(简称*经度*，和*纬度*)上添加信息窗口。InfoWindow 对象接受一个 **InfoWindowOptions** 对象。

```
function createInfoWindow(text){
  var infowindow = new google.maps.InfoWindow({
    content: text
  });
  return infowindow;
}
```

将下面的代码放到一个`initialize()`函数中

```
// add infowindow when clicking on the simple marker marker
var info = createInfoWindow("Congratulations!");
google.maps.event.addListener(marker, 'click', function() {
  info.open(map,marker);
});
```

这段代码将创建一个名为`info`的信息窗口，并放置文本“恭喜！”在里面。一个事件监听器处理标记上的`click`事件来打开信息窗口。

![Drawing simple and custom marker](img/ef341ecdd231a85fdf0473c051a2b0aa.png)

### *画线*

画线需要一系列坐标来连接。Google Maps API 提供了一个名为`Polyline`的对象，用于绘制具有属性`stroke_color`、`weight`和`opacity`的线条，以及添加图标、符号或动画。

### 简单线条

```
// drawing static polyline
var lineCoordinates = [
  new google.maps.LatLng(30.055487, 31.279766),
  new google.maps.LatLng(30.223356, 31.324345),
  new google.maps.LatLng(30.345656, 31.567677),
  new google.maps.LatLng(30.565678, 31.676887)
];
createPolyline(map, lineCoordinates, lineSymbol);

var linePath;
function createPolyline(map,lineCoordinates,lineSymbol){
  linePath = new google.maps.Polyline({
    path: lineCoordinates,
    geodesic: true,
    strokeColor: '#FF0000',
    strokeOpacity: 1.0,
    strokeWeight: 2
   });
 linePath.setMap(map);
}
```

我们用折线的坐标定义了一个名为`lineCoordinates`的数组。函数`createPolyline`实际上创建了折线，使用`lineCoordinates`数组设置其`path`。`geodesic`属性是`true`，告诉谷歌地图为我们处理复杂的数学问题。给它一个`#FF0000`的笔画颜色，不透明度为 1，笔画粗细为 2，使其可见。当我们准备好折线对象后，使用`setMap`函数将其添加到地图中。

### 带虚线的简单线条。

画一条虚线仅仅是创建样式并告诉线条使用它。下面，`lineSymbol`变量被添加到折线上。请注意，`lineSymbol`有一条路径可循，比例为 4。`createPolyline`功能修改为使用`lineSymbol`作为重复图标。

```
var lineSymbol = {
  path: 'M 0,-1 0,1',
  scale: 4,
  strokeOpacity: 1,
  strokeColor: '#393'
};

// modify the createPolyline function to contain the symbol
var linePath;
function createPolyline(map, lineCoordinates, lineSymbol){
  linePath = new google.maps.Polyline({
    path: lineCoordinates,
    geodesic: true,
    strokeColor: '#FF0000',
    strokeOpacity: 1.0,
    strokeWeight: 2,
     icons: [{ // this Array is for adding symbols to the line
      icon: lineSymbol,
      offset: '100%'
    }]
  });
  linePath.setMap(map);
}
```

### 动画破折号

我们甚至可以添加一个名为`animateCircle`的函数，通过改变偏移量来沿着线条移动图标，从而制作出虚线的动画。甚至不需要改变`createPolyline`功能。

```
function animateCircle() {
  var count = 0;
  window.setInterval(function() {
    count = (count + 1) % 200;

    var icons = linePath.get('icons');
    icons[0].offset = (count / 2) + '%';
    linePath.set('icons', icons);
  }, 20);
}

//modify the `createPolyline` function to be like the following
var linePath;
function createPolyline(map, lineCoordinates, lineSymbol){
  linePath = new google.maps.Polyline({
    path: lineCoordinates,
    geodesic: true,
    strokeColor: '#FF0000',
    strokeOpacity: 1.0,
    strokeWeight: 2,
     icons: [{ // this Array is for adding symbols to the line
      icon: lineSymbol,
      offset: '0',
      repeat: '20px'
    }]
  });
   linePath.setMap(map);
}
```

然后用`createPolyline`函数创建折线后调用`animateCircle()`函数。

### 用户创建的动态折线

在下面的代码中，我们将折线选项/属性添加到一个变量中，并使用它来创建折线。这与上面的代码示例没有太大的不同。向地图添加一个`click`事件监听器，为我们的线添加一个点，允许用户随意绘制。

```
// drawing dynamic polyline
var polyOptions = {
  strokeColor: '#000000',
  strokeOpacity: 1.0,
  strokeWeight: 3
};
poly = new google.maps.Polyline(polyOptions);
poly.setMap(map);
google.maps.event.addListener(map, 'click', addLatLng);

function addLatLng(event){
  var path = poly.getPath();
  // Because path is an MVCArray, we can simply append a new coordinate
  // and it will automatically appear.
  path.push(event.latLng);
}
```

![Drawing simple and animated lines](img/3d062882591dcbc7641f116f06097747.png)

### *绘制多边形*

多边形与折线相似，都是由一系列坐标绘制的。一个多边形有`stroke`和`fill`，可以自定义。我们将在一个名为`polygonCoords`的数组中手动添加多边形的*坐标*，并将其传递给一个名为`drawingPolygon`的新函数。该函数创建多边形，并将其路径设置为添加到`polygonCoords`数组中的坐标。多边形也是`draggable`和`editable`。

#### 简单多边形

```
// drawing polygon
var polygonCoords = [
  new google.maps.LatLng(30.055487, 31.279766),
  new google.maps.LatLng(30.466465, 31.118292),
  new google.maps.LatLng(30.321384, 31.75737),
  new google.maps.LatLng(30.055487, 31.279766)
];

// Construct the polygon.
drawingPolygon(polygonCoords);

function drawingPolygon(polygonCoords) {
  var polygon = new google.maps.Polygon({
    paths: polygonCoords,
    strokeColor: '#FF00FF',
    strokeOpacity: 0.8,
    strokeWeight: 2,
    fillColor: '#FF0000',
    fillOpacity: 0.35,
    draggable:true,
    editable: true
  });
  polygon.setMap(map);
}
```

![Drawing polygon](img/3f77e166023a3d157b6aa860ab96a7e2.png)

### **使用绘图库在地图上绘图**

Google Maps API 支持一个提供图形界面的图形库。这个 GUI 允许用户在地图上绘制折线、多边形、圆形、标记和三角形。

要在地图上加载图片库，只需让 maps API URL 包含`&libraries=drawing`并开始使用`DrawingManager`对象。

API 源代码链接应该如下所示:

```
https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=drawing
```

初始化`DrawingManager`对象:

```
// trying the drawing liberary
var drawingManager = new google.maps.drawing.DrawingManager({
  drawingMode: null,
  drawingControl: true,
  drawingControlOptions: {
    position: google.maps.ControlPosition.TOP_CENTER,
    drawingModes: [
      google.maps.drawing.OverlayType.MARKER,
      google.maps.drawing.OverlayType.CIRCLE,
      google.maps.drawing.OverlayType.POLYGON,
      google.maps.drawing.OverlayType.POLYLINE,
      google.maps.drawing.OverlayType.RECTANGLE
    ]
  },
  markerOptions: {
    icon: "/assets/icon.png"
  }
});
drawingManager.setMap(map);
```

`DrawingManager`构造函数允许你管理地图绘制工具(控件),通过指定哪个覆盖图将被渲染，它在地图上的位置，以及它的初始状态。将`drawingMode`设置为`null`意味着它不会默认为特定的叠加控制。通过将`null`更改为`google.maps.drawing.OverlayType.POLYLINE`，可以默认为折线覆盖。

第二个参数是`drawingControl`，它使用`true`来呈现控件，使用`false`来隐藏控件。`drawingControlOptions`指定地图上的控制位置。谷歌地图提供了各种放置控件的地方，比如`TOP_CENTER`、`TOP_RIGHT`、`BOTTOM_LEFT`等等。

参数还指定了可用的`drawingModes`，一组可用的`google.maps.drawing.OverlayType`常量，如`CIRCLE`、`POLYLINE`、`POLYGONS`、`RECTANGLE`、`MARKER`。您还可以为每个覆盖图赋予特定的属性，就像我们在前面的代码片段中所做的那样。

最后一步是在`drawingManager`上设置地图。

## ![Drawing markers, polylines, polygons, circles and triangles by Drawing library](img/22aa465b0869c075ad4eb2bb541831f5.png)

## **添加地图服务**

#### *地理编码和反向地理编码*

Google Maps API 提供了一个名为`Geocoder`的类，用于动态获取已知地址的坐标位置(地理编码)，反之亦然(反向地理编码)。

虽然该服务不再需要 API 密钥，但它将地理编码限制在每天 2500 个，并要求生成的应用程序使用谷歌地图显示数据。返回的数据要么是 JSON，要么是 XML。

```
var geocoding  = new google.maps.Geocoder();
$("#submit_button_geocoding").click(function(){
  codeAddress(geocoding);
});
$("#submit_button_reverse").click(function(){
  codeLatLng(geocoding);
});
```

#### 通过地理编码获取坐标

这里，我们通过在输入字段中输入地址来获得坐标。

```
function codeAddress(geocoding){
  var address = $("#search_box_geocoding").val();
  if(address.length > 0){
    geocoding.geocode({'address': address},function(results, status){
      if(status == google.maps.GeocoderStatus.OK){
        map.setCenter(results[0].geometry.location);
        var marker = new google.maps.Marker({
          map: map,
          position: results[0].geometry.location
        });
      } else {
        alert("Geocode was not successful for the following reason: " + status);
      }
    });
  } else {
    alert("Search field can't be blank");
  }
}
```

![Geocoding](img/ddc68c1ced9d587cfaec4ae07681e67f.png)

#### 通过反向地理编码获取地址

在这种情况下，我们将`latlng`变量传递给`geocode`对象来生成地图上的位置(地址)。

```
function codeLatLng(geocoding) {
  var input = $('#search_box_reverse').val();
  console.log(input);

  var latlngbounds = new google.maps.LatLngBounds();
  var listener;
  var regex = /([1-9])+\.([1-9])+\,([1-9])+\.([1-9])+/g;
  if(regex.test(input)) {
    var latLngStr = input.split(",",2);
    var lat = parseFloat(latLngStr[0]);
    var lng = parseFloat(latLngStr[1]);
    var latLng = new google.maps.LatLng(lat, lng);
    geocoding.geocode({'latLng': latLng}, function(results, status) {
      if (status == google.maps.GeocoderStatus.OK) {
        if(results.length > 0) {
          //map.setZoom(11);
          var marker;
          map.setCenter(results[1].geometry.location);
          var i;
          info = createInfoWindow("");
          for(i in results){
            latlngbounds.extend(results[i].geometry.location);
              marker = new google.maps.Marker({
              map: map,
              position: results[i].geometry.location
            });

            google.maps.event.addListener(marker, 'click', (function(marker,i) {
              return function() {
                info.setContent(results[i].formatted_address);
                info.open(map,marker);
              }
            })(marker,i));
          }

          map.fitBounds(latlngbounds);
          listener = google.maps.event.addListener(map, "idle", function() {
            if (map.getZoom() > 16) map.setZoom(16);
            google.maps.event.removeListener(listener);
          });
        }
      } else {
        alert("Geocoder failed due to: " + status);
      }
    });
  } else {
    alert("Wrong lat,lng format!");
  }
}
```

![Reverse Geocoding](img/9c746a03dd675f5ae1d7864f9c4a74a8.png)

### *生成方向*

谷歌地图 API 为计算两个或更多地址之间的路线提供了一个很好的方向服务。这个服务可以通过初始化`google.maps.DirectionsService`来启用，它没有参数，但是有一个名为`route()`的方法。这个方法有两个参数:一个来自`google.maps.DirectionsRequest`的对象和一个回调函数。

`DirectionRequest`的基本属性是`origin`、`destination`和定义运输方式的`travelMode`。`DirectionsStatus`包含方向请求的回复状态。

为了公开结果路由，有一个`DirectionsRenderer`，它没有参数，有一个用于定义地图的方法`setMap`和一个用于设置返回响应的方法`setDirections`。

关于方向服务的更多细节，请阅读本[教程](https://www.sitepoint.com/find-a-route-using-the-geolocation-and-the-google-maps-api/)

```
var directionsService = new google.maps.DirectionsService();
var directionsDisplay = new google.maps.DirectionsRenderer();

map = new google.maps.Map(document.getElementById("map-canvas"),mapOptions);

directionsDisplay.setMap(map);

var request = {
  origin: "Mansoura, Daqahlia, Egypt",
  destination: "Cairo, Egypt",
  travelMode: google.maps.DirectionsTravelMode.DRIVING
};
directionsService.route(request, function(response, status) {
  //Check if request is successful.
  if (status == google.maps.DirectionsStatus.OK) {
    console.log(status);
    directionsDisplay.setDirections(response); //Display the directions result
  }
});
```

![Direction between Cairo and Manousoura cities](img/6d3a6c407535a86661c760a1c9e0bc49.png)

### *地图控件*

Google Maps 在地图上提供了用于处理和操作地图 UI 的控件。这些控件可以被禁用、移动，甚至可以定制新的功能。

可用的控件有:

*   缩放控制
*   平移控制
*   标度控制
*   MapType 控件
*   街景控制
*   旋转控件
*   概览地图控件

通过在地图选项中添加`disableDefaultUI: true`可以禁用默认的 UI 控件。

要删除一个默认控件，将其作为属性添加到`MapOptions`上，比如`panControl: true`、`zoomControl: false`。

```
var mapOptions = {
  center: new google.maps.LatLng(30.055487, 31.279766),
  zoom: 8,
  mapTypeId: google.maps.MapTypeId.NORMAL,
  panControl: true,
  zoomControlOptions: {
    style: google.maps.ZoomControlStyle.SMALL,
    position: google.maps.ControlPosition.LEFT_CENTER
  },
  mapTypeControlOptions: {
    style: google.maps.MapTypeControlStyle.DROPDOWN_MENU,
    mapTypeIds: [google.maps.MapTypeId.ROADMAP, "map_style"]
  },
  scaleControl: false,
  streetViewControl: true,
  overviewMapControl: true
};
```

![Simple and Custom controls (fullscreen,zoom) ](img/0cf49f5f48b2724dd43022f30d1c5d6f.png)

### *自定义地图样式*

这个特性提供了操作标准底图显示界面的能力。

地图呈现由两个特征组成:道路、公园、山脉等地图特征。和各种地图元素的样式。

抓住:样式数组允许的字符数是有限的，如果超出了，样式就不能应用到地图上。

要创建自定义地图样式，请执行以下操作:

**首先:**用两个基本属性 **MapFeatures** 和 **stylers** 创建样式数组:

```
var mapstyle = [
  {
    "featureType": "administrative.locality",
    "elementType": "labels.icon",
    "stylers": [
      { "invert_lightness": true },
      { "color": "#e40952" },
      { "visibility": "on" }
    ]
  },{
    "featureType": "water",
    "elementType": "geometry.fill",
    "stylers": [
      { "visibility": "on" },
      { "hue": "#5eff00" },
      { "color": "#282744" },
      { "weight": 0.1 },
      { "saturation": -56 },
      { "lightness": 22 },
      { "gamma": 3.91 }
    ]
  }
]
```

**第二:**在地图选项中设置`mapTypeId`:

```
var mapOptions = {
  center: new google.maps.LatLng(30.055487, 31.279766),
  zoom: 8,
  mapTypeId: google.maps.MapTypeId.NORMAL,
  mapTypeControlOptions: {
    style: google.maps.MapTypeControlStyle.DROPDOWN_MENU,
    mapTypeIds: [google.maps.MapTypeId.ROADMAP, "map_style"]
  }
};
```

**第三:**实例化`StyledMapType`，提供预定义的 mapstyle 数组和地图名称。该名称将作为样式选项出现在控件中。

```
var styledMap = new google.maps.StyledMapType(mapstyle, {name: "styled map"});
map = new google.maps.Map(document.getElementById("map-canvas"), mapOptions);
```

**第四:**将我们定制的地图样式添加到地图的`mapTypes`:

```
map.mapTypes.set("map_style", styledMap);
```

**第五:**将`mapType` ID 设置为我们的风格化地图:

```
map.setMapTypeId("map_style");
```

## ![Giving Custom style to the map](img/76beab07ab9d2b9980411194bfd2a2c1.png)

## **有用的 Rails 宝石**

### 地理编码器

地理编码器 gem 为 Ruby 提供地理编码、反向地理编码、查找附近位置、确定距离和地图服务。

> **注意:**
> 地理编码器支持使用`rails3`和`rails4`，还有一个`rails2`的分支。

安装 Geocoder 就像安装任何 Ruby gem 一样，使用`gem install geocoder`或者在`Gemfile`中添加`gem "geocoder"`并运行`bundle install`。

在通过街道地址或邮政编码获取纬度和经度值之后，您必须向任何用于存储纬度和经度值的模型添加两个浮点字段(lat，lng ),如下所示:

```
rails generate migration AddLatitudeAndLongitudeToModel lat:float lng:float
rake db:migrate
```

此外，将以下内容添加到模型中，以指定将使用的服务(地理编码器或反向地理编码器):

```
geocoded_by :address
# auto-fetch coordinates and the condition is for preventing fetching the same address more than once
after_validation :geocode, if: :address_changed?
```

这个`full_street_address`需要在模型上实现，以创建可读的地址。

> **注意:**
> Geocoder 支持一些流行的数据库，如(MySQL，PostgreSQL，MongoDB)。

Geocoder 还为您提供了一种在各种地理编码提供商之间进行切换的简单方法。

### GMaps4rails

GMaps4rails 是一个了不起的宝石，提供地理编码和地图位置。它使用 **JS** 在渲染标记时生成过滤器。它还有一个地理编码功能，可以计算简单的`lat`、`lng`值。

您可以将这个 gem 与 Geocoder gem 特性结合起来，使用`GMaps4rails`在地图上呈现结果。

**安装**

**首先:**将下面一行添加到您的 Gemfile 文件中:

```
gem 'gmaps4rails
```

然后运行`bundle install`

**第二:**添加一个 div 来保存地图:

```
<div style='width: 800px;'>
  <div id="map" style='width: 800px; height: 400px;'></div>
</div>
```

**第三:**将谷歌脚本添加到您的`application.html.erb`布局中:

```
<script src="//maps.google.com/maps/api/js?v=3.13&sensor=false&libraries=geometry" type="text/javascript"></script>
<script src='//google-maps-utility-library-v3.googlecode.com/svn/tags/markerclustererplus/2.0.14/src/markerclusterer_packed.js' type='text/javascript'></script>
```

**第四:**也需要`underscore.js`库，因为 Gmaps4rails 使用它。在你的轨道上`application.js`:

```
//= require underscore
//= require gmaps/google
```

现在，按如下方式创建地图:

```
handler = Gmaps.build('Google');
handler.buildMap(
  {
    provider: {
      disableDefaultUI: true
      // here you can pass other Google Maps API options here
    },
    internal: {
      id: 'map'
    }
  },
  function() {
    markers = handler.addMarkers([
      {
        "lat": 0,
        "lng": 0,
        "picture": {
          "url": "https://addons.cdn.mozilla.net/img/uploads/addon_icons/13/13028-64.png",
          "width":  36,
          "height": 36
        },
        "infowindow": "hello!"
      }
    ]);
    handler.bounds.extendWith(markers);
    handler.fitMapToBounds();
  }
);
```

关于这颗宝石的更多细节，请查看此[链接](https://github.com/apneadiving/Google-Maps-for-Rails)。

## **谷歌地图的替代品**

### 传单. js

Leaflet 是一个用于嵌入地图的现代 JavaScript 库，它因实现标记、覆盖和操作各种地图组件的简单性和易用性而广受欢迎。传单可以用大量可用的[插件](http://leafletjs.com/plugins.html)来扩展。它使用一个许可的 BSD 开源许可证，所以它可以被添加到任何站点而没有法律问题。此外，它还支持多个地图提供程序，包括 OpenStreetMap、MapQuestOpen、Stamen、Esri 和 OpenWeatherMap。

**安装**

从其官方网站[leaflet.com](http://leafletjs.com/download.html)下载。它在 github 上以`.zip`文件或者[分支的形式提供。](https://github.com/Leaflet/Leaflet)

说明传单简单性的一小段代码:

```
// create a map in the "map" div, set the view to a given place and zoom
var map = L.map('map').setView([51.505, -0.09], 13);

// add an OpenStreetMap tile layer
L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
}).addTo(map);

// add a marker in the given location, attach some popup content to it and open the popup
L.marker([51.5, -0.09]).addTo(map)
    .bindPopup('A pretty CSS3 popup. <br> Easily customizable.')
    .openPopup();
```

查看[活页快速入门](http://leafletjs.com/examples/quick-start.html)了解更多信息。

### 文件夹盒

MapBox 是一个漂亮的工具，它为创建地图提供了灵活性。它有能力用一个可下载的应用程序 **TileMill** (地图设计工作室)来设计带有奇妙图层和许多自定义功能的自定义地图。或者，您可以使用其 JavaScript API 创建带有自定义标记和覆盖的 MapBox web 应用程序。

**安装**

你所需要的只是在[地图框](https://www.mapbox.com)上的一个账户。一旦您在 MapBox 上创建了一些地图，这些地图 id 将用于将其与您的 web 应用程序集成。

这个例子将向您简要介绍如何在 JavaScript 中使用 MapBox。

在 [MapBox](https://www.mapbox.com) 上创建地图后，将 Mapbox.js 库包含在您的项目中。

**首先:**初始化地图:

```
var map = mapbox.map('map');
```

**第二:**设置地图的缩放范围和中心缩放点:

```
map.setZoomRange(5, 15);

map.centerzoom({
    lat: 37.871385,
    lon: -99.228516
}, 5);
```

**第三:**添加你在[地图框](https://www.mapbox.com)上创建的自定义图层。

```
map.addLayer(mapbox.layer().id('YOUR-MAP-ID-HERE'));
```

之后，你可以在地图中嵌入更多的功能，比如标记，UI 功能(全屏，缩放)等等。

> **注意:**
> 这不是一个关于如何使用 MapBox 的深入教程，但它将它作为谷歌地图的替代选项。

## 摘要

本教程给出了如何创建地图和一般地图概念的基本理解。希望您现在可以使用您选择的任何地图 API 来创建满足您需求的地图。

## 分享这篇文章