# 使用 jQuery 通过 Flowplayer 嵌入电影

> 原文：<https://www.sitepoint.com/jquery-embed-movie-flowplayer/>

按照这些简单的步骤来使用 Flowplayer (一个开源视频播放器)嵌入一部电影。你可以对播放器进行大量的定制，我将在单独的帖子中更详细地分享——包括 ipad 支持、定制控制、RTMP/RTSP 直播、自动播放、剪辑缩放、播放器调试、更改分辨率、动态视频大小等。这篇帖子详细介绍了播放器的最小设置，以帮助你入门。


**相关帖子:**

*   [**Flowplayer 动态视频大小**](http://www.jquery4u.com/flowplayer/flowplayer-dynamic-video-size/)
*   [**用 Flowplayer**](http://www.jquery4u.com/flowplayer/debugging-catching-errors-flowplayer-video/) 调试并捕捉错误

![scaling-fit](img/adf743e3f774b7356fb1e3bd84abdde6.png "scaling-fit")

### 超文本标记语言

```
 `**Status** Ready.`

### jQuery

```
//video or stream address
var streamAddressFull = "http://streamaddress/mp4:filename/playlist.m3u8",
    vidElem = $('#fms'),
    statusElem = $('#video-stream-status .status');

$f('fms', "http://releases.flowplayer.org/swf/flowplayer-3.2.7.swf",
{
    /** ------- Error Handling -------- **/

    debug: debug,  //output all events triggered by the Flash component to the console

    onError: function(e)
    {
        statusElem.html("Error: (code:"+e+").");
    },

    // we need at least this version of flash
    version: [9, 115],

    // older versions will see a custom message
    onFail: function()
    {
          statusElem.html("You need the latest Flash version to view MP4 movies. " + "Your version is " + this.getVersion());
    },

    onBeforeLoad:function()
    {
        statusElem.html("Loading...");
    },

    /** ------- Clip Configurations -------- **/

    clip:
    {
        url: streamAddressFull,
        scaling: 'fit', //fit, scale, orig, half
        autoPlay: true,
        autoBuffering: true,

        onStart: function(clip)
        {
            statusElem.html("Playing.");
        }
    },

    plugins:
    {
        controls:
        {
            // display properties such as size, location and opacity
            right: 0,
            bottom: 0,
            opacity: 0.95,
            backgroundGradient: 'low', //faded slightly
        }
    },

    /** ------- Look and Feel -------- **/

    canvas:
    {
        backgroundGradient: 'none',
        backgroundColor: '#000000'
    }

});
```

## 分享这篇文章

```