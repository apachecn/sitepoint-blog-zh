# Ajax-缩放评论

> 原文：<https://www.sitepoint.com/ajax-zoom-review/>

[![Ajax-Zoom](img/87b01b9d6d9708453b64734fbad8257d.png)](https://www.sitepoint.com/wp-content/uploads/jquery4u/2013/04/Ajax-Zoom.jpg)

如果你想在你的网站上展示高分辨率图像，Ajax-Zoom 绝对不会出错。这个 jQuery 插件 Ajax-Zoom 不需要任何浏览器插件，如 Flash、Java 或 Silverlight，非常适合电子商务、扩展产品演示和其他应用程序。

**[查看演示](http://www.ajax-zoom.com/index.php?cid=examples)**

什么是 Ajax-Zoom？

Ajax-Zoom 是一个非常强大的图像缩放和平移插件，具有 360°旋转选项。它有基于 jQuery 和 PHP 的图片库选项，可以顺利地集成到你的任何网站，因为它还有 300 多个其他选项！完全打包的免费/低成本 jQuery zoom 解决方案，用于在 web 上呈现高分辨率图像。

**特殊功能**:

Ajax-Zoom 具有全屏功能，各种图像保护选项，动态水印/注释。不需要像 Flash，Java 或 Silverlight 这样的插件，有时这可能是一种痛苦。它支持 JPG，TIF，PNG，BMP，GIF 和 PSD 图像类型。支持鼠标滚轮的无级缩放、平移和裁剪界面(缩放到点)。iPhone / iPad 支持。你知道有趣的是什么吗？Ajax loader 和背景可以替换成你自己的图片，这是免费的！

全屏模式
[![Ajax-Zoom FullScreen](img/7c772c9a85ce5144bec0b6eb49b5b4fb.png)](https://www.sitepoint.com/wp-content/uploads/jquery4u/2013/04/Ajax-Zoom-FullScreen1.jpg)

它使用 AJAX-ZOOM 3D/360/2D 播放器的热点插件很酷，因为它现在可以创建多个热点或矩形图像区域，并为它们设置几个点击/鼠标悬停动作(例如，链接、工具提示、弹出灯箱)。

**代码示例 1** :
与任何其他 AJAX-ZOOM 实现或示例的唯一区别在于，这个编辑器生成的带有 JSON 数据的 JavaScript 文件是用 jquery . fn . axzm . loadhotspotsfromjsfile API 加载到 AJAX-ZOOM onLoad 回调中的，见下文…

```
 Loading, please wait...

// Create empty object
var ajaxZoom = {}; 

// Define callbacks, for complete list check the docs
ajaxZoom.opt = {
	onBeforeStart: function(){
		// Set backgrounf color, can also be done in css file
		jQuery(".zoomContainer").css({backgroundColor: "#FFFFFF"});		
	},
	onLoad: function(){
		// Some settings can be set inline
		jQuery.axZm.spinReverse = false;

		// Load hotspots over this function... 
		// or just define jQuery.axZm.hotspots here and trigger jQuery.fn.axZm.initHotspots(); 
		jQuery.fn.axZm.loadHotspotsFromJsFile("../pic/hotspotJS/eos_1100D.js", false, function(){
			// Do something if you want				
		});
	}
};

// Define the path to the axZm folder, adjust the path if needed!
ajaxZoom.path = "../axZm/"; 

// Define your custom parameter query string
// example=spinIpad has many presets for 360 images
// You can change them in /axZm/zoomConfig.inc.php after elseif ($_GET['example'] == 'spinIpad')
// 3dDir - best absolute path to the folder with 360/3D images
// for 2D you can use zoomData, e.g. zoomData=/your/path/image1.jpg|/your/otherPath/image2.jpg
ajaxZoom.parameter = "example=spinIpad&3dDir=/pic/zoom3d/Uvex_Occhiali"; 

// The ID of the element where ajax-zoom has to be inserted into
ajaxZoom.divID = "abc"; 
```

代码示例 2 :
这个示例不需要 PHP 代码，也可以用所见即所得编辑器插入到任何内容中。您所要做的就是用源图像的路径定义 ajaxZoom.parameter 字符串。

```
 Loading, please wait...

// Create new object
var ajaxZoom = {}; 

// Define the path to the axZm folder
ajaxZoom.path = "../axZm/"; 

// Define your custom parameter query string
ajaxZoom.parameter = "example=2&zoomData=/pic/zoom/furniture/test_furniture1.png|/pic/zoom/furniture/test_furniture2.png|/pic/zoom/boutique/test_boutique1.png|/pic/zoom/boutique/test_boutique2.png"; 

// The ID of the element where ajax-zoom has to be inserted into
ajaxZoom.divID = "test"; 
```

**如何获得**:

好的一面是，你可以随时下载并测试 Ajax-Zoom，如果你已经对它有了很好的感觉，并且已经启用了某些功能，你可以购买它。

查看 **[演示](http://www.ajax-zoom.com/index.php?cid=examples)**

联系[支持](http://www.ajax-zoom.com/index.php?cid=contact)进行进一步查询。

## 分享这篇文章