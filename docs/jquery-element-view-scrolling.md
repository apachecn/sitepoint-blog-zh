# 滚动时 jQuery 保持元素在视图中

> 原文：<https://www.sitepoint.com/jquery-element-view-scrolling/>

关于如何保持元素可见的快速 jQuery 代码片段。对于演示，向下滚动博客上的任何页面，并在右侧边栏广告中看到广告，当您向下滚动页面时，它们仍然在视图中。然后当你再次向上滚动时回到普通视图。

![stays-in-view](img/496822ef4da436418467492fb5ef4367.png "stays-in-view")

## 演示

向下滚动这一页，看看右边的工具条。

## 代码

```
//keep element in view
(function($)
{
    $(document).ready( function()
    {
        var elementPosTop = $('#jq4u-sidebar-ads').position().top;
        $(window).scroll(function()
        {
            var wintop = $(window).scrollTop(), docheight = $(document).height(), winheight = $(window).height();
            //if top of element is in view
            if (wintop > elementPosTop)
            {
                //always in view
                $('#jq4u-sidebar-ads').css({ "position":"fixed", "top":"10px" });
            }
            else
            {
                //reset back to normal viewing
                $('#jq4u-sidebar-ads').css({ "position":"inherit" });
            }
        });
    });
})(jQuery);
```

## 把它变成一个插件

可以很容易地做成一个 jQuery 插件。实际上，我会很快写出一个代码，并包含在下面。

```
/**
 *  jQuery keep element in view plugin.
 *
 *  @author      Sam Deering
 *  @copyright   Copyright (c) 2012 jQuery4u
 *  @license     http://jquery4u.com/license/
 *  @link        http://jquery4u.com
 *  @since       Version 1.0
 *  @notes       Plugin only works on scroll down elements.
 *
 */

(function($)
{
    $.fn.keepElementInView = function()
    {
        var elemPosTop = this.position().top;
        $(window).scroll(function()
        {
            var wintop = $(window).scrollTop(), docheight = $(document).height(), winheight = $(window).height();
            if (wintop > elemPosTop)
            {
                this.css({ "position":"fixed", "top":"10px" });
            }
            else
            {
                this.css({ "position":"inherit" });
            }
        });
        return this;
    };

    $(document).ready( function()
    {
        jQuery('#jq4u-sidebar-ads').keepElementInView();
    });
})(jQuery);
```

## 分享这篇文章