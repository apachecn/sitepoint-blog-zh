# 通过 Pinterest 行动号召分享图片

> 原文：<https://www.sitepoint.com/images-pinterest-call-to-action/>

在本文中，我将演示如何在用户主动与图片交互时，在图片上添加 Pinterest 的行动号召。

首先，让我们看看为什么你会想这样做。

## 分享是关怀

![Picture of a tea pot with a Pinterest share button appearing on hover](img/23f8b3405712a387de649b1817110130.png)

什么是好内容通常在很大程度上取决于终端用户的特殊需求和兴趣。好的内容可能是吸引人的、有效的、简单的、实用的、信息丰富的、鼓舞人心的……等等。

我们习惯于添加社交网络按钮或链接来共享页面(理想情况下通过[无脂按钮](https://www.sitepoint.com/social-media-button-links/))。脸书、Twitter 和 Google+通常是首要必备软件。

我们可能还希望用户能够分享我们页面的*部分*。页面内容通常由块组成，并且这些块中的每一个都可以被提供有其自己偏好的社交网络共享特征。

例如，一个报价可以针对 Twitter 的 140 个字符限制，一张图片可以针对 Pinterest。

让我们来看看你是如何设置图片在 Pinterest 上分享的。

## Pin URL 的剖析

一个 [Pinterest Pin URL](https://developers.pinterest.com/pin_it/) 由四个部分组成:

*   URL 的 Pinterest 部分:`https://www.pinterest.com/pin/create/button/`
*   `url`参数:链接到的页面的地址，URL 编码的
*   `media`参数:要共享的图片的地址，URL 编码
*   `description`参数:Pinterest 上显示的描述性文本，URL 编码，最多 500 个字符，但 [200 个字符似乎是最佳选择](http://danzarrella.com/infographic-how-to-get-more-pins-and-repins-on-pinterest.html)。

如果你添加 Pinterest 自己的 JavaScript，可以添加一些 HTML 数据元素，特别是`data-pin-do`和`data-pin-config`，但在这种情况下并不需要，因为这是一种无脂肪的方法，我们依赖于严格的最少代码量。

## 硬编码的方式

我们想要得到的代码如下所示:

```
<a href="http://www.pinterest.com/pin/create/button/?url=<page url (encoded)>&media=<picture url (encoded)>&description=<HTML-encoded description>" target="_blank" class="pinterest-anchor pinterest-hidden">
    <div class="pinterest-logo"> </div>
</a>
<img src="<picture url>" data-pin="pinIt" alt="<description>">
```

每张 sharaeble 图片(标有`data-pin`属性`pinIt`)都带有一个指向 Pinterest 共享页面的锚，在其 URL 中带有正确的属性，嵌入了一个`div`来显示徽标。

这里用到三个 CSS 类:`pinterest-anchor`，绝对设置图片左上角的 Pinterest logo 位置；`pinterest-hidden`，设置标识的可见性状态，`pinterest-logo`，标识本身，40×40 像素，在 CSS 中以 base64 编码。

```
.pinterest-anchor {
    margin-left:10px;
    margin-top:10px;
    position:absolute;
    z-index:42;
    width:40px;
    height:40px;
}
.pinterest-hidden {
    display:none;
}
.pinterest-anchor:hover {
    display:block !important;
}
.pinterest-logo {
    background: url("data:image/png;base64,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") no-repeat center center;
    background-size: 40px 40px;
    height: 100%;
    width: 100%;
}
```

动画部分——在悬停图片时显示和隐藏徽标——由 JavaScript 处理，使用 jQuery 符号:

```
$("img[data-pin='pinIt']").each(function() {
    $(this).hover(function() {
        $(this).prev().css("display", "block")
    }, function() {
        $(this).prev().css("display", "none")
    });
});
```

## 自动化是你的朋友

但是我不想用 sharaeble 图片前面的那段特定的 HTML 来膨胀我的内容代码。如果有什么需要改变的话，光是维护方面就需要一些成本——比如 Pinterest 分享网址、参数以及对其他社交网络的支持。

因为 JavaScript 已经用于处理鼠标交互，所以让我们更进一步，为页面上的任何图像生成代码。

再次使用 jQuery，我们得到:

```
$("img[data-pin='pinIt']").each(function() {
    $(this).before('<a href="http://www.pinterest.com/pin/create/button/?url=' + encodeURIComponent($(location).attr("href")) + '&media=' + encodeURIComponent($(this).attr("src")) + '&description=' + encodeURIComponent($(this).attr("alt")) + '" target="_blank" class="pinterest-anchor pinterest-hidden"><div class="pinterest-logo"> </div></a>');
    $(this).hover(function() {
        $(this).prev().css("display", "block")
    }, function() {
        $(this).prev().css("display", "none")
    });
});
```

对于每一张`data-pin`属性设置为`pinIt`的图片，我们在它前面加上共享锚，直接从页面`location`属性获取页面 URL，从图片`href`获取图片 URL，从其`alt`文本获取描述，同时对 URI 组件进行适当的编码。

下面的代码笔演示了这一点

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen[Pinterest pin-it Call-To-Action(jQuery)](http://codepen.io/SitePoint/pen/yyEveb/)。