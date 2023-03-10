# 使用 jQuery 获取客户端 IP

> 原文：<https://www.sitepoint.com/client-ip-jquery/>

使用 JavaScript/jQuery 获取客户端 IP 的一些代码片段。

## 1.单向的

这个给你经度/纬度和时区。

[试试看！](https://smart-ip.net/geoip-json?callback=?)

```
$(document).ready( function() { $.getJSON( "https://smart-ip.net/geoip-json?callback=?", function(data){ alert( data.host); } );});
```

退货:

```
?(
{
   source: "smart-ip.net",
   host: "14.200.158.65",
   lang: "en",
   countryName: "Australia",
   countryCode: "AU",
   city: "South Sydney Municipality",
   region: "New South Wales",
   latitude: "-33.9000",
   longitude: "151.2000",
   timezone: "Australia/NSW"
}
)
```

## 2.双向的

这个提供 JSON。

[试试看！](https://api.hostip.info/get_json.php)

```
function myIP() {
    if (window.XMLHttpRequest) xmlhttp = new XMLHttpRequest();
    else xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");

    xmlhttp.open("GET"," http://api.hostip.info/get_html.php ",false);
    xmlhttp.send();

    hostipInfo = xmlhttp.responseText.split("n");

    for (i=0; hostipInfo.length >= i; i++) {
        ipAddress = hostipInfo[i].split(":");
        if ( ipAddress[0] == "IP" ) return ipAddress[1];
    }

    return false;
}

// console.log(myIP());
```

退货:

```
{
  country_name: "UNITED STATES",
  country_code: "US",
  city: "(Unknown city)",
  ip: "14.200.158.65"
}
```

## 3.三路

[试试看！](https://l2.io/ip.js)

```
https://l2.io/ip.js
Syntax : https://l2.io/ip
Diplay Client IP address
--> x.x.x.x

Syntax : 
```