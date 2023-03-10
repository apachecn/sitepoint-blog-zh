# Flowplayer 动态视频大小

> 原文：<https://www.sitepoint.com/flowplayer-dynamic-video-size/>

这就是你如何使 Flowplayer 视频大小动态变化的方法。如果您在可调整大小的窗口中播放视频，或者如果您希望以不同的分辨率(通常为 16:9(宽:高)的比例)播放不同的可变比特率的视频，这将非常方便。

**相关帖子:**

*   [**用 Flowplayer**](http://www.jquery4u.com/flowplayer/debugging-catching-errors-flowplayer-video/) 调试并捕捉错误
*   [**使用 jQuery 嵌入电影使用 Flowplayer**](http://www.jquery4u.com/flowplayer/jquery-embed-movie-flowplayer/)

### 固定大小

![360-640-res](img/eaf67b009a1dfad14704e4258e5eec7a.png "360-640-res")


### 适合窗口分辨率

![fit-window-res](img/ac4c62601c9e4ac2a71a0b95e24b6a07.png "fit-window-res")

### jQuery 代码

```
//change size
$('.change-size-btn').live('click', function(e)
{
    e.preventDefault();

    //find the video id
    var videoId = $(this).parents().find('.fms').attr('id');

    //vidDisplayType = fixed, fit, fullscreen
    var btnElem = $(this),
        vidElem = $('#'+videoId).find('object'),
        widgetContainer = $('#'+videoId).parents('.video-container'),
        vidDsplayType = btnElem.attr('vidDisplayType'),
        width, height;

    //FIXED
    if (vidDsplayType == 'fixed')
    {
        //get new fixed dimensions
        height = btnElem.attr('vidHeight'),
        width  = btnElem.attr('vidWidth');

    }
    //FLUID
    else if (vidDsplayType == 'fit')
    {
        //get widget dimensions
        height = widgetContainer.height(),
        width  = widgetContainer.width();
    }

    //RESIZE VIDEO
    console.log('changing video size to ' + width + ' by ' + height + '...');
    // vidElem.height(height).width(width);
    vidElem.height(height).width(width).fadeIn("slow", function()
    {
        console.log('done.');
        $f().getScreen().animate({ "width" : width, "height" : height}, 500);
    });
}
```

### 超文本标记语言

```
 Video size:
    <button viddisplaytype="fixed" vidheight="240" vidwidth="427" class="change-size-btn">240 x 427</button>
    <button viddisplaytype="fixed" vidheight="360" vidwidth="640" class="change-size-btn">360 x 640</button>
    <button viddisplaytype="fixed" vidheight="720" vidwidth="1280" class="change-size-btn">720 x 1280</button>
    <button viddisplaytype="fit" class="change-size-btn">Fit window</button> 
```

## 分享这篇文章