# 使用绝对路径更新具有特定 src(相对路径)的图像

> 原文：<https://www.sitepoint.com/relative-path-absolute-path/>

用域名更新图像的 jQuery 代码片段，将域名从相对路径转换为绝对路径。当使用相同的静态内容跨域测试或从外部域提取图像时，有时会很有用。

[![replace-url-absolutw](img/b90d70a2d3f4af518cde71ba95e7d655.png)](https://jsfiddle.net/BK9Zp/)

```
(function ($) {
    $(document).ready(function () {
        $('img').each(function (i, v) {
            var $el = $(this),
                s = $el.attr('src'),
                sRx = /[/rimg/.*]+/igm;
            console.log(s);
console.log(s.test(sRx));
            if (s.test(sRx)) {
                console.log('match');
                s = 'http://splash.abc.net.au' + s;
                $el.attr('src', s);
            }
        });
    });
})(jQuery);
```

[jsfiddle.net/BK9Zp/](https://jsfiddle.net/BK9Zp/)

JSfiddly lolz。

本·阿尔曼有一个很棒的项目，它拥有这方面的所有功能，请查看:
[https://raw . github . com/cowboy/jquery-URL internal/master/jquery . ba-URL internal . js](https://raw.github.com/cowboy/jquery-urlinternal/master/jquery.ba-urlinternal.js)

## 分享这篇文章