# jQuery 在图像加载后运行代码

> 原文：<https://www.sitepoint.com/jquery-run-code-image-loads/>

简单的 jQuery 代码片段，在运行一些代码之前等待图像加载。唯一的缺点是您需要提供图像名称(包括扩展名)。

```
$('#myImage').attr('src', 'image.jpg').load(function() {  
	//run code
	alert('Image Loaded');  
});
```

## 分享这篇文章