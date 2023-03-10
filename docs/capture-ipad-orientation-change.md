# 捕捉 iPad 方向变化

> 原文：<https://www.sitepoint.com/capture-ipad-orientation-change/>

代码片段**捕捉你的 ipad 设备**的方向变化，然后你可以为每个肖像和风景设计添加非常具体的风格。该代码向 html 标记添加了一个类，以帮助使用 CSS3 媒体查询的 CSS(就像 Modernizr 等库一样)。

```
jQuery(document).ready(function($) {
 //capture ipad device change
function doOnOrientationChange()
{
    switch(window.orientation)
    {
      case -90: 
      case 90:
        alert('landscape'); //debug
        ////add class to assist with libraries like Modernizr
        $('html').removeClass('portrait').addClass('landscape'); 
        break;
      default:
        alert('portrait'); //debug
        $('html').removeClass('landscape').addClass('portrait');
        break;
    }
}

//setup event listener
window.addEventListener('orientationchange', doOnOrientationChange);

//initial call
doOnOrientationChange(); 
});
```

CSS 使用的例子。

```
/* styles for tablet only */
@media (min-width: 768px) and (max-width: 1024px)  {

    .portrait {

        /* styles for ipad landscape orientation */

    }    

    .landscape {

        /* styles for ipad landscape orientation */

    }
}
```

## 分享这篇文章