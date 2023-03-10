# jquery 将图像添加到浏览器缓存

> 原文：<https://www.sitepoint.com/jquery-add-image-browser-cache/>

这就是你如何使用 jQuery 预加载图像到**将图像添加到浏览器缓存**(作为隐藏的 DOM div 元素的一部分)。


```
(function($,D,W) {

    var JQUERY4U = {};

    JQUERY4U.UTIL = {

        images:
        {
            loadingImage: '<img class="loading" src="images/loading.gif" alt="loading" />',
            ajaxImage: '<img class="msg-loading" src="images/ajax-loader.gif" alt="" />',
            savingImage: '<img class="loading" src="images/saving.gif" alt="saving" />'
        },

        preloadImages: function()
        {
            $('body').append('<div id="img-cache" style="display:none></div>');
            $.each(JQUERY4U.UTIL.images, function(i,v)
            {
                $('#img-cache').append(v);
            });
        }

    }

    $(D).ready(function($) {
        JQUERY4U.UTIL.preloadImages(); //preload images in browser cache
    });

})(jQuery,document,window);
```

### 其他技术

```
// Array of images:
var imageArray = ['image1.jpeg', 'image2.png', 'image3.gif'];

// Loop through and declare an element that will not actually be used.
$.each(imageArray, function (i, val) {
  $('<img />').attr('src', val);
});
```

## 分享这篇文章