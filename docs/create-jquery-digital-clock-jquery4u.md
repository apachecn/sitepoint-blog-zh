# 创建自己的 jQuery 数字时钟

> 原文：<https://www.sitepoint.com/create-jquery-digital-clock-jquery4u/>

这是一种非常酷的方式，可以在 web 页面上显示时间，使用 jQuery 获取当前时间，然后每 1 秒刷新一次时钟。

```
function updateClock ( )
 	{
 	var currentTime = new Date ( );
  	var currentHours = currentTime.getHours ( );
  	var currentMinutes = currentTime.getMinutes ( );
  	var currentSeconds = currentTime.getSeconds ( );

  	// Pad the minutes and seconds with leading zeros, if required
  	currentMinutes = ( currentMinutes < 10 ? "0" : "" ) + currentMinutes;
  	currentSeconds = ( currentSeconds < 10 ? "0" : "" ) + currentSeconds;

  	// Choose either "AM" or "PM" as appropriate
  	var timeOfDay = ( currentHours < 12 ) ? "AM" : "PM";

  	// Convert the hours component to 12-hour format if needed
  	currentHours = ( currentHours > 12 ) ? currentHours - 12 : currentHours;

  	// Convert an hours component of "0" to "12"
  	currentHours = ( currentHours == 0 ) ? 12 : currentHours;

  	// Compose the string for display
  	var currentTimeString = currentHours + ":" + currentMinutes + ":" + currentSeconds + " " + timeOfDay;

   	$("#clock").html(currentTimeString);

 }

$(document).ready(function()
{
   setInterval('updateClock()', 1000);
});
```

## 分享这篇文章