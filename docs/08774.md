# HTML5 视频与 HTML 视频

> 原文：<https://www.sitepoint.com/html5-video-vs-html-video/>

如今，设计人员和开发人员有几种将视频集成到网页中的选择。你可以设计自己的视频播放器，使用免费的小工具，嵌入 YouTube 视频代码或购买一个别致的 flash 弹出播放器。

在线视频播放器有多种形式、形状和布局——都是为了增强您的网络形象而设计的。但无论你使用哪种播放器，主要目的是让所有的访问者都能快速访问你的视频内容。

这意味着每个用户应该能够播放视频，而不会收到不友好的“安装 Flash 插件以观看该视频”的消息。这样的警告会损害用户体验，提高网站的跳出率。

考虑到这一点，你可能会倾向于 HTML5 视频技术。毕竟 HTML5 承诺直接在浏览器中播放视频，不需要任何插件。乍一看，这似乎是终极视频解决方案。但是在您将网站视频切换到 HTML5 之前，让我们比较一下经典 HTML 视频和创新 HTML5 视频集成的优缺点。

Flash 是 HTML 视频中间件。这是现在将视频嵌入网站最传统的方式。标签和 Flash 插件有助于在浏览器中播放视频。

下面是一些典型 HTML 视频嵌入代码:

```
<object id=0 type="application/x-shockwave-flash" data=player_flv_maxi.swf width=512 height=384>

<param name="movie" value=player_flv_maxi.swf />

<param name="wmode" value="opaque" />

<param name="allowFullScreen" value="true" />

<param name="allowScriptAccess" value="sameDomain" />

<param name="quality" value="high" />

<param name="menu" value="true" />

<param name="autoplay" value="false" />

<param name="autoload" value="false" />

<param name="FlashVars" value="flv=movie1.flv&width=512&height=384&autoplay=0&autoload=0&
  buffer=5&buffermessage=&playercolor=464646&loadingcolor=999898&buttoncolor=ffffff&
  buttonovercolor=dddcdc&slidercolor=ffffff&sliderovercolor=dddcdc&showvolume=1&showfullscreen=1&
  playeralpha=100&title=movie1.flv&margin=0&buffershowbg=0" />

</object>
```

与所有中间件一样，Adobe Flash 插件落后了，确实需要不断更新。

HTML5 不需要任何插件或更新。有了新的`<video>`标签，你可以像这样[在页面](https://www.sitepoint.com/how-to-embed-video-using-html5/)中插入视频:

```
<video width="320"

    height="240"

    poster="intro.jpg"

    autoplay

    controls

    loop>

    This content appears if the video tag or the codec is not supported.

    <source src="intro.mp4" type="video/mp4" />

    <source src="intro.webm" type="video/webm" />

    <source src="intro.ogv" type="video/ogg" />

    </video>
```

如你所见，使用 HTML5 时，视频应该以三种主要格式提供:H.264 (.mp4)、WebM 和 Theora OGG。涵盖所有三种格式为您提供了在所有设备上正确回放的最佳机会。

不过 HTML5 并不能保证在*所有*浏览器中播放。Internet Explorer 6、7 和 8 无法播放 HTML5 视频。当你知道你有使用这种浏览器的站点访问者时，这就变得很重要了。因此，您应该彻底检查您的网站流量统计，以统计 HTML5 视频迁移之前的 Internet Explorer 版本的用户百分比。谷歌分析是一个很好的工具。

结果可能是 60%的观众坚持使用老式的浏览器，因此 HTML5 对你的网站来说可能不是一个好的视频补救措施。或者，如果你的大多数访问者是移动网络消费者，HTML5 视频值得嵌入到你的网站中。

下表总结了需要考虑的最常见的 HTML/HTML5 优点和缺点:

**HTML 视频与 HTML5 视频**

| **Markup** | **优点** | **缺点** |
| **T1**HTML 视频**** | 众所周知、久经考验的标记语言 | Flash 插件是必须的 |
| 受所有桌面浏览器支持:Internet Explorer、Firefox、Chrome、Opera、Safari | 持续的插件更新 |
| 许多视频托管选项:自有服务器、YouTube、Vimeo 等 | 缓慢、高负载的视频 |
| 许多视频嵌入方法:从高级编码到简单的 YouTube 复制粘贴代码 | 无法在 iPad 和 iPhone 等手机上播放视频 |
| **HTML5** | 不需要插件 | Internet Explorer 6、7、8 不支持 |
| 适用于桌面和移动设备 | 需要视频转换 |
| 灵活的播放器设置:用户可以移动和旋转网络播放器 | 没有稳定的规格。你必须学习新的标记原则 |

乍一看，HTML5 视频的弊端似乎比资产更多。但如果你进一步钻研 HTML5 视频，你会发现新兴的 web 技术并不是那么晦涩难懂。它已经为开发者提供了一个坚实的工具箱。

Internet Explorer 6、7 或 8 不支持 HTML5 视频是真的。因此，对于这些用户，提供一个后备的 YouTube 直接链接或下载，以便网站访问者可以观看标准的 Flash 视频:

```
<video width="640" height="360" controls>

    <source src="__VIDEO__.MP4"  type="video/mp4" />

    <source src="__VIDEO__.OGV"  type="video/ogg" />

    <object width="640" height="360" type="application/x-shockwave-flash" data="__FLASH__.SWF">

        <param name="movie" value="__FLASH__.SWF" />

        <param name="flashvars" value="controlbar=over&amp;image=__POSTER__.JPG&amp;file=__VIDEO__.MP4" />

        <img src="__VIDEO__.JPG" width="640" height="360" alt="__TITLE__" title="No video playback 
            capabilities, please download the video below" />

    </object>

</video>

<p>     <strong>Download Video:</strong>

              Closed Format:         <a href="__VIDEO__.MP4">"MP4"</a>

              Open Format:            <a href="__VIDEO__.OGV">"Ogg"</a>

</p>
```

将 HTML5 转换成三种格式(H.264、OGG 和 WebM)并不像听起来那么可怕。有几种免费的解决方案:

*   **[手刹视频转换器](http://handbrake.fr/)用于 Mac** 。免费，这个著名的视频转换软件可以处理 HTML5 视频转换
*   **[Freemake 视频转换器 3.0](http://www.freemake.com/free_video_converter/) for Windows** 。这是 100%免费的视频转换工具。选择一个视频，点击“HTML5”按钮，你会得到所有三种格式的视频文件和视频播放器代码供嵌入。Freemake 的视频转换器为你工作(披露:我为 Freemake 工作)。
*   **[扎姆扎](http://zamzar.com/)。**免费在线视频转换器。Zamzar 编码有点慢。此外，你将通过电子邮件获得文件。

所以，即使是缺点也不意味着你今天不能使用 HTML5。

相反，HTML 的缺点实际上更难避免。易受攻击的 Adobe Flash 插件不适合手持设备，是流媒体视频集成的绊脚石。最流行的移动硬件不支持 Flash 视频。最近， [Adobe 甚至取消了进一步的 Flash 插件开发](https://www.sitepoint.com/adobe-abandons-mobile-flash/)，转而使用他们的 Adobe Edge 设计工具开发 HTML5 / CSS3 生态系统。

显然，HTML5 正在名利双收，但现在宣布 HTML5 完全战胜 Flash video 还为时过早，因为 Flash 仍然在流媒体 web 服务上占据主导地位。至少我们应该做好准备，在没有交通损失的情况下顺利地从一个加价点转移到另一个加价点。

你用什么方法在网页上实现视频？

## 分享这篇文章