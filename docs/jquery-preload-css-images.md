# jQuery 预加载 CSS 图像

> 原文：<https://www.sitepoint.com/jquery-preload-css-images/>

例如，这是在渲染 JavaScript/jQuery 动画之前**预加载 css 图像**的一种有用方式。这个脚本是一个小 jQuery 插件，它缓存链接/导入的 CSS 文件中引用的所有图像(它遍历附加的每个样式表中的每个规则，如果规则的值包含图像 URL，它就加载图像，从而确保在文档中使用时图像在缓存中可用。


### 预加载 CSS 图像功能

```
jQuery.preloadCssImages = function(){

	var allImgs = [];//new array for all the image urls  
	var k = 0; //iterator for adding images
	var sheets = document.styleSheets;//array of stylesheets

	for(var i = 0; i<sheets .length="" i="" loop="" through="" each="" stylesheet="" var="" csspile="" create="" large="" string="" of="" all="" css="" rules="" in="" sheet="" csshref="(sheets[i].href)" sheets="" :="" baseurlarr="csshref.split('/');//split" href="" at="" to="" make="" array="" baseurlarr.pop="" remove="" file="" path="" from="" baseurl="" base="" url="" for="" the="" images="" this="" dir="" if="" tack="" on="" a="" needed="" w3="" thissheetrules="document.styleSheets[i].cssRules;" j="0;" else="" parse="" image="" urls="" and="" load="" them="" into="" dom="" imgurls="cssPile.match(/[^(]+.(gif|jpg|jpeg|png)/g);//reg" ex="" get="" between="" imgurls.length="">0 && imgUrls != ''){//loop array

			var arr = jQuery.makeArray(imgUrls);//create array from regex obj        

			jQuery(arr).each(function(){
				allImgs[k] = new Image(); //new img obj
				allImgs[k].src = (this[0] == '/' || this.match('http://')) ? this : baseURL + this;     //set src either absolute or rel to css dir
				k++;
			});
		}
	}//loop
	return allImgs;
}</sheets>
```

### 使用

```
//preload only
$.preloadCssImages();

//load into array
var preloadedImgs = $.preloadCssImages();
```

### 这是图像数组！

![preload-images](img/2cfc5c3cb74e4fe6de50a9764536b8d2.png "preload-images")

## 分享这篇文章