# 用两行 JavaScript 获得地理位置

> 原文：<https://www.sitepoint.com/geo-location-2-lines-javascript/>

[![geo-location](img/a0a959cd75426981779d99783eb8a4cd.png "geo-location")](https://www.sitepoint.com/wp-content/uploads/jquery4u/2011/09/geo-location.png)

这就是如何使用两行 JavaScript 代码获得用户地理位置的方法。第一行加载 geo location JavaScript 文件，第二行提醒用户位置(在一行的文档 ready 中)。

## 代码

```
<script language="JavaScript" src="https://www.geoplugin.net/javascript.gp" type="text/javascript"></script>
<script type="text/javascript">
jQuery(document).ready(function($) {
	alert("Your location is: " + geoplugin_countryName() + ", " + geoplugin_region() + ", " + geoplugin_city());
});
</script>
```

需要注意的事项:

*   提供的地理位置基于您的 ISP 的 ip 地址/位置。
*   你依赖于 geoplugin.com 提供的 API 服务。

## 直接在 Firebug 中运行

```
jQuery(document).ready(function($) {
    jQuery.getScript('https://www.geoplugin.net/javascript.gp', function() 
{
    var country = geoplugin_countryName();
    var zone = geoplugin_region();
    var district = geoplugin_city();
    console.log("Your location is: " + country + ", " + zone + ", " + district);
});
});
```

## 地理位置属性的完整列表

```
function geoplugin_city() { return 'Dobroyd Point';} 
function geoplugin_region() { return 'New South Wales';} 
function geoplugin_regionCode() { return '02';} 
function geoplugin_regionName() { return 'New South Wales';} 
function geoplugin_areaCode() { return '0';} 
function geoplugin_dmaCode() { return '0';} 
function geoplugin_countryCode() { return 'AU';} 
function geoplugin_countryName() { return 'Australia';} 
function geoplugin_continentCode() { return 'OC';} 
function geoplugin_latitude() { return '-33.873600';} 
function geoplugin_longitude() { return '151.144699';} 
function geoplugin_currencyCode() { return 'AUD';} 
function geoplugin_currencySymbol() { return '&#36;';} 
function geoplugin_currencyConverter(amt, symbol) { 
	if (!amt) { return false; } 
	var converted = amt * 0.9587170632; 
	if (converted <0) { return false; } 
	if (symbol === false) { return Math.round(converted * 100)/100; } 
	else { return '&#36;'+(Math.round(converted * 100)/100);} 
	return false; 
}
```

## Hello World 示例

```
<html>
 <head>
  <script language="JavaScript" src="https://www.geoplugin.net/javascript.gp" type="text/javascript"></script>
 </head>
 <body>
  <script language="Javascript"> document.write("Welcome to our visitors from "+geoplugin_city()+", "+geoplugin_countryName()); </script>
 </body>
</html>
```

整合成一个表单:[http://www . jquery 4 u . com/API-calls/geolocation-jquery-API-geo plugin/](http://www.jquery4u.com/api-calls/geolocation-jquery-api-geoplugin/)

更多关于插件的信息:[http://www.geoplugin.com/webservices/javascript](http://www.geoplugin.com/webservices/javascript)

## 分享这篇文章