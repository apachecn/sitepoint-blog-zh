# photofy . js–jQuery 随机照片插件

> 原文：<https://www.sitepoint.com/photofy-js-jquery-random-photo-plugin/>

![photofy](img/3f9bc8d66290c2ba871a2c3e82278e6b.png "photofy(2)")

这个新的小插件从一个父容器或一个图像源中随机选择一组图像，然后随机显示它们的动画效果。所有需要的是一行 javascript，少量的 css，当然还有一些图片。

创建的演示给出了正在拍摄的新照片的效果，这对摄影师来说是一个很好的主意。然而，它还有许多其他用途，因为它最初是为在线商店创建的，产品图像显示在主屏幕上，这样潜在的客户就可以立即看到该网站有什么出售。

该插件易于使用，并与 jQuery 完全集成。所以要初始化这个插件，你只需要做$("#my-image-wrapper ")。photofy()；

[试玩](https://blog.tremaynechrist.co.uk/post/2011/04/17/Photofy-New-Animated-Photo-Swap-Plugin-for-JQuery.aspx)
[下载](http://blog.tremaynechrist.co.uk/Downloads/jquery-photofy.zip)

## jQuery 代码

```
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.5.2/jquery.min.js"></script>
<script type="text/javascript" src="/Downloads/jquery-photofy.min.js"></script> 
<script type="text/javascript">
      $(document).ready(function () {
          var myOptions = {
              imagecount: 21,
              images: imageList
          }
          $("#facesPhotoWrapper").photofy(myOptions);
      });
</script>
```

## HTML 代码

```
<div id="facesPhotoWrapper">
        <img src="https://blog.tremaynechrist.co.uk/Downloads/Faces/1.jpg" />
        <img src="https://blog.tremaynechrist.co.uk/Downloads/Faces/2.jpg" />
        <img src="https://blog.tremaynechrist.co.uk/Downloads/Faces/3.jpg" />
        <img src="https://blog.tremaynechrist.co.uk/Downloads/Faces/4.jpg" />
        <img src="https://blog.tremaynechrist.co.uk/Downloads/Faces/5.jpg" />
        <!-- etc -->
</div>
```

## 分享这篇文章