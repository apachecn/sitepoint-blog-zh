# 如何使用 HTML5 嵌入视频

> 原文：<https://www.sitepoint.com/how-to-embed-video-using-html5/>

那么如何使用 HTML5 语法在网页上嵌入视频呢？很简单:

`<video id="sampleMovie" src="HTML5Sample.mov" controls></video>`

就是这样！这就是你在你的网站上添加视频播放器所需要的一切，不需要任何第三方插件或来自其他网站的嵌入式代码——只需要一个简单的标签。

要是真有那么简单就好了。

HTML5 的复杂性不是来自语法，而是来自浏览器支持和视频编码。Web 开发人员习惯于努力解决浏览器兼容性问题，但是浏览器供应商之间关于编解码器标准的分歧也比平常更大。为了建立一个成功的 HTML5 网站，你需要考虑 Chrome 4、Firefox 4 和 Internet Explorer 9 等现代浏览器如何实现 HTML5 视频网络标准，以及如何处理用户在传统浏览器中观看视频的方式。

## 局限性和好处

首先，只是设定期望值，你应该知道 HTML5 在视频播放方面的局限性。

全屏播放目前不是 HTML5 规范的一部分。目前，一些(但不是全部)浏览器支持全屏播放。

默认的播放器菜单是非常基本的，可能需要花费相当多的努力来获得品牌标识或复杂的外观和感觉。使这变得复杂的是，不同的浏览器仍然以不同的方式实现 HTML5 和 CSS。例如，添加圆角，你的视频播放器在 IE 和 Firefox 中可能看起来是圆的，但在 Chrome 和 Safari 中仍然看起来是方的。

该规范中没有任何内容允许平滑或自适应的流媒体，这是一个严肃的视频播放网站的基本功能。

也许对某些人来说更重要的是，该规范不包括，而且显然永远也不会包括数字版权管理(DRM)。这意味着受版权保护的内容永远不会通过 HTML5 发布(至少不会被版权所有者发布)。

当然，还有编解码器。稍后我们会详细讨论这个问题。

放弃这些东西，你能换来什么？实际上，相当多。

首先，不太专注于视频的网站临时开发者将更容易在他们的网站上进行流媒体播放，而无需通过 YouTube 或 Vimeo 等第三方视频网站。以前，开发人员有三种直接嵌入视频的主要选择:

*   嵌入一个非流式文件，该文件在播放前必须完整下载
*   购买第三方插件(可能适用于也可能不适用于所有浏览器)
*   使用专用的媒体服务器，这种解决方案过于昂贵和/或复杂，不适用于大多数站点

HTML5 缓解了这些问题，它为开发人员提供了一种无需额外的第三方软件就能在一个可换肤的播放器中流式播放的方法。

第二，HTML5 规范允许开发者将视频交付给已经强制解决该问题的平台，如 iOS 产品(iPhone、iPad)。但从另一个角度来看，它也将开发者从限制或控制媒体交付方式的专有交付平台中解放出来。毕竟，这是拥有开放标准的目的。

最后，HTML5 不是外星技术，它是 HTML 的最新版本。这意味着 JavaScript 和 CSS 可以很好地处理它。开发人员将能够在客户端或服务器端操纵它，就像现有的 HTML 源代码一样。

## 视频编解码器

正如已经提到的，HTML5 视频的最大挑战是准备和合并多种编解码器中的内容。目前，每个浏览器都有自己喜欢的视频格式。而且看起来 HTML5 视频的支离破碎的实现在变好之前会变得更糟。

现在，您有三种可能的视频编码方式:

1.  H.264 一种流行的格式，可以利用硬件加速，由台式机和设备中的图形芯片支持；也是市场上许多较新的视频和移动设备的默认录制格式；然而，这种格式是有专利的，虽然它对于非商业用途仍然是免版税的，但它变得复杂，并且可能很昂贵。更多详情见本文件。当谈到商业内容分发时。支持:IE 9、Safari 3.1 和 Chrome(目前)
2.  2.ogg Theora——一个开放的标准，没有专利，免版税。支持:火狐 3.5，Chrome 4，Opera 10.5
3.  3.VP8(WebM)——Google 最近收购的新标准，以开源、免版税(但仍受专利保护)的格式发布。支持的:火狐 4.0，Chrome 6.0，Opera 10.6。

这里有一个图表，便于理解:

![](img/8a0413c230a342c797170b0aa0977b8d.png)

Chrome 值得特别一提，因为谷歌实际上将取消对三种 HTML5 视频格式之一的支持。在收购 WebM 格式的开发商和赞助商 On2 之后，谷歌宣布未来版本的 Chrome 将不再支持 H.264。相反，他们在 BSD 许可下发布了 WebM，授予全球用户免费、非独家、免版税的专利许可。因此，尽管 Chrome 是目前唯一支持所有三种格式的浏览器，但市场将很快在两种操作系统默认浏览器之间分裂，即仅支持 H.264 的 Internet Explorer 和 Safari，以及支持除 H.264 之外的所有其他浏览器

一个大的警告:为了简化这里的讨论，这个描述没有深入研究这三种基本格式的许多变化。一旦你了解了视频和音频配置文件的不同可能性，以及视频和音频容器和编解码器之间的区别，事情就变得更加复杂了。例如，虽然 iPhone 4 以 H.264 格式录制视频，但你不能将其放入 HTML5 网站；你必须先通过一个转换器。

很高兴宣布视频标准 X 是最受普遍支持的，应该成为 HTML5 视频的首选格式。不幸的是，正如你从上面的图表中看到的，事实并非如此。如果包括用户驱动的浏览器扩展安装，那么最广泛支持的格式应该是 Ogg Theora。然而，你不能假设用户，尤其是在企业工作场所的用户，有专门知识或能力安装额外的视频编解码器或浏览器扩展来观看 HTML5 视频。如果出于某种原因，您被迫为您的视频选择一种编解码器，您应该选择哪一种？看情况。考虑选项:

## 264

在这三个标准中，H.264 在媒体专业人士中具有发展势头、质量和认可度。它还通过 MPEG LA 得到了包括微软和苹果在内的一些业内大公司组成的大财团的支持。这就是批评者的主要抱怨:H.264 不是免费的。

对于消费者来说，是的，是免费的。而且大多数开发者都不用担心许可问题(目前是)。然而，对于视频分发网站来说，情况可能并非如此。H.264 专利许可的来龙去脉远远超出了本文的范围(而且[真的很难理解](http:/www.engadget.com/2010/05/04/know-your-rights-h-264-patent-licensing-and-you/))，但可以说人们有疑问。

对于大多数开发者来说，专利问题将在很大程度上归结为开放标准和图像质量之间的哲学争论。H.264 提供了比 Ogg(见下文)和 VP8 ( [WebM](http://www.businessinsider.com/google-has-a-problem-vp8-is-not-as-good-as-h264-2010-5) )更高的图像质量和更好的流媒体。由于包括 PC 和移动设备在内的多种平台上的硬件加速，它还享有性能优势。

最后，考虑生产的容易程度，这不是一个无关紧要的问题。所有主要的视频编辑器，包括 Final Cut、Adobe Premiere 和 Avid，都可以导出为 H.264 格式。Ogg Theora 或 VP8 就不一样了。如果你的商店正在制作自己的视频，而且是大量的视频，只使用 H.264 将会减少你工作流程中的一大块。

## Ogg Theora

Ogg 是唯一真正不受专利限制的标准。然而，它也可以说是质量最差的，尽管差不了多少。针对 H.264 的多个[头](http://www.htmlgoodies.com/html5/%20http:/arstechnica.com/open-source/news/2010/02/ogg-theora-vs-h264-head-to-head-comparisons.ars)到[头](http://www.htmlgoodies.com/html5/%20http:/jasonmillerdesign.com/Blog/Ogg_or_MP4_The_HTML5_Video_Codec_Comparison)的比较偏向于后者。虽然 Ogg 编码为稍微紧凑的文件，但它产生的图像质量比 H.264 低，并且在流传输性方面甚至更差。

## VP8 (WebM)

在高质量但受专利保护(H.264)和边缘质量但免版税(Ogg)这两个极端之间是 VP8，它可能是三个标准中最有争议的。到目前为止，测试表明，H.264 提供的视频质量略高于 [VP8](http://www.businessinsider.com/google-has-a-problem-vp8-is-not-as-good-as-h264-2010-5) ，但对于大多数商业目的而言，这种差异可以忽略不计。

更大的问题是开放标准。辩论的一方是谷歌，它放弃了 H.264，转而支持它的“开放”WebM 标准，甚至在知识共享许可下发布了 WebM。另一方面，几乎所有人都认为，在这种情况下，“开放”[可能并不真正意味着](http://blog.chromium.org/2011/01/more-about-chrome-html-video-codec.html)“开放”。以 JPEG 诉讼为例，[微软指出](http://www.htmlgoodies.com/html5/%20http:/blogs.msdn.com/b/ie/archive/2011/02/02/html5-and-web-video-questions-for-the-industry-from-the-community.aspx)即使 WebM 没有受到谷歌账户的专利保护，没有谷歌明确的用户赔偿，许多公司和个人仍然可能因部署 WebM 视频而面临专利侵权诉讼，表面上是来自 MPEG LA。

总之，在大多数情况下，没有一个解决方案能够满足所有项目在所有情况下的需求。但是对于大多数情况下的大多数项目，您可能会希望使用 H.264。它不仅是最广泛采用的格式，在生产和消费端都是如此，而且由于硬件加速，它具有明显的性能优势。对于商业视频网站来说，潜在的巨额许可费可能是有利于市场覆盖的子弹。

但是这整个辩论往往会因为一个主要原因而失败:无论是现在还是在不久的将来，所有的浏览器都将支持 HTML5。但是，并非所有浏览器都支持所有编解码器。就像你不会编写一个在 IE 中看起来很漂亮，但在 Firefox 中却很糟糕的样式表一样，你也想创建交叉兼容的视频标签。

## 视频转换器

对于开发人员来说，这实际上意味着您的视频内容需要以至少两种(最好是全部三种)视频格式进行编码，以便符合 HTML5 规范。幸运的是，您有几个工具可以帮助您完成这项任务。

Miro 视频转换器(Miro Video Converter):你的主要工具，这款开源转换器，可用于 Mac 和 Windows，可以将几乎任何视频源转换为你可能想要使用的所有 HTML5 格式，以及其他几种特定于设备和平台的格式。

[手刹](http://handbrake.fr):另一个用于 Mac 和 Windows(以及 Linux)的开源转换器，手刹是专门转换为 H.264 的好工具。

MPEG stream clip():MPEG stream clip 虽然不是开源的，但在 Mac 和 Windows 上都是免费的。它也可以处理 lH.264，尽管它也可以转换成 HTML5 规范之外的许多其他有用的格式。

Firefogg 是一种特殊的工具，Firefogg 是 Firefox 的扩展，只能将视频转换成 ogg 格式，因此得名。要使用它，你需要先下载 Firefox，然后安装扩展。

## <video>标签</video>

正如您所记得的，在 HTML5 中嵌入视频本质上可以归结为使用一个标签:`<video>`。但是和任何 HTML 标签一样，您有许多参数选项。以下是标签本身的要素:

**src** :源视频的位置和名称，与`<img />`标签的 src 参数作用相同。但是，请注意，除了在这里标识视频源之外，您还可以(并且可能应该)在标记之外进行标识(见下文)。

`<video id="sampleMovie" src="HTML5Sample.mov"> </video>`

**宽度和高度**:(可选)视频的尺寸。更准确的说，这是视频帧的宽度和高度，而不是视频本身。与`<img />`一样，省略它将导致浏览器默认为源视频的尺寸。然而，与`<img />`不同的是，输入不同于视频源的尺寸将会缩放视频，而不是扭曲视频。换句话说，嵌入的视频将保留其源的比率。如果指定的大小小于源，整个视频将缩小。但是，如果您指定的尺寸大于源尺寸，视频将以其原始尺寸显示，您指定的尺寸的剩余部分由空白填充。

`<video id="sampleMovie" src="HTML5Sample.mov" width=”640” height=”360”> </video>`

**控件**:添加默认视频控件叠加。如果您不想创建自己的自定义控件，这很有用。然而，如果你想深入定制播放器控件，你可以使用 JavaScript 和 CSS。该规范允许对 play()、pause()、currentTime、volume、muted 等方法和属性进行操作。

`<video id="sampleMovie" src="HTML5Sample.mov" controls> </video>`

**预载**:用户一点击页面就开始下载视频。要指示视频预加载，只需包含属性。若要指示它不预加载，请将该属性设置为“none”

`<video id="sampleMovie" src="HTML5Sample.mov" preload> </video>` `<video id="sampleMovie" src="HTML5Sample.mov" preload=”none”> </video>`

**自动播放**:纯恶。这指示浏览器在页面加载后立即开始播放视频。不要用这个。

`<video id="sampleMovie" src="HTML5Sample.mov" autoplay> </video>`

现在魔法来了。HTML5 规范包含一个方便的特性，允许您补偿当前围绕视频格式出现的浏览器功能障碍。您可以在`<video>`标签中嵌套多个标签，每个标签有一个不同的视频源，而不是使用 src 属性指定视频源。浏览器会自动浏览列表，选择第一个可以播放的。例如:

`<video id="sampleMovie" width="640" height="360" preload controls>
<source src="HTML5Sample_H264.mov" />
<source src="HTML5Sample_Ogg.ogv" />
<source src="HTML5Sample_WebM.webm" />
</video>`

如果你在 Chrome 中测试那段代码，你会得到 H.264 视频。不过，在 Firefox 中运行它，你会在同一个地方看到 Ogg 视频。

理想情况下，尽管并非所有浏览器都绝对需要，但您应该在 type 参数中包含一个 MIME 类型，以确保与所有浏览器兼容。此参数应指定视频类型，以及视频和音频编解码器。参数的细节将完全取决于您如何编码您的视频。许多可能性的列表可以在[这里](http://wiki.whatwg.org/wiki/Video_type_parameters)找到。

`<video id="sampleMovie" width="640" height="360" preload controls>
<source src="HTML5Sample_H264.mov" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"' />
<source src="HTML5Sample_Ogg.ogv" type='video/ogg; codecs="theora, vorbis"' />
<source src="HTML5Sample_WebM.webm" type='video/webm; codecs="vp8, vorbis"' />
</video>`

作为所有三种视频类型的实例，请访问[我自己的网站](http://www.justinwhitney.com/)。

在这个页面上，你会看到三个不同视频的源代码:H.264，Ogg 和 WebPM。视频本身标有编码解码器，因此您可以在浏览器中看到显示的内容。

关于 MIME 类型有一个小问题:您的服务器需要配置为识别各种类型。对于 Windows Server，这意味着向 IIS 添加各种 MIME 类型的条目。对于 Apache 服务器，您需要将 AddType 指令添加到 httpd.conf 或 local。htaccess 文件:

`AddType video/ogg .ogv
AddType video/mp4 .mp4
AddType video/webm .webm`

## 功能退化

尽管一个完整的多源<video>标签可以在多种 HTML5 浏览器上工作，但它仍然不能解决旧的、HTML5 之前的浏览器。幸运的是，该规范包含了向下兼容的机制。</video>

取您当前正在使用的任何`<object>`标签，或者将使用 HTML5 之前的标签，并将其添加到`<source>`参数列表的底部。通常，该对象将指定您正在使用的播放器(可能是第三方插件)，以及该播放器的参数和源代码。以下示例使用了一个流行的第三方播放器 Flowplayer，它有一个可用的免费版本:

`<video id="sampleMovie" width="640" height="360" preload controls>
<source src="HTML5Sample_H264.mov" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"' />
<source src="HTML5Sample_Ogg.ogv" type='video/ogg; codecs="theora, vorbis"' />
=<source src="HTML5Sample_WebM.webm" type='video/webm; codecs="vp8, vorbis"' />
<object type="application/x-shockwave-flash" data="http://releases.flowplayer.org/swf/flowplayer-3.2.1.swf" width="640" height="360">
<param name="movie" value="http://releases.flowplayer.org/swf/flowplayer-3.2.1.swf" />
<param name="allowFullScreen" value="true" />
<param name="wmode" value="transparent" />
<param name="flashvars" value='config={"clip":{"url":"HTML5Sample_flv.flv","autoPlay":false,"autoBuffering":true}}' />`

一篇更详细解释这一点的优秀文章是 Kroc Camen 的[“视频给大家”](http://camendesign.com/code/video_for_everybody)。你还可以在 Jonathan Neal 的[“人人视频生成器”](http://sandbox.thewikies.com/vfe-generator)中找到一个包含所有三种视频格式和一个 flash 对象的代码生成器。

## 资源

查看以下内容，了解有关 HTML5 视频的更多信息，包括特定编解码器的复杂性:

*   [关于 HTML5 视频和音频你需要知道的 5 件事](http://channel9.msdn.com/Events/MIX/MIX11/HTM12)——关于如何快速登陆你的网站的高评价视频漫游
*   [深入 HTML5](http://diveintohtml5.org/video.html)——学习 html 5，尤其是视频的重要资源
*   [视频支持演示](http://ie.microsoft.com/testdrive/graphics/videoformatsupport/default.html)–并排比较不同视频编解码器(包括浏览器插件)的网站
*   关于 Ogg-Theora 的更多信息，包括音频和视频编解码器、播放器和转换器
*   [谷歌放弃对 H.264 的支持](http://blog.chromium.org/2011/01/html-video-codec-support-in-chrome.html)
*   [H.264 和 WebM](http://wiki.xiph.org/TheoraSoftwareEncoders)——这是一篇关于相互竞争的编解码器标准和浏览器制造商观点的有趣文章

## 分享这篇文章