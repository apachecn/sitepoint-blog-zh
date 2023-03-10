# 更快的 YouTube 嵌入 JavaScript

> 原文：<https://www.sitepoint.com/faster-youtube-embeds-javascript/>

我们希望我们的访问者尽可能快地获得我们的内容，这意味着内容需要是轻量级的，并且具有最少的请求数。但我们想让用户留在我们的页面上，并从中获得乐趣。这就是嵌入视频进入场景的地方。

视频阐释了我们的文本内容，赋予它生命，通常由第三方提供。还要问什么？嗯，有一个隐藏的价格标签:**视频下载缓慢且沉重，即使访问者没有在看它们**。

通过无害的 iframe 调用的页面上的一个简单视频可以添加多达 6 个 HTTP 请求和多达 450kb 的内容。我在本文中提出的解决方案可以将这些数字减少到 1 个请求和每个视频大约 50kb，以及几个字节的 JavaScript(如果您不喜欢香草味的话，可以放在 jQuery 库上面)。

你知道吗？这个解决方案并不新鲜。之前由 Amit Agarwal 在 2013 年 4 月提出[。](http://www.labnol.org/internet/light-youtube-embeds/27941/)

## 那么，有什么诀窍呢？

在 Amit 的解决方案中，DOM 在文档加载时由 JavaScript 解析，每次调用 YouTube 视频(通过一个特殊的 div，而不是常规的 iframe)都被一个缩略图预览所取代，当点击它时，iframe 会附加到缩略图预览上。

通过这种方式，我们可以获得一个由第三方服务器提供的不错的预览缩略图，其重量只是整个视频播放器的一小部分。视频播放器仅在实际观看视频时加载。

## 我小小的附加值

我用普通的 JavaScript 和 jQuery 重写了 Amit 的代码，供感兴趣的人参考。我在代码中保留了最初的注释，以使其尽可能易于理解。我的版本在 HTML5 数据参数中添加了一个新功能，使您可以向 YouTube URL 添加任何参数来定制您的视频。

YouTube 提供了一个参数列表来显示和隐藏控件、品牌和信息，并设置视频质量或视频的开始帧(等等)。

*   **控件**:设置为 0，图层控件不在视频播放器上显示。
*   **modestbranding** :设置为 1，YouTube 标志从控制栏消失。
*   **rel** :设置为 0，当初始视频回放结束时，将不显示相关视频。
*   **showinfo** :设置为 0，在视频开始播放之前，播放器不会显示视频标题和上传者等信息。
*   **start** :设置为秒数，播放器从这个时间开始播放视频(或者从最近的关键帧开始)。
*   **vq** :如果支持，将其设置为想要的视频质量(例如:`hd720`当高质量可用时)

当在 click 事件上添加 YouTube iframe 时，一些参数被赋予值，即`autoplay`(我们希望视频在其缩略图被点击时立即开始)和`autohide`(当没有检测到交互时隐藏视频进度条和播放器控件)。

## 支持的 YouTube 缩略图

每个 YouTube 视频都有一个生成图像的列表。你可以通过网址`https://img.youtube.com/vi/<youtube-video-id>/<youtube-thumbnail>`访问它们(如果你愿意的话，这里的`img.youtube.com`甚至可以简称为`i.ytimg.com`)。

我们感兴趣的是:

*   default.jpg(默认版本，120 像素 x 90px 像素)
*   hqdefault.jpg(高质量版本，480 像素×360 像素)
*   mqdefault.jpg(中等质量版本，320 像素×180 像素)
*   sddefault.jpg(标准清晰度版本，640 像素×480 像素)
*   maxresdefault.jpg(最大分辨率版本，1.280 像素×720 像素)

例如，[参见这个 URL](https://img.youtube.com/vi/fsrJWUVoXeM/sddefault.jpg) ，您可以使用它来调整值，以查看不同的图像选项。

在下面的代码中，我们使用 sddefault.jpg 缩略图。根据您的需求和用户屏幕的功能，这可以替换为任何列出的支持格式。

## HTML

HTML 代码设置 YouTube 视频 ID，设置视频大小(宽度和高度)的样式，并在需要时列出 Youtube URL 参数。

```
<div class="youtube"
     id="fsrJWUVoXeM" 
     style="width: 500px; height: 281px;">
</div>

<div class="youtube" 
     id="lR4tJr7sMPM" 
     data-params="modestbranding=1&showinfo=0&controls=0&vq=hd720"
     style="width: 640px; height: 360px;">
</div>
```

## CSS

在用作示例的两个视频中，图像比例为 16:9，这将返回带有水平黑带的 sddefault.jpg 缩略图。为了在显示这个缩略图时隐藏它们，`background-position`属性被设置为`center`值，同时在 HTML `div`标签中设置图片大小，就像`style="width:500px;height:281px;"`。这样，可以在同一页面上显示不同大小的视频。

播放图标告诉用户内容不仅仅是一张图片，它被添加到缩略图顶部的一个层中，并通过不透明过渡来突出显示它。我们在这里使用了一个数据 URI base64 编码的 png(来自 [IconFinder](https://www.iconfinder.com/icons/118620/play_icon#size=64) )，它保存了一个 HTTP 请求并向下工作到 IE8。

```
.youtube {
    background-position: center;
    background-repeat: no-repeat;
    position: relative;
    display: inline-block;
    overflow: hidden;
    transition: all 200ms ease-out;
    cursor: pointer;
}

.youtube .play {
    background: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEAAAABACAYAAACqaXHeAAAERklEQVR4nOWbTWhcVRTHb1IJVoxGtNCNdal2JYJReC6GWuO83PM/59yUS3FRFARdFlwYP1CfiojQWt36sRCUurRIdVFXIn41lAoVdRGrG1M01YpKrWjiYmaSl8ybZJL3cd+YA//NLObd3++eO8x79z5jSq5Gw+8kov0AP8vMR5l1BtBZQM4B8ks75wCdZdYZZj5qLZ4hov2Nht9Z9vhKKSIaB/gI4M4w62KeAO6Mte4lYOq20FxrlqqOibhHmeWbvNC9ZfDX1mLae391aN6limO/gwgvAPJbWeAZuSDingdwXTBw7/0IsyaA/Fkh+KqOkD+YNfHej1QKD+y7iVlOhgLvFqFfNJvNGyuBJ+KDAF8MDd0tgS8y64OlgSdJMsysL4cG7SOHkyQZLhTee7+d2R2rAVy/S+Jd7/32ouBHAP4gNNRGQyTHc/84NhqNywZp5rvjjnnvt21aABFeCQ+RLwAf2hQ8s7sv9OCLk6AHNgQvIrvbfzKCD76g/O6cu7lf/iER/aQGgy448pExZmhdegAPhR9sObFWH1gT3lp7DaA/5bkIgJhZPgsNmz02novj+KqeApj1ubwXWe4kdyeznAgNvTpE/HQmvKqOMeuFogTUVQSRno+iaLRLAJF7uIgL9O4ubgL8aWgB7S44mNX+35YpICUiAvS9sBLkq1WzT+NFffl6AuoiApi6NT37h6sWkBIRZGkQ8YtLgyji6e1mBYTqCEBPG2Naz+0BWQgtoGoRgCzEsd9hAN1X5BfnFZASUfrSAFQNsyZ1FJASUVpHiLinDJG8U2cBZYogkrcNs5waBAGdstbeU9zdqpw0gPwwSAI6VUxHyFlDpOcHUUBBIuYNs14aZAE5RVwyzPr3/0EAEY0TyfGNjBWQvwZ +CTSbehfAH29mrID8bET0+0EUkAd8WYDOmqJ3ecsG30yr9wqRfm6Y+a1BEFDEjHfHvWmY9ck6CygHvBVr8Xhtb4ZE5HZA3y8DvBNA1TjnrmXWf+sioMwZX5V/VHXMGGMMoKdDCxCRvRWBdzKzdHEO+EisilbPyopHYqp6S9UCAsz4iojI7hUDAtyXVQgIDd6KnOoaWNkbI6FaPSuZGyMArsi7MZoloB4zviI/Nhr3X95jltwTRQmoIfgisy5ai+me67OI7fE4nrqjrqfK1t0eby0FPRB6oGVlchL3rgnfrq19RKbVBdhV9IOSwJmfmJi4vi/4ThERitwyCxVAFqydshuCX5awhQ9KtmuIWd8IDZED/nXT77rvVVv6sHRKwjYi91poqP7Dr+Y6JJ1VSZIMA3wkPNy6bX+o8Bcm0sXMdwM8Fxo0A3xORPaWBp6uPXsmbxCRD0NDL0dOANhVCXy6iAjMcjbcrMt3RITKwdMVRdFo+y5yvkL4eWZ+zHt/ZVD4dEVRNGotpst+dZZZH8k86lqn2pIvT/eqrNfn2xuyqYPZ8mv7s8pfn/8Pybm4TIjanscAAAAASUVORK5CYII=") no-repeat center center;
    background-size: 64px 64px;
    position: absolute;
    height: 100%;
    width: 100%;
    opacity: .8;
    filter: alpha(opacity=80);
    transition: all 0.2s ease-out;
}

.youtube .play:hover {
    opacity: 1;
    filter: alpha(opacity=100);
}
```

## 普通 JavaScript 实现

没有依赖性和最快的实现，普通 JavaScript 版本使用我能找到的最小的 DOM 就绪代码。

浏览器的差异仍然需要考虑，比如 IE8 缺乏对`getElementsByClassName`方法的支持。

```
'use strict';
function r(f){/in/.test(document.readyState)?setTimeout('r('+f+')',9):f()}
r(function(){
    if (!document.getElementsByClassName) {
        // IE8 support
        var getElementsByClassName = function(node, classname) {
            var a = [];
            var re = new RegExp('(^| )'+classname+'( |$)');
            var els = node.getElementsByTagName("*");
            for(var i=0,j=els.length; i<j; i++)
                if(re.test(els[i].className))a.push(els[i]);
            return a;
        }
        var videos = getElementsByClassName(document.body,"youtube");
    } else {
        var videos = document.getElementsByClassName("youtube");
    }

    var nb_videos = videos.length;
    for (var i=0; i<nb_videos; i++) {
        // Based on the YouTube ID, we can easily find the thumbnail image
        videos[i].style.backgroundImage = 'url(http://i.ytimg.com/vi/' + videos[i].id + '/sddefault.jpg)';

        // Overlay the Play icon to make it look like a video player
        var play = document.createElement("div");
        play.setAttribute("class","play");
        videos[i].appendChild(play);

        videos[i].onclick = function() {
            // Create an iFrame with autoplay set to true
            var iframe = document.createElement("iframe");
            var iframe_url = "https://www.youtube.com/embed/" + this.id + "?autoplay=1&autohide=1";
            if (this.getAttribute("data-params")) iframe_url+='&'+this.getAttribute("data-params");
            iframe.setAttribute("src",iframe_url);
            iframe.setAttribute("frameborder",'0');

            // The height and width of the iFrame should be the same as parent
            iframe.style.width  = this.style.width;
            iframe.style.height = this.style.height;

            // Replace the YouTube thumbnail with YouTube Player
            this.parentNode.replaceChild(iframe, this);
        }
    }
});
```

这是一个运行中的代码演示:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )用 JavaScript 优化的 Pen [YouTube Embed。](http://codepen.io/SitePoint/pen/CKFuh/)

## 分享这篇文章