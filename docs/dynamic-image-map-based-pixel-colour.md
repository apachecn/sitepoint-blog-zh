# 基于像素颜色的动态图像映射

> 原文：<https://www.sitepoint.com/dynamic-image-map-based-pixel-colour/>

所以今天我创建了一个 html 图像地图，它根据用户点击的像素的颜色创建动态链接。创建这个的原因是因为这个地图是一个世界的矢量地图，正如你所知道的，所有的国家都有不同的大小，不可能创建一个正常的基于点/阵列的图像地图…它太大了，无法存储，加载时间太长。

## 这种方法的优点

*   它加载速度非常快
*   不需要知道图像的哪些区域被哪些链接所覆盖
*   图像区域可以是任何形状或大小

## 它是如何工作的

![dynamic-image-map-process](img/b888be5a35c05562c7addf914f0466ba.png "dynamic-image-map-process")

1.  用户点击图像上的任意位置
2.  jQuery 捕获用户单击位置的 x 和 y 坐标
3.  x 和 y 坐标通过 Ajax 发送到 PHP 控制脚本
4.  PHP 脚本在本地获取图像，并在 x 和 y 坐标处选择像素颜色
5.  PHP 脚本获取与十六进制颜色值匹配的国家
6.  PHP 脚本发回该国家页面的 url
7.  jQuery 重定向到通过 Ajax 调用返回的页面

## **如何设置的说明:**

用唯一的十六进制值创建图像(我用 adobe illustrator 创建了一个基于自由矢量的图像)。

![vector-map-illustrator](img/b463e1b8ab4e7c90abc2f9f9c7554bee.png "vector-map-illustrator")

将每个国家边界内的像素的十六进制颜色值存储在数据库中。

![database-hex-codes](img/cfdc98d327bf8b97342e6dbfc6c60f65.png "database-hex-codes")

**jQuery 代码:**

```
jQuery(document).ready(function($) {
	$('#theworldmapimage').live('click', function(ev) {
	   var X = $(this).offset().left;
	   var Y = $(this).offset().top;
	   mouseX = ev.pageX - X;
	   mouseY = ev.pageY - Y;
	   //FIX X AXIS ERORR MARGIN
	   //	mouseY -= 5;
	   if (mouseX > 500) { mouseX -= 13; }
	   //alert("x="+mouseX+" y="+mouseY);
		$.get("../php/php-functions/phpfunc-imagemap.php",{x: mouseX ,y: mouseY}, function(data){
			//alert(data);
			if (data != '') {
				//alert(data + " " +"x="+mouseX+" y="+mouseY);
				window.location.replace(data); //load the url of the clicked country
			}
			else {
				//alert("no data");
			}
		});
	});
});
```

**PHP 代码:**

```
> 16) & 0xFF;
    $g = ($rgb >> 8) & 0xFF;
    $b = $rgb & 0xFF;

    function rgb2html($r, $g=-1, $b=-1)
    {
        if (is_array($r) && sizeof($r) == 3)
            list($r, $g, $b) = $r;
        $r = intval($r); $g = intval($g);
        $b = intval($b);
        $r = dechex($r<0?0:($r>255?255:$r));
        $g = dechex($g<0?0:($g>255?255:$g));
        $b = dechex($b<0?0:($b>255?255:$b));
        $color = (strlen($r) 
```

最终图像如下所示:

![worldmap](img/19c89af44bf52886a55862939625cef6.png "worldmap")

[见演示](http://blogoola.com)(点击“显示世界地图”显示动态影像地图，然后点击任意国家)。

## 分享这篇文章