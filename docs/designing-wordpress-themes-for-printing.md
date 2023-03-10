# 为打印设计 WordPress 主题

> 原文：<https://www.sitepoint.com/designing-wordpress-themes-for-printing/>

在本教程中，我们将看看**设计打印主题**是什么意思，以及 WordPress 主题开发者如何设计他们的打印主题。在网页浏览器中设计一个看起来很棒的主题是不够的，有些人仍然因为各种原因喜欢(或需要)打印出网页，所以我们也需要设计主题在打印时看起来很棒。

![Designing WordPress Themes for Printing  Example](img/8ba5c130a4a7e557558ccfbfe15c7003.png)

在整篇文章中，我将提供一些基本的 CSS，通过优化打印界面来帮助你的主题更上一层楼。如果你是一个主题开发者，那么你可以直接在你的主题中嵌入 CSS。如果你是一个 WordPress 用户，并且想让你当前的网站可以打印，那么你可以通过插件安装这个代码。我将在下面详细介绍所有这些步骤。

## 如何确定主题是否可以打印？

默认情况下，当用户打印一个没有准备好打印的 WordPress 网页时，样式表被剥离，页面打印时就好像没有样式表一样。它看起来像一长串信息，从你的标题、内容开始，然后是你的侧边栏，接着是页脚。不太好看。

如果一个主题在打印时只打印了网站名称、文章内容和评论，那么这个主题就可以打印了。它不应该打印小工具、广告和其他类似的元素。对于图像和锚点标签，它应该在图像和链接文本旁边显示 URL。图像应该适合打印页面的宽度。背景应该是干净的，文本应该使用标准的字体和颜色。总的来说，文本和图像应该易于阅读和查看，并且必须在页面中完美对齐。

SitePoint 是打印就绪网站的一个最好的例子。继续按下`Ctrl + P`并检查它看起来如何。

![SitePoint Print Friendly Theme Example](img/ced773beafd1c4187e15305ce3fe1852.png)

你可以看到，它删除了标题中的大部分内容，只包含页面 url 和徽标。然后直接从页面的页眉和内容开始。对于每个图片和锚标签，它会在旁边打印相应的 URL。

![Full Width Image Printing](img/9ccce439b173dbc7097f28122452ea9a.png)

这里可以看到，当实际尺寸超过页面宽度时，图像宽度适合页面宽度。

如果您计划创建一个新网站，在选择主题之前，请确保检查它是否可以打印。有很多主题看起来很棒，但设计不适合印刷。尽可能避免这些主题。

## 创建打印样式的 CSS 文件

第一步是创建一个`print.css`文件，我们将打印设计 CSS 代码放入其中。如果你是一个主题开发者，那么你可以将这个文件和 CSS 文件一起放入你的主题中。如果你是一个 WordPress 用户，你的主题还没有准备好打印，那么你需要通过一个插件将这个脚本加入队列。

主题开发者可以将这些代码直接放入主题的`functions.php`文件中

```
function prefix_print_style()
{
    wp_enqueue_style("mytheme_print_style", get_bloginfo("template_directory")."/print.css");
}

add_action("wp_enqueue_scripts", "prefix_print_style");
```

我假设您已经将`print.css`文件放在了主题目录的根目录下。

如果你已经在使用一个没有准备好打印的主题，那么你需要通过一个简单的功能插件来安装这种风格。下面是插件的代码。

```
<?php
    /*
    Plugin Name: SitePoint Print Ready
    Plugin URI: https://www.sitepoint.com
    Description: This plugin makes your theme print ready.
    License: GPL2
    */

    function prefix_print_style()
    {
        wp_enqueue_style("myplugin_print_style", plugins_url("print.css", __FILE__));
    }

    add_action("wp_enqueue_scripts", "prefix_print_style");

?>
```

同样，我还假设您在插件根目录中有`print.css`文件。

从这里开始，无论我向您展示什么代码，您都需要放入`print.css`文件中。

## @页面规则

`@page`规则可用于定义或更改打印页的尺寸、方向、页边距、孤立页、窗口和分页符。

下图在一张打印纸上标记了上述每个术语。

![Printed Sheet Example](img/dae76a6d3f2e50454f140b9ceb1586ce.png)

图片由 [mPDF](https://mpdf.github.io/) 提供

将这段代码添加到您的`print.css`文件中，以保持打印页面和内容之间的边距。

```
@page 
{
      margin: 2cm;
}
```

## @媒体印刷

媒体规则用于定义打印时 HTML 元素的 CSS 属性。打印网页时，将应用此规则中使用的所有 CSS 代码。

从现在开始，我将向你展示的所有 CSS 代码都应该遵守`@media print`规则。

我们将使用 WordPress 默认类、HTML5 语义标签和最常见的类名来设计网页的打印样式。

## 正文背景颜色、文本颜色和边距

打印页面的标准背景色是白色，字体颜色是白色。由于我们已经使用`@page`规则给上面的打印页留出了页边距，我们需要从正文中删除任何页边距。

```
@media print 
{
    body 
    { 
        background:white; 
        color:black; 
        margin:0 
    }
}
```

## 隐藏导航、页脚和侧栏

当网页被打印时，我们需要隐藏导航菜单，页脚和侧栏。我们可以通过下面的 CSS 来实现。

```
@media print
{
    nav, .nav, footer, #sidebar, aside, #footer
    {
        display: none;
    }
}
```

## 打印注释

我们还需要打印评论，但不是评论表。

```
@media print
{
    #commentform 
    {
        display:none; 
    }
}
```

## 分页符

虽然下面的代码并不适用于每种浏览器或打印机，但您可以指示它们不要将您的照片或图形“分割”成两部分，或者分割引号，或者不要在标题后分页，而是强制在标题前分页。这并不是一门完美的科学，但是如果你真的对打印出来的网页的外观很挑剔，你可能会想要使用这些。

```
@media print
{
    h1, h2, h3, h4, h5, h6 
    { 
        page-break-after:avoid; 
        page-break-inside:avoid; 
    }
    img 
    { 
        page-break-inside:avoid; 
        page-break-after:avoid;
    }
    blockquote, table, pre 
    { 
        page-break-inside:avoid; 
    }
    ul, ol, dl  
    { 
        page-break-before:avoid; 
    }
}
```

## 样式标题

您应该专门为打印设置网页标题的样式，您可以通过使用以下 CSS 来实现这一点:

```
@media print
{
    h1, h2, h3  
    {
        font-weight:bold; 
    }
    h1      
    { 
        font-size:24px; 
    }
    h2
    { 
        font-size:16px; 
        border-bottom:1px solid #ccc; 
        padding:0 0 2px 0; 
        margin:0 0 5px 0; 
    }
    h3      
    { 
        font-size:13px; 
        margin:0 0 2px 0; 
    }
}
```

## 样式段落

你应该为你的段落设计字体系列，字体大小和页边空白。

```
@media print
{
    p
    { 
        font-size:11px; 
        font-family:times new roman, serif; 
        margin:0 0 18px 0; 
    }

}
```

## 样式和格式链接

你应该特别为打印而设计你的锚标签，并且在链接旁边的括号中显示`href`值。

```
@media print
{
    p
    { 
        font-size:11px; 
        font-family:times new roman, serif; 
        margin:0 0 18px 0; 
    }

    p a:after 
    {
        content: " (http://yourdomain.com/" attr(href) ")";
        font-size: 80%;
    }

    p a[href^="/"]:after 
    {
        content: " (http://yourdomain.com" attr(href) ")";
    }

    p a[href^="http://"]:after, p a[href^="https://"]:after 
    {
        content: " (" attr(href) ")";
    }

    p a[href^="#"]:after 
    {
        display: none;
    }
}
```

## 造型图像

我们需要确保图像宽度不会超出打印页面宽度，我们还需要在它旁边显示图像 URL。

```
@media print
{
    img 
    {
        max-width: 100%;
    }
    p img:after 
    {
        content: " (http://yourdomain.com/" attr(src) ")";
    }

    p img[href^="/"]:after 
    {
        content: " (http://yourdomain.com" attr(src) ")";
    }

    p img[href^="http://"]:after, p img[href^="https://"]:after 
    {
        content: " (" attr(src) ")";
    }
}
```

## 进一步造型

如果你是一个主题开发者，那么你应该已经知道其他类包含了你的主题中不必要的部分。将它们添加到`print.css`文件中以隐藏它们或重新设计它们的样式。

如果你正在构建一个插件，那么使用你的浏览器专用开发工具检查你的主题，找到类名，然后将它们添加到`print.css`文件中隐藏它们或者重新设计它们。

## 完整的 print.css 文件

如果您一直在跟进，但是还没有创建示例，不要担心。这里是`print.css`文件中的完整代码。

```
@page 
{
      margin: 2cm;
}

@media print
{
    body 
    { 
        background:white; 
        color:black; 
        margin:0 
    }

    nav, .nav, footer, #sidebar, aside, #footer
    {
        display: none;
    }

    #commentform 
    {
        display:none; 
    }

    h1, h2, h3, h4, h5, h6 
    { 
        page-break-after:avoid; 
        page-break-inside:avoid; 
    }
    img 
    { 
        page-break-inside:avoid; 
        page-break-after:avoid;
    }
    blockquote, table, pre 
    { 
        page-break-inside:avoid; 
    }
    ul, ol, dl  
    { 
        page-break-before:avoid; 
    }

    h1, h2, h3  
    {
        font-weight:bold; 
    }
    h1      
    { 
        font-size:24px; 
    }
    h2
    { 
        font-size:16px; 
        border-bottom:1px solid #ccc; 
        padding:0 0 2px 0; 
        margin:0 0 5px 0; 
    }
    h3      
    { 
        font-size:13px; 
        margin:0 0 2px 0; 
    }

    p
    { 
        font-size:11px; 
        font-family:times new roman, serif; 
        margin:0 0 18px 0; 
    }

    p a:after 
    {
        content: " (http://yourdomain.com/" attr(href) ")";
        font-size: 80%;
    }

    p a[href^="/"]:after 
    {
        content: " (http://yourdomain.com" attr(href) ")";
    }

    p a[href^="http://"]:after, p a[href^="https://"]:after 
    {
        content: " (" attr(href) ")";
    }

    p a[href^="#"]:after 
    {
        display: none;
    }

    img 
    {
        max-width: 100%;
    }
    p img:after 
    {
        content: " (http://yourdomain.com/" attr(src) ")";
    }

    p img[href^="/"]:after 
    {
        content: " (http://yourdomain.com" attr(src) ")";
    }

    p img[href^="http://"]:after, p img[href^="https://"]:after 
    {
        content: " (" attr(src) ")";
    }
}
```

## 进一步的改进

如果您想进一步提高页面的打印友好性，这不仅仅是隐藏或重新设计元素。有些网站添加了二维码等元素。

下面是一个将 QR 码添加到所有打印页面的示例:

```
@media print 
{
   body:after 
   {
      content: url(https://chart.googleapis.com/chart?cht=qr&chs=150x150
&chl=http://<?php the_permalink(); ?>&choe=UTF-8);
      position: absolute;
      right: 0;
      top: 0;
   }
}
```

在本文中，我介绍了设计基本博客风格主题的概念。但是，你的主题可以更高级，可以支持播放视频和音频。在这种情况下，考虑隐藏音频和视频元素，但显示资源的 URL。

请分享你设计 WordPress 打印主题的经验，我喜欢听你做了什么。

## 分享这篇文章