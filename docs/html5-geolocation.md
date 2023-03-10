# HTML5 地理定位

> 原文：<https://www.sitepoint.com/html5-geolocation/>

HTML5 的地理定位 API 有助于识别用户的位置，这可用于向用户提供位置特定信息或路线导航细节。

有许多技术用于识别用户的位置。桌面浏览器通常使用基于 WIFI 或 IP 的定位技术，而移动浏览器使用蜂窝三角测量、GPS、A-GPS、基于 WIFI 的定位技术等。地理定位 API 将使用这些技术中的任何一种来识别用户的位置。

地理定位 API 通过规定在将用户的位置信息发送到任何网站之前应当寻求并获得用户许可来保护用户的隐私。因此，用户将被提示一个弹出窗口或对话框，请求用户允许共享位置信息。用户可以接受或拒绝请求。

![user permission required](img/f4d74b7377ea872bf4ceb4912f7ff3f9.png)

现在让我们先了解 API，然后在谷歌地图上标出你的位置。

在 HTML5 中使用任何 API 的第一步是检查它与浏览器的兼容性。

## 检查浏览器兼容性

全局导航器对象的地理位置属性有助于检测浏览器对地理位置 API 的支持。

```
if (navigator.geolocation) {
	// Get the user's current position
} else {
	alert('Geolocation is not supported in your browser');
}
```

## 获取用户的当前位置

使用`navigator.geolocation`对象的`getCurrentPosition`函数可以获得用户的当前位置。该函数接受三个参数——**成功**回调函数、**错误**回调函数和**位置**选项。如果位置数据提取成功，将调用成功回调函数，并将获得的`position`对象作为其输入参数。否则，将调用错误回调函数，并将`error`对象作为其输入参数。

```
if (navigator.geolocation) {

// Get the user's current position
navigator.geolocation.getCurrentPosition(showPosition, showError, optn);
} else {
	alert('Geolocation is not supported in your browser');
}
```

1.  **Success callback function**
    This callback function is invoked only when the user accepts to share the location information and the location data is successfully fetched by the browser. The location data will be available as a position object and the function will be called with the position object as its input parameter. A position object contains a `timestamp` property denoting the time at which the location data is retrieved and a `coords` object.

    `coords`对象具有以下属性

    *   **纬度，经度**–以十进制度数表示的地理坐标
    *   **精度**–经纬度坐标的精度等级，单位为米。数字越大，精确度越低
    *   **海拔**——海拔以上位置的高度，单位为米
    *   **高度精确度**–表示高度位置与以米为单位获得的实际姿态值之间的距离。数字越大，精确度越低
    *   **航向**–提供 360 度航向信息
    *   **速度**–表示相对速度，单位为米/秒

    ```
    function showPosition(position) {
    document.write('Latitude: '+position.coords.latitude+'Longitude: '+position.coords.longitude);
    }
    ```

2.  **错误回调函数**
    这是一个可选的回调函数，将“位置错误”对象作为其输入参数。该功能在以下任一情况下被调用

    ```
    function showError(error) {
    	switch(error.code) {
    		case error.PERMISSION_DENIED:
    			alert("User denied the request for Geolocation.");
    			break;
    		case error.POSITION_UNAVAILABLE:
    			alert("Location information is unavailable.");
    			break;
    		case error.TIMEOUT:
    			alert("The request to get user location timed out.");
    			break;
    		case error.UNKNOWN_ERROR:
    			alert("An unknown error occurred.");
    			break;
    	}
    }
    ```

    *   出现未知错误
    *   请求超时
    *   用户拒绝共享位置信息
    *   位置信息本身是不可用的
3.  **Position Options**

    它描述了检索用户位置时要使用的选项。

    *   **enablehighcrability:**布尔型。如果为真，用户代理将尝试提供最准确的位置。这可能导致较慢的响应时间和较高的功耗。为假，将获得不太精确的位置。默认值为 false。
    *   **超时:**正长值。它表示用户代理可以用位置数据响应的最长时间(以毫秒为单位)。默认值是无穷大。
    *   **maximum image:**正长值。它表示在尝试获取新的位置数据之前，用户代理可以使用缓存的位置数据多长时间(以毫秒为单位)。零值指示用户代理不得使用缓存的位置数据，而无穷大值指示用户代理必须使用缓存的位置数据。

    ```
    if (navigator.geolocation) {
    var optn = {
    			enableHighAccuracy : true,
    			timeout : Infinity,
    			maximumAge : 0
    		};
    					 navigator.geolocation.getCurrentPosition(showPosition, showError, optn);
    } else {
    		alert('Geolocation is not supported in your browser');
    }
    ```

## 跟踪位置变化

`watchPosition()`可用于定期获取位置数据。当设备或用户代理位置改变时，自动调用成功回调函数。该功能的参数类似于`getCurrentPosition()`功能。它返回一个`watch ID`值，可以通过将它传递给`clearWatch()`函数来取消注册成功回调函数。

```
function startWatch(){
if (navigator.geolocation) {
	var optn = {
		enableHighAccuracy : true,
		timeout : Infinity,
		maximumAge : 0
	};
	watchId = navigator.geolocation.watchPosition(showPosition, showError, optn);
} else {
	alert('Geolocation is not supported in your browser');
}
}
function stopWatch() {
.	if (watchId) {
		navigator.geolocation.clearWatch(watchId);
		watchId = null;
	}
}
```

## 在谷歌地图上显示我的位置

为了在谷歌地图上标出你的位置，我们可以使用谷歌地图 API 和地理定位 API。

1.  首先，我们要把使用 Geolocation API 得到的 position 对象的经纬度坐标转换成一个 Google maps `latLng`对象。

    ```
    var googlePos = new google.maps.LatLng(position.coords.latitude, position.coords.longitude);
    ```

2.  创建一个 Map 对象，指定地图显示选项和应该显示地图的 HTML div 容器。在下面的例子中，指定了三个地图显示选项

    ```
    var mapOptions = {
    				zoom : 12,
    				center : googlePos,
    				mapTypeId : google.maps.MapTypeId.ROADMAP
          				};
    	var mapObj = document.getElementById('mapTxt');
    	var googleMap = new google.maps.Map(mapObj, mapOptions);
    ```

    *   **缩放**–指定缩放级别
    *   **居中**–指定地图应该以用户位置为中心
    *   **map typeid**–可以是路线图、卫星或混合
3.  Create a marker at the location as given below

    ```
    var markerOpt = {
    		map : googleMap,
    		position : googlePos,
    		title : 'Hi , I am here',
    		animation : google.maps.Animation.DROP
    		};
    	var googleMarker = new google.maps.Marker(markerOpt);
    ```

    在上面的代码标记选项中指明了以下内容

    *   **地图**–标记应该出现的地图对象
    *   **位置**–标记应显示的锁定位置
    *   **标题**–当我们悬停在标记上时应该出现的标题
4.  使用反向地理编码 API 查找您所在位置的地址，并显示单击标记时获得的地址。

    ```
    var geocoder = new google.maps.Geocoder();
    	geocoder.geocode({
    		'latLng' : googlePos
    	}, function(results, status) {
    		if (status == google.maps.GeocoderStatus.OK) {
    			if (results[1]) {
    			var popOpts = {
    			content : results[1].formatted_address,
    			position : googlePos
    			};
    		var popup = new google.maps.InfoWindow(popOpts);
    								google.maps.event.addListener(googleMarker, 'click', function() {
    								popup.open(googleMap);
    						});
    			} else {
    				alert('No results found');
    		}
    		} else {
    			alert('Geocoder failed due to: ' + status);
    		}
    });
    ```

## 示例代码

```
<!DOCTYPE html>
<html>
<head>
<title>Sample Map</title>
<style>
#mapdiv {
	margin: 0;
	padding: 0;
	width: 500px;
	height: 500px;
}
</style>

<script src="https://maps.googleapis.com/maps/api/js?v=3.exp&sensor=true"></script>
<script>
	var watchId = null;
	function geoloc() {
	if (navigator.geolocation) {
		var optn = {
				enableHighAccuracy : true,
				timeout : Infinity,
				maximumAge : 0
		};
	watchId = navigator.geolocation.watchPosition(showPosition, showError, optn);
	} else {
			alert('Geolocation is not supported in your browser');
	}
	}

function showPosition(position) {
		var googlePos = new google.maps.LatLng(position.coords.latitude, position.coords.longitude);
		var mapOptions = {
			zoom : 12,
			center : googlePos,
			mapTypeId : google.maps.MapTypeId.ROADMAP
		};
		var mapObj = document.getElementById('mapdiv');
		var googleMap = new google.maps.Map(mapObj, mapOptions);
		var markerOpt = {
			map : googleMap,
			position : googlePos,
			title : 'Hi , I am here',
			animation : google.maps.Animation.DROP
		};
		var googleMarker = new google.maps.Marker(markerOpt);
		var geocoder = new google.maps.Geocoder();
		geocoder.geocode({
			'latLng' : googlePos
			}, function(results, status) {
				if (status == google.maps.GeocoderStatus.OK) {
				if (results[1]) {
					var popOpts = {
						content : results[1].formatted_address,
						position : googlePos
					};
				var popup = new google.maps.InfoWindow(popOpts);
				google.maps.event.addListener(googleMarker, 'click', function() {
				popup.open(googleMap);
			});
				} else {
					alert('No results found');
				}
				} else {
					alert('Geocoder failed due to: ' + status);
				}
			});
			}

			function stopWatch() {
				if (watchId) {
					navigator.geolocation.clearWatch(watchId);
					watchId = null;

				}
			}

		function showError(error) {
		var err = document.getElementById('mapdiv');
		switch(error.code) {
		case error.PERMISSION_DENIED:
		err.innerHTML = "User denied the request for Geolocation."
		break;
		case error.POSITION_UNAVAILABLE:
		err.innerHTML = "Location information is unavailable."
		break;
		case error.TIMEOUT:
		err.innerHTML = "The request to get user location timed out."
		break;
		case error.UNKNOWN_ERROR:
		err.innerHTML = "An unknown error occurred."
		break;
		}
		}
		</script>

	</head>
	<body onload="geoloc()">
		<p id = 'mapdiv'></p>
		<button onclick="stopWatch()">
			Stop
		</button>
	</body>
</html>
```

![Google Map](img/b9ccba1c83ce408ab8fbb4ab96c08919.png)

## 结论

开始探索 Google maps API，看看除了上面给出的示例之外，您还可以构建哪些功能。

## 分享这篇文章