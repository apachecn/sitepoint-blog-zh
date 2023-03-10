# 修复 jQuery $的 5 种方法。找不到缺少的浏览器功能

> 原文：<https://www.sitepoint.com/fix-jquery-browser-function/>

好了，现在已经正式宣布 **[$了。浏览器](https://raw.github.com/jquery/jquery-browser/master/src/jquery.browser.js)功能已被弃用**。我们该怎么办？不要惊慌，我有下面的 **5 个可能的解决方案供你酌情实施。这个来自回购的 [GitHub 消息](https://github.com/jquery/jquery-browser)说明了一切:**

> “不再坚持此回购不活跃。如果需要，请使用 jQuery 迁移插件。浏览器是必需的，请重写代码，或者直接使用 navigator.userAgent。

## 那么为什么$。浏览器被移除了？

当大多数开发人员开始看到错误出现时，他们首先想到的是**“搞什么，为什么有$。浏览器被移除了？”**。让我解释一下可能的原因。因为 **$。浏览器使用 navigator.userAgent 来确定平台，它容易受到用户的欺骗或浏览器本身的误导**。尽可能完全避免特定于浏览器的代码总是最好的。[美元。support](http://api.jquery.com/jQuery.support/) 属性可用于检测对特定特性的支持，而不是依赖于 [$。浏览器](http://api.jquery.com/jQuery.browser/)。

可用的标志有:

*   webkit(从 jQuery 1.4 开始)
*   safari(已弃用)
*   歌剧
*   msie(注意，在兼容性视图中，IE8 声称是 7)
*   浏览器名

## 解决方案 1–迁移 jQuery

使用 [jQuery 迁移插件](https://code.jquery.com/jquery-migrate-1.1.1.js)到[将早期版本的 jQuery 升级到 jQuery 1.9.x](http://www.jquery4u.com/ie/migrating-earilier-versions-jquery/) 。这是迁移费。辉煌的浏览器代码:

```
 jQuery.uaMatch = function( ua ) {
        ua = ua.toLowerCase();

        var match = /(chrome)[ /]([w.]+)/.exec( ua ) ||
                /(webkit)[ /]([w.]+)/.exec( ua ) ||
                /(opera)(?:.*version|)[ /]([w.]+)/.exec( ua ) ||
                /(msie) ([w.]+)/.exec( ua ) ||
                ua.indexOf("compatible") 
```

## 解决方案 2 -使用 Modernizr

使用 [Modernizr](http://modernizr.com/) 利用特征检测、HTML5/CSS3 等...而不是基本的浏览器检测。我觉得 Modernizr 很棒！

## 解决方案 3 -使用 jQuery。支持

使用新的 [$。支持](http://api.jquery.com/jQuery.support/)利用功能&缺陷检测。jQuery 再次完成所有的艰苦工作，在浏览器上执行测试，并将结果存储在 jQuery.support 对象上(默认情况下加载每个页面)。然后，我们可以简单地查询该对象，以确定某个功能是否可用。例如，要检查不透明度支持，只需执行以下操作:

```
 if (jQuery.support.opacity)
{
   //opacity you may do...
} 
```

## 解决方案 4 -使用 JavaScript/手动检测

使用以下 JavaScript 代码片段检测浏览器和版本。Quirksmode 有一个非常广泛的 [JavaScript 浏览器/设备检测对象](http://www.quirksmode.org/js/detect.html)，这可能会很有用。

```
 /*
        Internet Explorer sniffer code to add class to body tag for IE version.
        Can be removed if your using something like Modernizr.
    */
    var ie = (function ()
    {

        var undef,
        v = 3,
            div = document.createElement('div'),
            all = div.getElementsByTagName('i');

        while (
        div.innerHTML = '',
        all[0]);

        //append class to body for use with browser support
        if (v > 4)
        {
            $('body').addClass('ie' + v);
        }

    }()); 
```

## 解决方案 5 -预防/告知

只需通知用户他们使用的 jQuery 版本不支持$。浏览器功能。可能不会推荐这个解决方案，因为它对可用性没有任何帮助，但可以用来阻止某些插件。我建议使用开发者版本的[迁移插件](https://github.com/jquery/jquery-migrate/)，它可以打开信息调试。

```
 var undef;
    if ($.browser == undef) {
      message = [];
      message.push("WARNING: you appear to be using a newer version of jquery which does not support the $.browser variable.");
      message.push("The jQuery iframe auto height plugin relies heavly on the $.browser features.");
      message.push("Install jquery-browser: https://raw.github.com/jquery/jquery-browser/master/src/jquery.browser.js");
      alert(message.join("n"));
      return $;
    } 
```

来源:[https://raw . github . com/house 9/jquery-iframe-auto-height/master/release/jquery . iframe-auto-height . plugin . 1 . 9 . 1 . js](https://raw.github.com/house9/jquery-iframe-auto-height/master/release/jquery.iframe-auto-height.plugin.1.9.1.js)

一如既往，欢迎提出意见、建议和改进。

## 分享这篇文章