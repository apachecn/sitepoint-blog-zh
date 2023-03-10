# 使用 jQuery Mobile 创建简单的应用程序

> 原文：<https://www.sitepoint.com/simple-app-creation-jquery-mobile/>

## 介绍

jQuery 已经成功地进入了手机世界。它被称为 jQuery Mobile，非常容易使用，你只需要在你的头中包含 jQuery Mobile 文件，并在你的标记中添加一些数据属性，你就一切就绪了。所有样式都由 jQuery 和附带的 CSS 文件处理，因此您可以在几分钟内创建一个完整的移动网页。在这个例子中，我们将创建一个简单的页面，使用过渡效果加载内部链接。第一步将概述 jQuery Mobile 的一般用法，而其余步骤将简要介绍演示中使用的特定元素。

## 标题中的 jQuery 移动文件

这个直白；你只需要包含框架文件。你可以从 jQuery 移动网站下载它们，或者从他们自己的网站或 CDN 下载文件，这将减少带宽成本并保持你的网站速度。

```
<link rel="stylesheet" href="https://code.jquery.com/mobile/1.0a1/jquery.mobile-1.0a1.min.css" />
<script type="text/javascript" src="https://code.jquery.com/jquery-1.4.3.min.js"></script>
<script type="text/javascript" src="https://code.jquery.com/mobile/1.0a1/jquery.mobile-1.0a1.min.js"></script>
```

## HTML 标记属性

将数据属性标签应用到您的 HTML，其中 n 是您想要设置的特性。

```
<div data-role="page" data-theme="b">
        <div data-role="header" data-theme="b">
            <h1>My Site</h1>
        </div>
        <div data-role="content">
            <ul data-role="listview" data-inset="true" data-theme="c" data-dividertheme="a">
                <li data-role="list-divider">Transition Effects</li>
                <li><a href="effects.php?id=slide" data-transition="slide">Slide</a></li>
                <li><a href="effects.php?id=slideup" data-transition="slideup">Slide Up</a></li>
                <li><a href="effects.php?id=slidedown" data-transition="slidedown">Slide Down</a></li>
                <li><a href="effects.php?id=pop" data-transition="pop">Pop</a></li>
                <li><a href="effects.php?id=flip" data-transition="flip">Flip</a></li>
                <li><a href="effects.php?id=fade" data-transition="fade">Fade</a></li>
            </ul>
            <br /><br />
            <ul data-role="listview" data-dividertheme="e">
                <li data-role="list-divider">Seamless List (margin-less)</li>
                <li><a href="#" data-transition="slide">Example Item 1</a></li>
                <li><a href="#" data-transition="slide">Example Item 2</a></li>
                <li><a href="#" data-transition="slide">Example Item 3</a></li>
            </ul>
        </div>
        <div data-role="footer" data-position="fixed">
            <h1>&copy; Copyright Info or Site URL</h1>
        </div>
    </div>
```

数据角色元素指定应该使用哪个 DIV/块来支持页眉、页脚和内容，所有这些都在主页面包装器中。以下是本例中使用的数据属性。

*   **数据角色**–指定可以设置为页面、页眉、内容和页脚的包装元素的性质。
*   **数据位置**–指定元素应该固定在顶部还是底部呈现。
*   **数据插入**–指定元素应该在内容边距之内还是之外。
*   **数据转换**–指定加载新页面时使用哪个转换。它可以设置为滑动、向上滑动、向下滑动、弹出、翻转或渐变。
*   **数据主题**–指定元素使用的设计主题。
*   **Data-divider theme**–指定列表分隔符的主题，该主题使用与 data-theme 相同的选项。

## 添加内容/在线发布

jQuery Mobie 呈现与桌面浏览器相同的行为，你不必真的提供你的智能手机，虽然它可以帮助熨平错误。

## 解释的示例

为了方便起见，上面的例子被分成了一个页眉和页脚文件，你可以随意使用你喜欢的编码方法。

## 结论

jQuery 无疑开始成为我们今天设计世界的一部分。因此，如果有一天，即使是最小的互联网设备也能读取 jQuery，我也不会感到惊讶。

[看现场试玩](http://devgrow.com/examples/jquerymobile/)
[下载](http://devgrow.com/examples/jquerymobile/jquerymobile.zip)

## 分享这篇文章