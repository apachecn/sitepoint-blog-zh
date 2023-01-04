# 浏览器中的地理定位

> 原文：<https://www.sitepoint.com/geolocation-in-the-browser/>

毫无疑问，地理定位是智能手机最有用的功能之一，很多注意力都放在了利用它的本地应用上。不太为人所知的是，现在构建具有地理定位功能的网络应用和网站是可行的。W3C 地理定位 API 规范提出了一个通过 Javascript 在浏览器中访问定位服务的标准。这个规范是最近才出现的，但是已经得到了现代浏览器的广泛支持。现在是考虑位置感知移动网站如何让你的用户受益的最佳时机。

#### 当前浏览器对 W3C 地理定位 API 的支持

*   iPhone 3.0 +版本
*   安卓 2.0 以上
*   火狐 3.5
*   Safari 5.0
*   铬 5.0
*   Internet Explorer 9.0 以上

### 浏览器如何找到你

地理定位中采用了多种方法。你的浏览器可以简单地查找你的 IP 地址，或者向谷歌询问附近 wifi 热点的位置。不能访问 GPS 的桌面浏览器将求助于这些方法中的一种，并且它们不是特别准确。Chrome 与我当前的位置相差约 15 公里，但它足以告诉我我在澳大利亚的墨尔本。

你的手机可以获得更好的信息。它可以访问与之对话的手机信号塔的位置，并对你的实际位置进行合理的定位。最后但同样重要的是，手机将激活其 GPS 硬件，可以将你锁定在几米之内。GPS 芯片从手机电池中汲取大量能量，所以要考虑周全:如果这种精度水平不是必要的，就不要使用它。

与本地应用一样，浏览器使用你的位置的许可是*总是*选择加入。用户将收到访问其位置的请求，并可以通过浏览器设置随时重置权限。挑战在于确保用户相信你会负责任地使用他们的位置，并且你的网站在访问或不访问位置数据的情况下提供价值。

### 代码

如果支持地理定位，将有一个全局`navigator.geolocation`对象可用:

```
if ( navigator.geolocation )
{
	navigator.geolocation.getCurrentPosition(handlePosition);
}
```

`getCurrentPosition`将从浏览器请求位置并将一个位置对象传递给指定的回调函数:

```
function handlePosition(pos)
{
	alert("Your location is: " + pos.coords.latitude + ', ' + pos.coords.longitude);
}
```

试试看:

<button>获取我当前的位置</button>

位置对象应该具有以下属性:

*   以度为单位的坐标纬度。
*   **坐标经度**度。
*   **坐标精度**近似值，单位为米。
*   **北向坐标**度(0…360°)。
*   **坐标速度**以米每秒为单位。
*   海拔高度米(*大约*)我不相信！).
*   以米为单位的坐标高度精度。
*   **时间戳**一个 DOMTimeStamp 对象。

很有可能很多都是`null`，你只能依靠`coords.latitude`和`coords.longitude`。

`getCurrentPosition`可能失败的原因也有很多:位置数据可能根本不可用，或者用户可能拒绝访问。可以指定第二个回调来处理错误:

```
if ( navigator.geolocation )
{
	navigator.geolocation.getCurrentPosition(handlePosition, handleError);
}

function handleError(error)
{
	switch (error.code)
	{
		case error.PERMISSION_DENIED:
			// User denied access to location. Perhaps redirect to alternate content?
			alert('Permission was denied');
			break;
		case error.POSITION_UNAVAILABLE:
			alert('Position is currently unavailable.');
			break;
		case error.PERMISSION_DENIED_TIMEOUT:
			alert('User took to long to grant/deny permission.');
			break;
		case error.UNKNOWN_ERROR:
			alert('An unknown error occurred.')
			break;
	}
}
```

您的 web 应用程序应该优雅地处理所有情况。如果用户拒绝分享他们的位置，考虑提供不依赖地理位置的替代内容或功能。可以通过传递给`getCurrentPosition`的选项为您的用户提供进一步的考虑:

```
options = {
	enableHighAccuracy: false,
	timeout:            30000,  // milliseconds (30 seconds)
	maximumAge:         600000 // milliseconds (10 minutes)
}
navigator.geolocation.getCurrentPosition(handlePosition, handleError, options);
```

**enableHighAccuracy** 本质上是暗示你想要 GPS 数据(而不是精度较低的方法，如手机信号塔三角测量)。如上所述，GPS 消耗手机电池，并不总是需要的-如果你只需要一个大致的区域，将此设置为`false`。

**超时**指定您愿意等待定位结果的时间。

**maximum image**告诉浏览器你愿意接受一个结果，例如，最后 10 分钟。如果手机在这段时间内进行了位置查找(可能是另一个网站/应用程序)，它将返回一个缓存的结果。还是那句话，这是善待手机续航的一种方式。

如果您需要在用户移动时更新的最新位置数据，该怎么办？可以用`watchPosition`:

```
if ( navigator.geolocation )
{
	navigator.geolocation.watchPosition(handlePosition, handleError, {
		enabledHighAccuracy: true,
		maximumAge: 60000
		});
}

function handlePosition(pos)
{
	console.log("Heading: " + pos.coords.heading);
	console.log("Speed:   " + pos.coords.speed);
}
```

在大多数情况下，不需要这种精确度。考虑到使用位置数据的隐私问题，只访问您需要的信息并负责任地使用它是很重要的。一些简单的准则:

1.  考虑你为你的用户提供的价值，作为他们位置的回报——不要仅仅为了收集数据而收集数据！
2.  善待手机电池——如果你只需要一个宽阔的地方，就使用`enableHighAccuracy: false`。
3.  如果你存储位置数据，在你的网站上(以及在你网站的条款和条件中)清楚地表明这一点。

### 谷歌地图

现在我们已经介绍了地理定位的基础知识，让我们看看它如何与谷歌地图相适应。地图支持可通过以下方式添加到您的 web 应用程序中:

```
<script type="text/javascript" src="http://maps.google.com/maps/api/js?sensor=true"></script>
```

`sensor`参数表示您是否使用传感器——即 GPS——来检测位置。对于移动应用程序，这应该设置为 true。我们可以通过一些用户代理检测来进一步定制我们的地图——这个片段将为 Android 和 iPhone 用户提供一个全屏地图:

```
function detectBrowser()
{
	var userAgent = navigator.userAgent;
	var mapDiv = document.getElementById("map");

	if (userAgent.indexOf('iPhone') != -1 || userAgent.indexOf('Android') != -1 )
	{
		mapDiv.style.width = '100%';
		mapDiv.style.height = '100%';
	}
	else
	{
		mapDiv.style.width = '600px';
		mapDiv.style.height = '800px';
	}
}
```

iPhones 也关注`标签。这将禁用缩放页面，因此缩放仅适用于地图。`

```
<meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
```

现在来设置地图本身:

```
var map = new google.maps.Map(document.getElementById("map"), {
	zoom: 10,
	mapTypeId: google.maps.MapTypeId.ROADMAP
});
```

这就是全部了。参见[谷歌地图开发者指南](http://code.google.com/apis/maps/documentation/javascript/basics.html)获取全套地图配置选项。我们现在可以更新我们的`handlePosition`函数来使用地图:

```
function handlePosition(pos)
{
	// Google maps use a different location object (LatLng) which we can convert to easily:
	latlng = new google.maps.LatLng(pos.coords.latitude, pos.coords.longitude);

	// Center the map on the current location.
	map.setCenter(latlng);

	// Drop a marker on the current location
	marker = new google.maps.Marker
	({
		map: map,
		draggable: false,
		animation: google.maps.Animation.DROP,
		position: latlng
	});
}
```

### 地图示例

向用户显示他们当前的位置并不是最有用的应用程序，但是有了地理定位 API 和 Google maps，您就拥有了一套优秀的工具来构建基于位置的 web 应用程序。挑战在于做一些有趣的事情！地理定位愉快。

一些进一步的阅读:

*   [W3C 地理定位 API 规范](https://www.w3.org/TR/geolocation-API/)
*   [谷歌地图 Javascript API](http://code.google.com/apis/maps/documentation/javascript/)
*   [深入 html 5–地理定位](http://diveintohtml5.org/geolocation.html)
*   [Modernizr](http://www.modernizr.com/docs/#geolocation)–轻松检测地理位置支持。
*   [geo . js](http://code.google.com/p/geo-location-javascript/)——地理定位框架。

## 分享这篇文章`