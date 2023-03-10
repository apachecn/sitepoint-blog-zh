# 升级到新的 WordPress 颜色选择器

> 原文：<https://www.sitepoint.com/upgrading-to-the-new-wordpress-color-picker/>

如果你像我一样，一直在使用旧的 [Farbtastic](https://acko.net/blog/farbtastic-jquery-color-picker-plug-in/) 颜色选择器作为 WordPress 插件，我肯定你渴望在 WordPress 3.5 发布时使用新的和改进的颜色选择器。

升级你的插件来使用新的颜色选择器相当简单。困难的是知道如何容易地回到旧版本 WordPress 的夸张的颜色选择器。

在下面的截屏和文章中，我们将详细讨论如何做到这一点，并在 Github 上提供一个完整的插件示例来帮助您入门。

## 电影剧本

[https://www.screenr.com/embed/DZK7](https://www.screenr.com/embed/DZK7)

## 实现 WP 颜色选择器的步骤

在了解 PHP 和 JavaScript 的本质之前，让我们看一下实现新的颜色选择器的步骤。

1.  确定服务器上安装的 WordPress 版本。
2.  如果 WordPress 的安装版本是 3.5 或更高版本，请将新颜色选择器的样式和脚本加入队列。
3.  如果 WordPress 版本低于 3.5，将过时的 Farbtastic 颜色选择器的样式和脚本加入队列。
4.  在您的自定义 JavaScript 文件中，检查新的颜色选择器函数是否存在，如果存在，在您的文本输入字段中初始化颜色选择器。
5.  如果新的拾色器不存在，那么初始化旧的拾色器。

很简单，对吧？现在让我们看看插件中使用的 PHP。

## 使用 PHP 将正确的样式和脚本加入队列

如果你刚刚开始为 WordPress 创建插件，你需要对如何创建设置页面以及如何在这些页面中恰当地调用你的样式和脚本有一个坚实的理解。

如何为你的插件创建一个设置页面不在本文的讨论范围内，但是如果你需要更多关于如何处理插件设置的信息，请查看 Jeffrey Way 的[在 WordPress](https://www.sitepoint.com/wordpress-options-panel/) 中创建自定义选项页面。

出于我们的目的，您需要知道我们管理页面上的最终输入字段和必要的 div 如下所示:

```
<input id="color" name="color_options[color]" type="text" value="" />
<div id="colorpicker"></div>
```

[观生](https://gist.github.com/raw/4489186/707512a476b517bfd3e0dcada20fe818b2fb7323/gistfile1.html)【gistfile1.html】T2

一旦您通过`add_action()`恰当地挂钩了您的函数或方法，您将使用 PHP 来调用您的样式和脚本:

```
< ?php
function add_styles_scripts(){
    //Access the global $wp_version variable to see which version of WordPress is installed.
    global $wp_version;

    //If the WordPress version is greater than or equal to 3.5, then load the new WordPress color picker.

    if ( 3.5 <= $wp_version ){ //Both the necessary css and javascript have been registered already by WordPress, so all we have to do is load them with their handle. wp_enqueue_style( 'wp-color-picker' ); wp_enqueue_script( 'wp-color-picker' ); } //If the WordPress version is less than 3.5 load the older farbtasic color picker. else { //As with wp-color-picker the necessary css and javascript have been registered already by WordPress, so all we have to do is load them with their handle. wp_enqueue_style( 'farbtastic' ); wp_enqueue_script( 'farbtastic' ); } //Load our custom javascript file wp_enqueue_script( 'wp-color-picker-settings', plugin_dir_url( __FILE__ ) . 'js/settings.js' ); } ?>
```

[观生](https://gist.github.com/raw/4489182/8e3fd536587a2195af1bf0cb0a0e036c489f6cf9/gistfile1.php)【gistfile1.php】T2

在方法的顶部，我们使用了全局变量`$wp_version`,它为我们提供了 WordPress 安装的版本号。我们简单地检查我们是否使用 3.5 或更高版本，如果是，使用预先注册的样式和脚本加载新的颜色选择器。否则，我们加载旧的 Farbtastic 样式和脚本。

此时，我们已经加载了正确的文件，但是我们需要调用文本输入字段上的脚本。我们通过自己的 JavaScript 文件做到这一点。

## 使用 JavaScript 初始化颜色选择器

你可以在上面的`add_styles_scripts()`方法的底部看到，我们加载了一个 JavaScript 文件。

从短期来看，更简单的方法是拥有两个 Javascript 文件，一个用于新的颜色选择器，另一个用于 Farbtastic。有了这个插件就好了，但是如果我们每次需要修改 JavaScript 时都要更新两个文件，那么管理起来会非常低效。

那么，我们如何只设置一个 JavaScript 文件来初始化正确的颜色选择器呢？

```
//Set up the color pickers to work with our text input field

jQuery( document ).ready(function(){

    "use strict";

    //This if statement checks if the color picker widget exists within jQuery UI

    //If it does exist then we initialize the WordPress color picker on our text input field

    if( typeof jQuery.wp === 'object' && typeof jQuery.wp.wpColorPicker === 'function' ){

        jQuery( '#color' ).wpColorPicker();

    }

    else {

        //We use farbtastic if the WordPress color picker widget doesn't exist

        jQuery( '#colorpicker' ).farbtastic( '#color' );

    }

});
```

[查看原始数据](https://gist.github.com/raw/4489191/91c18674d589a5f0dd62f19831831f3925258dac/gistfile1.js) [gistfile1.js](https://gist.github.com/4489191#file-gistfile1-js)

if 语句首先确定 jQuery.wp 是否是一个对象，如果是，确保 wpColorPicker 是该对象的一个函数，从而检查新的颜色选择器是否作为一个小部件存在于 jQuery UI 中。如果这两个条件都成立，我们称之为新的颜色选择器。如果没有，我们加载 Farbtastic。

仅此而已。我们使用了新的 WP 颜色选择器，同时仍然为旧版本的 WordPress 提供了一个可用的后备。

如果你正在寻找整个插件的例子，你可以在 Github 上找到。

## 分享这篇文章