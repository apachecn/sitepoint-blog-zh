# 如何定制 SitePoint WordPress 基本主题

> 原文：<https://www.sitepoint.com/customize-sitepoint-wordpress-base-theme/>

[SitePoint 最近发布了新的 WordPress 基础主题](https://www.sitepoint.com/introducing-sitepoint-base-theme-for-wordpress/)。它非常容易定制，你可以快速简单地构建高级 WordPress 主题来满足你的需求。

[SitePoint 基本主题](https://www.sitepoint.com/beginners-guide-to-sitepoint-base-theme-for-wordpress/)给了你编辑和修改基本主题的全部权限。下载这个主题后，我对它进行了定制，为我的 WordPress 博客建立了一个主题。*如果你想了解更多关于自定义主题开发的知识，请查看 SitePoint 的课程[构建自定义主题](https://www.sitepoint.com/premium/courses/wordpress-theme-development-2931)！*

有两种不同的方法来自定义这个主题:

*   创建一个子主题。
*   使用 SitePoint 基本主题来启动您自己的精彩主题。

在本教程中，我将解释，如何创建一个子主题和修改网站点基础主题。

## 入门指南

我更喜欢使用本地 WordPress 安装来创建和测试新的 WordPress 主题。本地开发环境可以使您的开发工作流程更快、更简单。

我用的是 WAMP 视窗服务器。你可以使用任何你选择的工具来建立本地 WordPress 开发环境。[MAC 版的 MAMP](https://www.mamp.info/) ，Windows 版的 [WAMP](http://www.wampserver.com/en) ， [XAMPP](https://www.apachefriends.org/) 和 [Bitnami](https://bitnami.com/stack/wordpress) 都是跨平台工具，可以在本地安装 WordPress。

*   [用](http://justlearnwp.com/install-wordpress-on-wamp-server-windows-8/) [WAMP 服务器](http://www.wampserver.com/en)在本地安装 WordPress】
*   下载 [Sitepoint 基础主题](https://www.sitepoint.com/premium/themes/sitepoint-base-theme)
*   下载代码编辑器。我更喜欢 [SublimeText](https://www.sublimetext.com) 。

设置好本地 WordPress 安装后，运行你的本地 WordPress 站点，安装并激活 SitePoint 基本主题。

打开你的主题文件夹，新建一个文件夹，命名为 **sitepoint-base-child** 或者你喜欢的名字。在这个新的子文件夹中创建两个文件。

*   style.css
*   functions.php

如果您决定在 SitePoint 基本主题中使用子主题，那么就没有必要使用 CSS `@import`或者甚至将父样式表加入队列。SitePoint 基本主题会自动将父样式表加入队列。

打开`style.css`文件，包括以下信息:

```
/*
 Theme Name:   SitePoint Base Child
 Theme URI:    http://www.yoursite.com/
 Description:  A sitepoint-base child theme by Tahir Taous
 Author:       Your Name
 Author URI:   http://www.yoursite.com
 Template:     sitepoint-base
 Text Domain:  sitepoint-base-child
 Version:      1.0.0
*/
```

保存您的更改。

登录你当地的 WordPress 网站，进入`Appearance > Themes`。你应该看到 SitePoint 基本主题，没有任何截图。激活 SitePoint 基本子主题。

您的儿童主题已准备好。现在，我们可以添加我们自己的定制风格，以适应我们自己的喜好。

对我来说，我想改变链接的默认颜色。链接的默认颜色是`#3a3a3a`。

打开`style.css`文件，添加以下样式来改变链接的默认颜色。我也想强调所有访问过的链接。

```
a {color: #00BCD4;}
a:visited {text-decoration: underline;}
```

你可以在下面的截图中看到链接的底色。

![links color sitepoint base](img/e5eab663e38452b6bfcc0b3a86e77e63.png)

### 网站标题和描述

默认情况下，SitePoint 基本主题不显示你的标语，网站标题也很大。让我们更改网站标题的样式，并添加对网站描述的支持。从 SitePoint 基础主题文件夹中复制`header.php`文件并粘贴到你的子主题中。

在第 45 行，你可以找到下面的代码，它显示了我们的网站标题和徽标。如果您上传您的[自定义徽标](https://www.sitepoint.com/wordpress-custom-logo-api/)，网站标题文本将被隐藏。

```
<div class="grid-40 tablet-grid-40 site-title">
  <?php sitepointbase_the_custom_logo() ?>
</div> <!-- /.grid-40.site-title -->
```

我们现在要用下面的新代码替换上面的代码。

```
<div class="grid-40 tablet-grid-40">
  <div class="site-title">
    <?php sitepointbase_the_custom_logo() ?>
  </div>
  <p class="site-description">
    <?php bloginfo('description') ?>
  </p>
</div> <!-- /.grid-40.site-title -->
```

转到外观>自定义>网站标识，并添加您的网站标语。

现在，如果你上传你的自定义标志，只有网站标题将被隐藏，而不是标语文本。

我还想改变网站标题的字体大小(64px = 4rem)。你可以使用 Chrome 开发者工具来检查和查找任何元素的所有相关样式。在您的 SitePoint 基本主题的`style.css`中，您可以找到以下样式。

```
.site-title a {
    color: #3a3a3a;
    font-size: 64px;
    font-size: 4rem;
    text-decoration: none;
}
```

网站标题链接的尺寸也有点大，所以你可以减小尺寸。只需在子主题的`style.css`文件中声明你的新样式。您还可以更改网站标题的颜色。

```
.site-title a {
    color: #00BCD4;
    font-size: 32px;
    font-size: 2rem;
}
```

![site title](img/b93a51b9b10e5d09b59df9106744b41e.png)

在上面的截图中，你可以看到网站标题的新颜色和更小的字体。

### 新的谷歌字体

我在我的个人博客和 WordPress 博客上使用了大量定制的 SitePoint 基础主题。SitePoint Base 是一个很好的起点，但我想做一些定制，如字体，链接的颜色和列表项之间的空白。

我想为我的儿童主题使用新的谷歌字体。你可以选择和使用任何谷歌字体。我将使用 **Patua One** 和 **Titillium Web** 。对于标题，SitePoint Base 使用“Dosis”字体，我选择了“Patua One”作为标题。

对于身体和其他内容，SitePoint 基本主题是使用'开放的 Sans '谷歌字体，但我将使用' Titillium 网络'。

在你的孩子主题中打开`functions.php`文件，添加以下代码来注册我们新的 Google web 字体(Titillium Web 和 Patua One)。

```
function sitepoint_base_child_fonts_url() {
$fonts_url = '';

/* Translators: If there are characters in your language that are not
* supported by titillium_web, translate this to 'off'. Do not translate
* into your own language.
*/
$titillium_web = _x( 'on', 'titillium_web font: on or off', 'sitepoint-base-child' );

/* Translators: If there are characters in your language that are not
* supported by Patua One, translate this to 'off'. Do not translate
* into your own language.
*/
$patua_one = _x( 'on', 'Patua One font: on or off', 'sitepoint-base-child' );

if ( 'off' !== $titillium_web || 'off' !== $patua_one ) {
$font_families = array();

if ( 'off' !== $titillium_web ) {
$font_families[] = 'Titillium Web:400,400i,600,900';
}

if ( 'off' !== $patua_one ) {
$font_families[] = 'Patua One:400';
}

$query_args = array(
'family' => urlencode( implode( '|', $font_families ) ),
'subset' => urlencode( 'latin,latin-ext' ),
);

$fonts_url = add_query_arg( $query_args, 'https://fonts.googleapis.com/css' );
}

return esc_url_raw( $fonts_url );
}

// Enqueuing on the front end
function sitepoint_base_child_scripts_styles() {
wp_enqueue_style( 'sitepoint-base-child-fonts', sitepoint_base_child_fonts_url(), array(), null );
}
add_action( 'wp_enqueue_scripts', 'sitepoint_base_child_scripts_styles' );
```

接下来，我们需要在子主题`style.css`文件中添加新的 Google 字体。在你的孩子主题中打开`style.css` fine，粘贴以下代码。

```
body, dt, b, strong, strong em, b em, em,
.wp-caption .wp-caption-text,
.gallery-caption,
.entry-caption,
blockquote p, blockquote cite,
.menu-toggle, button, input,
.archive-title,
.page-title,
.entry-content th,
.comment-content th,
.comments-link,
.entry-header address,
.comment-content address,
.entry-header time,
.entry-header address,
.entry-content dt,
.comment-content dt,
.archive-meta,
.comments-area article header time,
form label.error,
#wp-calendar caption,
.nav-menu
{
  font-family: 'Titillium Web', sans-serif;
}

h1, h2, h3, h4, h5, h6,
article.sticky .featured-post,
h1.page-title{
font-family: 'Patua One', cursive;
}
```

现在保存你的更改并加载你的网站首页。你应该会看到你的新谷歌字体在运行。

虽然，我们的新谷歌字体正在工作，我们需要修改一些风格。`.nav-menu`的默认`font-weight`为 700。参见父主题的`style.css`文件中的第 1748 行。我们需要改变默认的`font-weight`。

打开子主题的`style.css`文件并添加以下代码。

```
.nav-menu { font-weight: normal;}
.main-small-navigation a, .main-navigation a{color: #00bcd4;}
```

默认的链接颜色主导航是`#3a3a3a`，但是我已经把它改成了`#00bcd4`。你可以在下面的截图中看到变化。

![sitepoint base main nav](img/193343aefde6acbc83bff0be326bf41d.png)

## 其他一些变化

到目前为止，我们已经改变了链接和主导航菜单链接的默认颜色，并添加了两种新的谷歌字体。在你的子主题的`style.css`文件中添加以下样式，以在下拉菜单项之间添加填充，增加小部件区域中列表项之间的边距，并在小屏幕设备上对齐站点标志行中心。

```
.main-navigation ul ul a {padding: .5rem;}

.widget li {margin: .5rem 0;}

@media only screen and (max-width: 519px) {
.site-description{text-align: center;}
}
```

![SitePoint base theme smaller changes](img/9ac3c39b2a883d088d9f81e005bf9c99.png)

你可以在上面的截图中看到这些变化。

## 下一步是什么？

在本教程中，我解释了如何使用子主题来修改 SitePoint 基本主题的默认样式，这只是一个基础教程。

你甚至可以添加新的导航菜单，自定义登录页面，新的部件区域，在主页上显示文章摘要而不是完整的文章内容，等等。你甚至可以自定义默认主题来构建你自己强大的主题。

此外，你还可以在 SitePoint 的[构建你的第一个 WordPress 主题](https://www.sitepoint.com/premium/courses/build-your-first-wordpress-theme-2953)迷你课程中学习为 WordPress 主题创建一个新的简单模板文件，你可以在下面预览:

如果您喜欢本教程，请在下面添加您的评论，让我们知道您想了解更多的内容，这样您就可以像专业人士一样学习自定义 SitePoint 基本主题。

[下载子主题文件](https://github.com/tahirtaous/wordpress-themes-plugins)

## 分享这篇文章