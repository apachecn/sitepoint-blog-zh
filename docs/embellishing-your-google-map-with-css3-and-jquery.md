# 用 CSS3 和 jQuery 美化你的谷歌地图

> 原文：<https://www.sitepoint.com/embellishing-your-google-map-with-css3-and-jquery/> 修饰你的谷歌地图

交互式地图很简洁。在你的页面上嵌入一个基本的地图并不难，但是 Google 提供了一个丰富且易于使用的 API 来在你的网页上嵌入和装饰地图，所以应该没有理由使用无聊的默认地图。

在这篇文章中，我将分享我最近使用的一项技术，通过结合使用 Google Maps API、jQuery 动画和一些新的 CSS 效果，来增加素食墨尔本的“购物和吃饭的地方”地图的味道。

![vegan-melbourne](img/05f85202dccb7bc3a8b636be2181fb40.png)

创建谷歌地图覆盖图有两种选择:KML 或自定义 javascript。KML 是谷歌地球和地图使用的 XML 文件格式，可以指定地标和其他覆盖。这对于标准化和在覆盖图中有效地显示大量数据非常有用，但是它限制了您的元数据和定制选项。

对于另一个选项，Google 提供了一个 API 来实时地在地图上添加地标和其他功能。这是我们将在本文中使用的。请注意，我们使用的是 API 的第 3 版——你会在互联网上找到的许多教程都在使用一个已经过时的旧版本。

## 基础知识

首先，我们需要加载 jQuery 和 Google Maps API 库。请注意，当包含谷歌地图时，如果您正在检测用户的位置(比如通过 GPS)，您必须将`sensor`参数设置为 true。对于典型的浏览器场景，虽然 false 也可以。

```
<!DOCTYPE html>
<html>
<head>
  <script
    src="https://ajax.googleapis.com/ajax/libs/jquery/1.5.1/jquery.min.js">

  </script>

  <script
    type="text/javascript"
    src="http://maps.google.com/maps/api/js?sensor=false">
  </script>

  <script>
    // New code goes here
  </script>

</head>
<body>
  <div id='map_canvas' style='height:500px; width: 700px'></div>
</body>
</html> 
```

这还没有显示地图，它只是做好了一切准备，加载了正确的库和一个用于放置地图的`div`。本文的其余部分将假设这个基本模板。

每一个带有地图的页面都需要从实际创建一个地图对象开始。这是通过指定一些基本选项来完成的，比如初始位置和缩放级别，以及放置地图的容器。

```
$(function() { // jQuery onload handler
  var melbourne = new google.maps.LatLng(-37.813611, 144.963056);
  var mapOptions = {
    zoom:      12,
    center:    melbourne,
    mapTypeId: google.maps.MapTypeId.ROADMAP
  }

  var map = new google.maps.Map($("#map_canvas")[0], mapOptions);
}); 
```

从这里，我们可以在地图上放置不同的项目。最常见的是“地标”，你毫无疑问在无数的地图上见过，但也有其他选项如圆圈和图像。让我们在墨尔本铁路网的枢纽弗林德斯站顶上做个标记。

```
// continuing on from above
var map = new google.maps.Map($("#map_canvas")[0], mapOptions);
var marker = new google.maps.Marker({
  position: new google.maps.LatLng(-37.818078, 144.966811),
  map:      map,
  title:    'Flinders St Station'
}); 
```

有很多选择可以让你的马克笔更时髦一点。让我们给它一个更好的图标和一些免费的动画。

```
var marker = new google.maps.Marker({
  position: new google.maps.LatLng(-37.818078, 144.966811),
  map:      map,
  title:    'Flinders St Station',
  icon:     'http://google-maps-icons.googlecode.com/files/train.png'
});
marker.setAnimation(google.maps.Animation.BOUNCE); // Also try DROP 
```

那个弹跳很烦人。请在继续之前再次删除它。我相信我已经表达了我的观点:你可以定制你的地标。

## 行为

Google Maps API 公开了大量的事件，我们可以在程序中对这些事件做出反应。它们是根据你可以放在地图上的每种类型的物体记录下来的。在这个例子中，我们将坚持普通的点击事件，但是也有很多与其他鼠标事件和拖放进行有趣交互的空间。

```
google.maps.event.addListener(marker, 'click', function() {
  alert("clicked Flinders St");
}); 
```

这是我们创建有趣的交互所需要的钩子，它超越了标准的“信息窗口”弹出窗口，这是谷歌地图上使用的标准对话框。使用 jQuery 提供的操作和动画功能，把一些闪亮的东西放在一起是微不足道的。我们将使用一个半透明的覆盖图，它可以滑入地图，以提供关于所单击的地标的额外细节。这需要一些新的 HTML:

```
<div class='map'>
  <div id='map_canvas' style='height:500px; width: 700px'></div>
  <div id='placeDetails'>
    <h1>Flinders St Station</h1>

    <p>
      This is a pretty major train station.
    </p>
  </div>
</div> 
```

它还需要一些新的“信息窗口”的样式。在下面的代码片段中有几行 CSS 代码，但是不要惊慌，它应该很容易理解。重要的部分标有注释。

```
<style>
  .map {
    width: 700px;

    /* The following are required to allow absolute positioning of the
     * info window at the bottom right of the map, and for it to be hidden
     * when it is "off map"
     */
    position: relative;
    overflow: hidden;
  }
  #placeDetails {
    /* Place the div off the bottom right of the map */
    position: absolute;
    width: 300px;
    bottom: 0;
    right: -320px;
    padding-left: 10px;
    padding-right: 10px;

    /* Semi-transparent background */
    background-color: rgba(0,0,0,0.8);
    color: white;
    font-size: 80%;

    /* Rounded top left corner */
    border-top-left-radius: 15px;
    -moz-border-radius-topleft: 15px;
    -webkit-border-top-left-radius: 15px;
  }

  /* Fit the text nicely inside the box */
  h1 {
    font-family: sans-serif;
    margin-bottom: 0;
  }
  #placeDetails p {
    margin-top: 0;
  }
</style> 
```

现在我们有了一个可以使用 jQuery 连接起来的结构。第一步是让窗口简单地滑入和滑出，以响应 placemark 上的 click 事件。

```
var currentPlace = null;
var info = $('#placeDetails');

google.maps.event.addListener(marker, 'click', function() {
  if (currentPlace) {
    info.animate({right: '-320px'});
    currentPlace = null;
  } else {
    info.animate({right: '0'});
    currentPlace = marker;
  }
}); 
```

这是一个很好的开始，但是如果要扩展到多个地标，我们需要对它进行一些抽象。

## JSON 来拯救

让我们在地图上添加南十字车站，墨尔本的另一个主要铁路枢纽。为了做到这一点，我们将通过将 placemark 的定义移到一个 hash 中，将我们的*数据*从我们的*代码*中分离出来。这总是一个好主意，可以产生可理解和可维护的代码。

```
var places = [
  {
    "title": "Flinders St Station",
    "description": "This is a pretty major train station.",
    "position": [ -37.818078, 144.966811 ]
  },
  {
    "title": "Southern Cross Station",
    "description": "Did you know it used to be called Spencer St Station?",
    "position": [ -37.818358, 144.952417 ]
  }
]

var currentPlace = null;
var info = $('#placeDetails');

$(places).each(function() {
  var place = this;
  var marker = new google.maps.Marker({
    position: new google.maps.LatLng(place.position[0], place.position[1]),
    map:      map,
    title:    place.title,
    icon:     'http://google-maps-icons.googlecode.com/files/train.png'
  });

  google.maps.event.addListener(marker, 'click', function() {
    $('h1', info).text(place.title);
    $('p',  info).text(place.description);

    if (currentPlace == marker) {
      info.animate({right: '-320px'});
      currentPlace = null;
    } else {
      info.animate({right: '0'});
      currentPlace = marker;
    }
  });
}); 
```

这是一个很好的提取——现在很容易看到和添加新的地点，以及适应任何可能可用的额外元数据(例如地点的电话号码)。能够处理任意的额外元数据是这种方法的优势，而使用 KML 是不容易做到的。请注意，使用 jQuery `text`函数来更改 info 窗口的标题和描述是多么容易。

另一个好处是，地图的底层数据现在可以从其他地方提供，比如动态生成 JSON 的服务器进程。将 places 数组移到一个新文件`places.json`(没有`var places =`部分)，我们可以用 jQuery 轻松地获取它。

```
$.getJSON('places.json', function(places) {
  $(places).each(function() {
    // As above
  });
}); 
```

由于 ajax 安全策略的原因，如果您直接在文件系统之外进行测试，那么运行这一部分可能会有问题。最简单的方法是通过一个 web 服务器提供这两个文件，如果你安装了 ruby，这是一个简单的方法，否则把它放在 apache www 根目录下对你来说可能更舒服。

```
ruby -rwebrick -e'WEBrick::HTTPServer.new(:Port => 3000, :DocumentRoot => Dir.pwd).start' 
```

## 收尾

你现在已经有了制作你自己的漂亮地图所需的框架。为了加分，我们可以稍微清理一下用户体验。当你点击一个电台，然后点击另一个，它目前有点…乏味。地图上也没有指示选择了哪个标记。在切换数据之前，让我们再次滑出窗口，并切换当前所选项目的图标。

```
var icons = {
  'train':          'http://google-maps-icons.googlecode.com/files/train.png',
  'train-selected': 'https://dl.dropbox.com/u/3120508/train-selected.png'
}
google.maps.event.addListener(marker, 'click', function() {
  var hidingMarker = currentPlace;
  var slideIn = function(marker) {
    $('h1', info).text(place.title);
    $('p',  info).text(place.description);

    info.animate({right: '0'});
  }

  marker.setIcon(icons['train-selected']);

  if (currentPlace) {
    currentPlace.setIcon(icons['train']);

    info.animate(
      { right: '-320px' },
      { complete: function() {
        if (hidingMarker != marker) {
          slideIn(marker);
        } else {
          currentPlace = null;
        }
      }}
    );
  } else {
    slideIn(marker);
  }
  currentPlace = marker;
}); 
```

好多了。这看起来太棒了。

[**参见**演示页面](https://www.sitepoint.com/examples/google-maps-css3-jquery/)。查看完整代码的源代码。

## Protips

*   [谷歌地图图标](http://code.google.com/p/google-maps-icons/)是一个图标的金矿，你可以在你的地图上使用和修改。如果你使用它们，一定要捐赠！
*   如果您真的想跨域提供 JSON 数据，那么可以看看 JSONP。
*   如果您在 ruby 下安装 json gem，您可以访问`prettify_json.rb`脚本，您可以通过管道将 json 导入该脚本，以便于阅读。
*   那种单行 ruby 服务器几乎是不可杀的。我的迂回方式是 CTRL-Z 来后台它，然后`ps | grep "ruby -rwebrick" | cut -d ' ' -f 1 | xargs kill -9`。欢迎在评论中提出更好的方法！
*   谷歌地图 API 文档真的非常壮观。适应它。

## 分享这篇文章