# 使用 WordPress 嵌入程序的完整指南

> 原文：<https://www.sitepoint.com/the-complete-guide-to-using-wordpress-embeds/>

当你经常在 WordPress 网站上写文章时，你知道很多时候你需要嵌入外部内容。

无论是来自 YouTube 或 Vimeo 的视频，来自 SoundCloud 或 Spotify 的音乐片段，Tweet 或 Vine，Instagram 上的 Flickr，都没有太多事情可做。你只需复制并粘贴你想嵌入到你的文章中的 URL，就这样。

这篇文章将介绍与 WordPress embed 和 oEmbed 功能相关的所有细节。

## 为什么 WordPress 需要嵌入功能？

与我用过的其他 CMS 相比，WordPress 做得非常好的一点是它嵌入外部媒体的便利性。

在不提及任何名字的情况下，使用最流行的内容管理系统(除了 WordPress)将一个简单的 YouTube 视频嵌入到一篇文章中可能是一个巨大的噩梦。

旁注:这是恳求，不是挖苦。在任何内容管理系统中，将 YouTube 视频嵌入到文章中应该是轻而易举的事情。

为什么会这样？

实际上，嵌入外部内容通常是一个潜在的安全问题。内容通常使用 iFrames 嵌入。iFrame 只是一个允许引用任何外部功能的新“窗口”。

例如，如果您想要嵌入来自 YouTube 的视频，您需要嵌入以下语法:

```
<iframe width="560" height="315" src="https://www.youtube.com/embed/aDorTBEhEtk" frameborder="0" allowfullscreen></iframe>
```

那么这有什么问题呢？

这个特定的代码实际上没有任何问题。然而，允许任何人在你的帖子中输入 iFrames 是一个严重的安全风险。

您可能会问，为什么这是一个安全风险？

虽然嵌入 YouTube iFrame 没问题，但是 iFrame 可以用于引用任何站点。具有恶意意图的用户可能实际上嵌入了引用托管恶意软件的站点的 iFrame。

最重要的是，iFrames 可以隐藏为 0 x 0 大小的像素(一个看不见的点)，这使得它很难被检测到，至少用肉眼是这样的。

因此，为什么在允许用户输入的地方不鼓励使用 iFrames。

在启用了 WordPress 支持的嵌入类型的其他 CMS 中，您可以通过执行以下操作之一来嵌入外部媒体:

1.  使用第三方插件
2.  允许嵌入 iFrames(从而产生潜在的安全风险)

事实上，即使使用 WordPress 嵌入功能，也只允许某些第三方服务。有一个内部白名单来决定哪些服务是允许的，哪些是不允许的。这是为了减轻任何潜在的安全问题。

## WordPress 嵌入功能是如何工作的？

WordPress 嵌入功能支持许多服务。你可以在这里找到 [Embed](https://codex.wordpress.org/Embed_Shortcode) 支持的服务的完整列表。

WordPress 嵌入功能通过实现 [oEmbed API](http://oembed.com/) 来工作——本质上，这是一种消费者网站(如 WordPress)可以显示来自提供商(如 YouTube)的嵌入内容的方式。

消费者向提供者发送一个 HTTP 请求，提供者用一个 JSON 或 XML HTTP 响应进行回复，其中的数据结构允许将媒体嵌入到消费者网站中。

这是请求:

```
http://www.youtube.com/oembed?url=http%3A//youtube.com/watch%3Fv%3DM3r2XDceM6A&format=json 
```

这是回应:

```
{"html": "\u003ciframe width=\"480\" height=\"270\" src=\"https:\/\/www.youtube.com\/embed\/M3r2XDceM6A?feature=oembed\" frameborder=\"0\" allowfullscreen\u003e\u003c\/iframe\u003e", "thumbnail_height": 360, 
"thumbnail_width": 480, 
"title": "Amazing Nintendo Facts", 
"width": 480, 
"height": 270, 
"type": "video", 
"provider_url": "https:\/\/www.youtube.com\/", 
"thumbnail_url": "https:\/\/i.ytimg.com\/vi\/M3r2XDceM6A\/hqdefault.jpg", 
"author_name": "ZackScott", "
author_url": "https:\/\/www.youtube.com\/user\/ZackScott", "version": "1.0", 
"provider_name": "YouTube"}
```

这当然是在幕后发生的事情。

## 将媒体嵌入 WordPress

现在我们已经理解了 WordPress 背后的功能，让我们看几个可以嵌入到 WordPress 帖子中的媒体的例子。

你可能已经这样做了:嵌入一个 YouTube 视频。

而不是必须使用与嵌入 YouTube 视频相关的正常语法，这类似于以下内容:

```
<iframe width="560" height="315" src="https://www.youtube.com/embed/O_Bw1_0u1P8" frameborder="0" allowfullscreen></iframe> 
```

你只需将 YouTube 视频的网址粘贴到你的帖子中:

```
[https://www.youtube.com/watch?v=O_Bw1_0u1P8](https://www.youtube.com/watch?v=O_Bw1_0u1P8)
```

![WordPress embed YouTube video](img/bb0b30d6b3d2b0315c99f58e2cce784b.png)

就是这样。

只要确保它实际上没有被转换成超链接。

您现在可以在编辑器中看到视频的预览。

一旦你的帖子上线，你会看到你的帖子中嵌入了 YouTube。

接下来，让我们在帖子中嵌入一条推文。找到你想要嵌入的 tweet，点击省略号获得链接，然后把它发布到你的 WordPress 帖子中。

![WordPress embed link to Tweet](img/c52c9033dba71c1e068d23dbbf4bfc01.png)

[https://Twitter . com/sitepointdotcom/status/756595431182835713](https://twitter.com/sitepointdotcom/status/756595431182835713)

结果将如下所示:

![WordPress embed Twitter](img/e795a2ba47b3a95c6a6ae1ec8ef78215.png)

这一切都是自动发生的，不需要用标签、块引号、iFrames 和其他任何东西。

让我们在帖子中嵌入一个 SoundCloud 文件。还是那句话，找到你想要嵌入的文件的 URL，然后粘贴到 post 中。

[https://soundcloud . com/version ingshow/ver 002-mat-marquis-on-the-version ONG-show](https://soundcloud.com/versioningshow/ver002-mat-marquis-on-the-versionong-show)

你可以从下面的截图中看到，当我们在帖子中输入 URL 时，我们立即得到了将要嵌入到帖子中的文件的预览。

![Embed SoundCloud WordPress](img/66f6b8c69e4896ccfcd804096747dff4.png)

我们可以继续展示各种其他的例子，但是我们确信你已经抓住了重点。

## 样式:设置嵌入媒体的(最大)大小

理论上没有太多造型的必要。WordPress oEmbed 特性能够满足大多数的风格。它还处理你的主题的响应，所以你也不需要真的担心。

但实际上，您可能需要实际确定要嵌入的媒体的大小。

WordPress oEmbed 功能实际上是由[【Embed】短代码](https://codex.wordpress.org/Embed_Shortcode)处理的。

使用[嵌入]短码，您可以设置介质的最大尺寸。这些都不是固定的，因为我们当然希望帖子保持响应。

```
[embed width="560" height="315"]https://www.youtube.com/watch?v=O_Bw1_0u1P8[/embed]
```

## 允许嵌入来自其他网站的媒体

我们前面提到过，出于安全原因，WordPress 不允许嵌入来自任何网站的媒体。

但是，如果您想要嵌入来自不受支持的网站的媒体，您当然可以这样做。

为此，您需要将该站点添加到内部白名单中。

有两种方法可以做到这一点，你应该使用的方法取决于网站是否支持 oEmbed API。

### 添加支持 oEmbed API 的站点

在你的主题或插件的 functions.php 文件中，调用下面的函数来添加一个新的站点到嵌入提供者白名单中。

[wp_oembed_add_provider](https://codex.wordpress.org/Function_Reference/wp_oembed_add_provider)

```
<?php wp_oembed_add_provider( $format, $provider, $regex ); ?>
```

举个例子，

```
<?php wp_oembed_add_provider( 'http://example.com/watchvideo/*', 'http://example.com/oembedprovider' ); ?>
```

如果媒体可以通过各种 URL 嵌入，您可以使用正则表达式来描述各种可能的 URL。如果我们想用正则表达式添加我们虚构的站点，即新管道，我们可以使用下面的代码:

```
<?php wp_oembed_add_provider( '#http://(www\.)?thenewtube\.com/watch.*#i', 'http://www.thenewtube.com/oembed', true ); ?>
```

### 添加不支持 oEmbed API 的站点

如果您要嵌入的站点不支持 o embed，您仍然可以将它们添加为提供者。

同样，通过 functions.php 文件，您需要使用[WP _ embed _ register _ handler](https://codex.wordpress.org/Function_Reference/wp_embed_register_handler)注册站点

```
<?php wp_embed_register_handler( $id, $regex, $callback, $priority ); ?>
```

您还需要提供一个回调函数，用于生成嵌入媒体所需的 HTML。

WordPress codex 提供了一个如何从福布斯网站嵌入视频的例子:

```
<?php

wp_embed_register_handler( 'forbes', '#http://(?:www|video)\.forbes\.com/(?:video/embed/embed\.html|embedvideo/)\?show=([\d]+)&format=frame&height=([\d]+)&width=([\d]+)&video=(.+?)($|&)#i', 'wp_embed_handler_forbes' );

function wp_embed_handler_forbes( $matches, $attr, $url, $rawattr ) {

    $embed = sprintf(
            '<iframe src="http://www.forbes.com/video/embed/embed.html?show=%1$s&format=frame&height=%2$s&width=%3$s&video=%4$s&mode=render" width="%3$spx" height="%2$spx" frameborder="0" scrolling="no" marginwidth="0" marginheight="0"></iframe>',
            esc_attr($matches[1]),
            esc_attr($matches[2]),
            esc_attr($matches[3]),
            esc_attr($matches[4])
            );

    return apply_filters( 'embed_forbes', $embed, $matches, $attr, $url, $rawattr );
}

?>
```

如果你需要为你的网站这样做，最好咨询并雇佣一个 WordPress 开发者，只是为了确保你不会引入任何可能破坏你的网站的错误。

## WordPress 作为 oEmbed 提供商(从 WordPress 4.4 版开始)

到目前为止，我们只是把 WordPress 作为第三方网站媒体的消费者来讨论，实际上 WordPress 才是嵌入式消费者。

![WordPress as oEmbed Provider](img/37b1192bfa4859df28713a0afb312433.png)

从 4.4 开始， [WordPress 也可以充当 oEmbed 内容](https://make.wordpress.org/core/2015/10/28/new-embeds-feature-in-wordpress-4-4/)的提供者。

这意味着，你网站上的任何公开帖子或帖子类型都可以通过你自己的 WordPress 网站嵌入到第三方网站中(通常是通过他们非常永久的 URL，并在实际 URL 的末尾添加一个嵌入——example . com/your-post/embed/)。

出于安全原因，嵌入驻留在沙盒 iFrame 中，但是 iFrame 的内容是一个模板，可以在提供者站点上完全由主题进行样式化或替换。

### 使用嵌入的函数和钩子

以下是与嵌入相关的四个最有用的功能:

*   [get_post_embed_url()](https://developer.wordpress.org/reference/functions/get_post_embed_url/) —检索在 iframe 中嵌入特定帖子的 url，例如[https://make . WordPress . org/core/2015/10/28/new-embeds-feature-in-WordPress-4-4/embed/](https://make.wordpress.org/core/2015/10/28/new-embeds-feature-in-wordpress-4-4/embed/)
*   [get_post_embed_html()](https://developer.wordpress.org/reference/functions/get_post_embed_html/) —检索特定帖子的完整嵌入代码
*   [get _ oembed _ endpoint _ url()](https://developer.wordpress.org/reference/functions/get_oembed_endpoint_url/)—检索给定永久链接的 o embed 端点 URL，例如【https://make.wordpress.org/core/?oembed=true】T2&URL =。这用于将 oEmbed 发现链接添加到单个帖子的 HTML 中。
*   [get _ oembed _ response _ data()](https://developer.wordpress.org/reference/functions/get_oembed_response_data/)—根据 oEmbed 规范检索给定帖子的 o embed 响应数据。

### 自定义 oEmbed 输出

与定制任何模板文件类似，可以使用 *embed_head* 和 *embed_footer* 将定制代码添加到嵌入模板的开头和结尾，来设计帖子的嵌入模板。

当使用该模板时，还将发送 *X-WP-embed:true* 头。这使得在任何使用 WordPress oEmbeds 的应用程序中定位嵌入的文章变得更加容易。

## 摘要

正如我们在这篇文章中看到的，WordPress 的嵌入特性非常丰富。对于作者和开发者来说，WordPress 嵌入有很多方法可以用来增强嵌入媒体的内容。

你有没有以任何创造性的方式使用 WordPress 嵌入？我们很乐意看到并分享你的例子。

## 分享这篇文章