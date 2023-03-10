# 使用 Google Maps API 和 jQuery 文章向地图添加标记

> 原文：<https://www.sitepoint.com/google-maps-api-jquery/>

Google Maps API 为开发人员和网站管理员提供了一种复杂的方式来为他们的网站添加自定义的交互式地图。API 的第 3 版于 2009 年 5 月发布，它代表了 API 的一次全面革新，以响应用户对上一版本几年来的反馈。

在本教程中，我们将向您展示如何向网站添加带有一组位置标记的地图，从而涵盖 API 的一些更简单的功能。每个标记都有一个相关的信息气泡，上面有该位置的名称和地址。此外，我们将通过 Ajax 加载位置数据，因此这可以作为开发更复杂的基于地图的应用程序的第一步。

例如，如果您站点的联系页面显示了地图上所有零售点的位置，您可以通过将这些参数发送到服务器并根据返回的 XML 在地图上显示标记来动态过滤它们(例如，根据哪些零售点提供了某些功能或在给定的一天营业)。

在开始之前，您至少应该对 jQuery 有一个基本的了解。要了解更多关于我们将要使用的类和方法的信息，你可以参考 [Google Maps API 参考](http://code.google.com/apis/maps/documentation/v3/reference.html)。

在本教程中，我们将创建:

*   一个名为`markers.html`的 HTML 文件，将用于显示地图和标记

*   一个名为`markers.xml`的 XML 文件，它包含在`name`、`address`、`lat`和`lng`标签中的数据

*   一个名为`markers.js`的 JavaScript 文件，我们将在其中放置代码以加载数据并创建地图

你可以[在这里](http://sitepoint-examples.s3.amazonaws.com/gmaps/markers.zip)下载完整的源代码。

## 数据格式

在我们开始编写代码之前，最好检查一下我们将用来加载位置数据的 XML 数据的格式。

我们希望在地图上放置的每个标记的坐标和信息将包含在一个 XML 文件中。这使得更改它变得容易，或者让服务器端脚本从数据库中提取信息来自动生成它。XML 的格式如下:

```
<?xml version="1.0"?><markers>
  <marker>
    <name>VODAFONE</name>
    <address>near Ghumaghumalu Restaurant, Marripalem, Visakhapatnam</address>
    <lat>17.74033553</lat>
    <lng>83.25067267</lng>
  </marker>
  <marker>
    <name>VODAFONE</name>
    <address>near Viswa Teja School, Thatichetlapalem, Visakhapatnam</address>
    <lat>17.73254774</lat>
    <lng>83.29195094</lng>
  </marker>
  ⋮
</markers>

```

根元素是`markers`，它包含一系列`marker`元素，每个元素包含一个文本地址、纬度和经度。

在加载这个 XML 并使用它在地图上放置标记之前，我们首先需要在 HTML 页面中包含 Google Maps JavaScript 和 jQuery 库。

## jQuery 和地图应用编程接口

不出所料，我们的功能将依赖的两个库是 jQuery 和 Google Maps API 库本身。就 jQuery 而言，您只需从 jQuery 主页下载最新版本，并将其包含在您的 HTML 页面中，如下所示:

```
<script type="text/javascript" src="js/jquery-1.4.1.min.js"></script>
```

对于谷歌地图代码，我们可以直接链接到谷歌服务器:

```
<script type="text/javascript" src="http://maps.google.com/maps/api/js?sensor=false"></script>
```

`sensor=false`参数指定我们不想使用传感器(比如 GPS 定位器)来确定用户的位置。

现在我们有了基本的库，我们可以开始构建我们的功能了。

## 概述剧本

让我们从地图代码的框架开始:

```
var MYMAP = {
  bounds: null,
  map: null
}
MYMAP.init = function(latLng, selector) {
  ⋮
}
MYMAP.placeMarkers = function(filename) {
  ⋮
}

```

我们将所有地图功能打包在一个名为`MYMAP`的 JavaScript 对象中，这将有助于避免与页面上其他脚本的潜在冲突。该对象包含两个变量和两个函数。`map`变量将存储一个对我们将要创建的谷歌地图对象的引用，而`bounds`变量将存储一个包含我们所有标记的边界框。当我们添加了所有的标记后，当我们想要缩放地图以使它们同时可见时，这将非常有用。

现在是方法:`init`将在页面上找到一个元素，并将其初始化为一个新的 Google map，具有给定的中心和缩放级别。与此同时，`placeMarkers`采用一个 XML 文件的名称，并将从该文件加载坐标数据，以在地图上放置一系列标记。

## 加载地图

现在我们已经有了基本的结构，让我们编写我们的`init`函数:

```
MYMAP.init = function(selector, latLng, zoom) {
  var myOptions = {
    zoom:zoom,
    center: latLng,
    mapTypeId: google.maps.MapTypeId.ROADMAP
  }
  this.map = new google.maps.Map($(selector)[0], myOptions);
  this.bounds = new google.maps.LatLngBounds();}

```

我们使用传递给方法的参数创建一个对象文字来包含一组选项。然后，我们初始化 Google Maps API 中定义的两个对象——一个`Map`和一个`LatLngBounds`——并将它们分配给我们之前为此目的设置的`MYMAP`对象的属性。

向`Map`构造函数传递一个 DOM 元素作为页面上的地图，以及一组选项。我们已经准备好了选项，但是要检索 DOM 元素，我们需要接受传入的选择器字符串，并使用 jQuery `$`函数来查找页面上的项目。因为`$`返回一个 jQuery 对象，而不是一个原始的 DOM 节点，所以我们需要使用`[0]`向下钻取:这允许我们访问“裸露的”DOM 节点。

因此，一旦这个函数运行，我们将有我们的地图显示在页面上，并有一个空的边界框，准备扩大我们添加标记。

## 添加标记

说到这里，我们来看看`placeMarkers`函数:

```
MYMAP.placeMarkers = function(filename) {
  $.get(filename, function(xml){
    $(xml).find("marker").each(function(){
      var name = $(this).find('name').text();
      var address = $(this).find('address').text();

      // create a new LatLng point for the marker
      var lat = $(this).find('lat').text();
      var lng = $(this).find('lng').text();
      var point = new google.maps.LatLng(parseFloat(lat),parseFloat(lng));

      // extend the bounds to include the new point
      MYMAP.bounds.extend(point);

      // add the marker itself
      var marker = new google.maps.Marker({
        position: point,
        map: MYMAP.map
      });

      // create the tooltip and its text
      var infoWindow = new google.maps.InfoWindow();
      var html='<b>'+name+'</b><br />'+address;

      // add a listener to open the tooltip when a user clicks on one of the markers
      google.maps.event.addListener(marker, 'click', function() {
        infoWindow.setContent(html);
        infoWindow.open(MYMAP.map, marker);
      });
    });

    // Fit the map around the markers we added:
    MYMAP.map.fitBounds(MYMAP.bounds);
  });
}

```

这个函数稍微复杂一点，但是很容易理解。首先，我们调用 jQuery 的`$.get`方法来执行一个 Ajax `GET`请求。该方法有两个参数:请求的 URL(在本例中是我们的本地 XML 文件)，以及请求结束时执行的回调函数。该函数将依次被传递对请求的响应，在本例中是我们的 XML。

jQuery 对待 XML 和 HTML 完全一样，所以我们可以使用`$(xml).find('marker’).each( … )`遍历响应 XML 中的每个标记元素，并在地图上为每个元素创建一个标记。

我们获取标记的名称和地址，然后为每个标记创建一个新的`LatLng`对象，并将其赋给一个`point`变量。我们扩展边界框以包含该点，然后在地图上的该位置创建一个标记。

我们希望每当用户点击这些标记时出现一个工具提示气泡，并且我们希望它包含我们位置的名称和地址。因此，我们需要使用 Maps API `event.addListener`方法为每个标记添加一个事件监听器。不过，在此之前，我们将创建工具提示本身。在谷歌地图 API 中，这种类型的工具提示被称为`InfoWindow`。所以我们创建了一个新的`InfoWindow`，并设置了一些 HTML 来填充必要的信息。然后我们添加我们的事件监听器。每当其中一个标记被点击时，监听器将被触发，两者都设置`InfoWindow`的内容并打开它，以便它在地图上可见。

最后，在添加了所有标记及其关联的事件处理程序和`InfoWindows`之后，我们通过使用 Maps API 的`fitBounds`方法将地图与标记相匹配。我们需要传递的只是我们一直在扩展以包含每个标记的`bounds`对象。这样，无论地图在哪里被缩放或平移，它总是会捕捉到包含所有标记的理想缩放级别。

## 将所有这些联系在一起

现在我们的代码已经准备好了，让我们把它付诸行动。我们将使用 jQuery 的`$('document').ready`等待页面加载，然后初始化地图，使用`#map`选择器字符串将地图指向带有`map`的`id`的页面元素:

```
$(document).ready(function() {
  $("#map").css({
    height: 500,
    width: 600
  });
  var myLatLng = new google.maps.LatLng(17.74033553, 83.25067267);
  MYMAP.init('#map', myLatLng, 11);
    $("#showmarkers").click(function(e){
    MYMAP.placeMarkers('markers.xml');
  });
});

```

我们还在`#showmarkers`按钮上附加了一个点击事件监听器。当点击这个按钮时，我们用 XML 文件的 URL 调用我们的`placeMarkers`函数。旋转一下，你会看到一组自定义标记出现在地图上。

## 总结

您可能已经猜到 Google Maps API 有比我们在这里介绍的更多的东西，所以一定要查看文档，感受一下所有可能的东西。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [jQuery Fundamentals](https://learnable.com/courses/jquery-fundamentals-1132) 。

## 分享这篇文章