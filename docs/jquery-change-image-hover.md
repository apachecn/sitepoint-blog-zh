# 悬停时 jQuery 改变图像

> 原文：<https://www.sitepoint.com/jquery-change-image-hover/>

jQuery 的一个非常酷的特性是能够动态改变图像，就像当你将鼠标移动到屏幕的某个区域时，图像就会改变。

![jquery-change-image-on-hover](img/f5f3c5c05539b9eb2009373f967139e3.png "animate-on-hover")

方法:引用 image src 属性，并通过两个函数对其进行更改。第一个函数改变图像，第二个函数将图像改回来。然后，在 HTML 中，我们向鼠标将触发功能的区域添加一个事件(注意，这些触发器也可以添加到 JavaScript 中)。就这么简单。 [见现场演示](http://blogoola.com/#bottombar2)

## jQuery 动态改变图像

```
//this code sits outside the (document).ready function

function twittereyesopen() {
	//alert("open");
	var name_element = $('#twitter-image'); 
	name_element.src =img/page-images/twitter-eyes-open.jpg";
}

function twittereyesclosed() {
	//alert("closed");
	var name_element = $('#twitter-image');
	name_element.src =img/page-images/twitter-eyes-closed.jpg";
}
```

**HTML 中包含以下内容:**

## 分享这篇文章