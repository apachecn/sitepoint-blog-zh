# 如何从头开始构建 WordPress 主题:基础

> 原文：<https://www.sitepoint.com/build-wordpress-theme-from-scratch-basics/>

在本教程中，我们将深入探索 WordPress 主题文件结构，并学习如何从头开始创建一个基本的 WordPress 主题。

在本系列的第一部分中，我们介绍了 WordPress 主题，以及与 WordPress 主题开发相关的基本术语。我们讨论了*模板*、*片段*、*模板层次结构*、 *WordPress 文章类型*、 *style.css* 样式表、WordPress *过滤器*和*动作*钩子、 *WordPress 循环*、*条件标签*，并且我们简单地看了一下典型的简单 WordPress 主题文件结构。

## 创造最简单的主题

我们要做的第一件事是安装一个插件，使我们能够批量创建 WordPress 帖子和其他内容。这样，我们将能够快速填充我们的开发网站，而不会浪费太多时间。一个服务于这个目的的插件是由[古斯塔沃·博尔多尼](http://bordoni.me/)开发的 [FakerPress](https://wordpress.org/plugins/fakerpress/) ，可以在 WordPress 插件库中找到。

<video class="wp-video-shortcode" id="video-170160-1" width="1280" height="720" preload="metadata" controls=""><source type="video/mp4" src="https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542590261faker.mp4?_=1">[https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542590261faker.mp4](https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542590261faker.mp4)</video>

我们通过 WP-CLI 快速安装并激活插件。

现在，当我们登录到管理仪表板时，我们会看到 FakerPress 已安装，我们可以批量创建各种内容，包括我们拥有的任何自定义帖子类型。

![FakerPress is installed](img/aed529ec201f238b3d1286a33b3cf53e.png)

现在，使用这个插件，我们将创建一些假的内容。这是结果，使用默认的*第二十七个* WordPress 主题:

![The TwentySeventeen theme with placeholder content](img/19064258a76cc7a1bced18c64abbe166.png)

现在，我们快速进入并设置一个最简单的主题，它由一个包罗万象的`index.php`文件和`style.css`组成，我们需要它们让 WordPress 模板系统识别主题:

```
/*
Theme Name: Botega Simple Theme
Theme URI: https://botega.co.uk
Author: Tonino Jankov
Author URI: https://botega.co.uk
Description: Basic WordPress theme for Sitepoint theme building tutorial
Text Domain: bsimple
Version: 1.0.0
License: GNU General Public License v2 or later
*/ 
```

这是`style.css`，目前只包含元 CSS 注释。这些注释是必需的。

这是`index.php`文件。现在它将捕获所有的请求:

```
<?php
/**
 *
 * @package Botega_Scratch_Theme
 */
?>

<!DOCTYPE html>
<html <?php language_attributes(); ?>>
<head>
    <title><?php bloginfo('name'); ?></title>
    <link rel="stylesheet" href="<?php bloginfo('stylesheet_url'); ?>">
    <?php wp_head(); ?>
</head>
<body>

      <header>
         <h1><?php bloginfo('name'); ?></h1>
         <h3><?php bloginfo('description'); ?></h3>
      </header>

        <?php
        if ( have_posts() ) :
            /* Start the Loop */
            while ( have_posts() ) :
                the_post();
            endwhile;
        endif;
        ?>

</body> 
```

我们现在上传并激活我们的最小主题。我使用 WP-CLI 激活它:

<video class="wp-video-shortcode" id="video-170160-2" width="1280" height="720" preload="metadata" controls=""><source type="video/mp4" src="https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542592865activating.mp4?_=2">[https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542592865activating.mp4](https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542592865activating.mp4)</video>

主题现在对 WordPress 可见，并且是活动的:

![The theme is now activated](img/cef5e01dd5badebfe4a437575c221eb6.png)

我们没有提供截图，所以后端的显示是基本的。

如果我们现在在浏览器中访问我们的网站，我们将看到以下内容:

![The current home page](img/3b2751b37039e8e0d767ba9f64426867.png)

显然，我们有工作要做。

如果我们查看主页的源代码，我们会看到`wp_head()`函数在`<head>`中输出了很多默认的 WordPress 标签，比如 CSS、JavaScript、`link`和`meta`标签。

`bloginfo()`功能是[，用于输出网站信息](https://developer.wordpress.org/reference/functions/bloginfo/)。

我们的主页是空的，因为我们没有在循环中输出任何内容 WordPress 在所有模板中使用这种模式来输出内容。

关于循环的[抄本页面深入讨论了它的细节。循环的典型结构——基于 PHP *和*控制结构——如下所示:](https://codex.wordpress.org/The_Loop)

```
<?php
if ( have_posts() ) {
    while ( have_posts() ) {
        the_post();
        //
        // Post Content here
        //
    } // end while
} // end if
?> 
```

我们需要用内容填充这个`while`循环——或者用输出内容的 WordPress 标签。

如果我们改变我们的循环，添加`the_title()`、`the_excerpt()`，我们添加 HTML 标记和`the_ID()`，看起来像这样:

```
 <?php
    if ( have_posts() ) : while ( have_posts() ): the_post(); ?>

    <div id="post-<?php the_ID(); ?>">
        <h2><?php the_title(); ?></h2>
        <div class="post-excerpt"><?php the_excerpt(); ?></div>
    </div>

    <?php endwhile;
    endif;
    ?> 
```

我们现在将在主页上获得一个帖子列表，没有应用任何样式:

![Unstyled output](img/1664040dec43372193849fdc4916ffca.png)

默认情况下，WordPress 显示一个博客页面——所有博客文章的存档页面。

如果我们现在访问单个帖子的 URL —类似于`http://my-website.com/2018/11/14/sapiente-ad-facilis-quo-repellat-quos/` —我们会看到这样的内容:

![Our current single post layout](img/8eea764c836a24d1d1a2b5a9067fb279.png)

我们的*循环*，虽然非常粗糙，但实际上是有效的。

## 将我们的主题组织成文件并应用引导标记

我们现在将实现[部分](https://developer.wordpress.org/themes/template-files-section/partial-and-miscellaneous-template-files/)，像`header.php`和`footer.php`以及各种专门的模板，都使用 [Twitter Bootstrap](https://getbootstrap.com/) 标记，这样我们可以更容易地对其进行样式化。

从`index.php`开始，我们用`get_header()`和`get_footer()`函数替换循环前后的所有内容:

```
<?php
/**
 *
 * @package Botega_Scratch_Theme
 */

get_header(); ?>

    <?php
    if ( have_posts() ) : while ( have_posts() ): the_post(); ?>

    <div id="post-<?php the_ID(); ?>">
        <h2><?php the_title(); ?></h2>
        <div class="post-excerpt"><?php the_excerpt(); ?></div>
    </div>

    <?php endwhile;
    endif;
    ?>

<?php get_footer(); ?> 
```

这意味着我们需要在我们提到的部分中提供所有的内容。

根据我们所说的——我们将使用 Twitter Bootstrap 主题——我们的`header.php`文件将如下所示:

```
<?php
/**
 * The header for our theme.
 *
 * @package Botega_Scratch_Theme
 *
 */
?>
<!DOCTYPE html>
<html>
<head>
<meta charset="<?php bloginfo( 'charset' ); ?>">
<meta name="viewport" content="width=device-width, initial-scale=1">

<?php wp_head(); ?>
</head>

<body <?php body_class(); ?>>

  <nav class="navbar navbar-default navbar-custom navbar-fixed-top">
    <div class="container-fluid">
      <div class="navbar-header page-scroll">
              <a href="<?php echo esc_url( home_url( '/' ) ); ?>" rel="home" class="navbar-brand"><?php bloginfo( 'name' ); ?></a>
      </div>

      <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
          <ul class="nav navbar-nav navbar-right">
          <?php wp_nav_menu( array( 'theme_location' => 'primary', 'items_wrap' => '%3$s' ) ); ?>
          </ul>
      </div>
    </div>
  </nav>

  <div class="container">
      <div class="row"> 
```

我们的`footer.php`文件将如下所示:

```
<?php
/**
 * Footer template partial
 *
 * @package Botega_Scratch_Theme
 *
 */
?>
     </div>
     <!-- /.row -->
  </div>
  <!-- /.container -->

  <!-- Footer -->
  <footer>
    <div class="container">
      <div class="row">
          <div class="col-lg-8 col-md-10 mx-auto">
          </div>
      </div><!-- /.row -->
    </div><!-- /.container -->
  </footer><!-- /footer -->

<?php wp_footer(); ?>

</body>
</html> 
```

我们在 HTML 标签中使用了 Bootstrap 类，以及 [wp_head()](https://codex.wordpress.org/Function_Reference/wp_head) 和 [wp_footer()](https://codex.wordpress.org/Function_Reference/wp_footer) fire `wp_head`和`wp_footer`动作钩子。

接下来我们要做的是包含来自 startbootstrap.com 的 CSS 和 JavaScript 模板，它带有麻省理工学院的许可，所以我们可以免费使用。这样，我们的主题将带有预定义的样式、响应性，并且我们仍然能够进一步对其进行样式化。

## functions.php

`functions.php`是任何严肃的 WordPress 主题都有的文件。这是一个充当穷人插件存档的文件。它允许我们在主题中包含任何自定义功能。

我们将首先使用这个文件来包含 bootstrap 和我们的 Bootstrap 主题的样式和脚本:

```
function bsimple_scripts() {
    wp_enqueue_style( 'bsimple-style', get_stylesheet_uri() );
    wp_enqueue_style( 'bsimple-clean', get_template_directory_uri() . '/css/clean-blog.min.css' );
    wp_enqueue_style( 'bsimple-bootstrap', get_template_directory_uri() . '/css/bootstrap.min.css' );
    wp_enqueue_style( 'bsimple-fontawesome', get_template_directory_uri() . '/css/fa-all.min.css' );
    wp_enqueue_style( 'bsimple-font1', "https://fonts.googleapis.com/css?family=Lora:400,700,400italic,700italic" );
    wp_enqueue_style( 'bsimple-font2', "https://fonts.googleapis.com/css?family=Open+Sans:300italic,400italic,600italic,700italic,800italic,400,300,600,700,800" );

    wp_enqueue_script( 'bsimple-jq', get_template_directory_uri() . '/js/jquery.min.js');
    wp_enqueue_script( 'bsimple-bootstrap', get_template_directory_uri() . '/js/bootstrap.bundle.min.js');
    wp_enqueue_script( 'bsimple-clean', get_template_directory_uri() . '/js/clean-blog.min.js');
}    

add_action( 'wp_enqueue_scripts', 'bsimple_scripts' ); 
```

这是一种在主题中包含脚本和样式的 WordPress 惯用方式。它允许我们指定脚本入队的位置(页眉对页脚)和入队的优先级。我们甚至可以指定每个特定资源对另一个资源的依赖性。这将确保资源以正确的顺序加载。

我们在这里使用了`wp_enqueue_scripts`动作钩子。我们可以在《T2 法典》中了解更多。(我们在[之前的文章](https://www.sitepoint.com/build-wordpress-theme-from-scratch-first-steps/)中讨论过动作钩子。)

在我们的定制`bsimple_scripts()`函数中——我们将其与`wp_enqueue_scripts`动作挂钩——我们使用两个 WordPress 函数来加载我们的脚本和样式— [wp_enqueue_script()](https://developer.wordpress.org/reference/functions/wp_enqueue_script/) 和 [wp_enqueue_style()](https://developer.wordpress.org/reference/functions/wp_enqueue_style/) 。这些函数的参数——如其链接参考页面中所指定的——允许我们充分利用我们提到的灵活性。

我们可以看到，我们正在从互联网(谷歌字体)和我们的主题文件夹加载样式。因此，我们在主题文件夹中创建`css`、`js`和`webfonts`目录，并复制我们的 Bootstrap 主题的 CSS、JavaScript 文件和 FontAwesome 图标字体文件。

我们还将我们的`index.php`文件复制到`archive.php`、`page.php`和`single.php`文件，我们将对它们进行修改。

现在我们的主题文件结构看起来像这样:

![Our current theme file structure](img/0f77e6d6d5b867221573c8c066091b59.png)

## 调整标记

如果我们现在访问我们的主页，我们会在顶部看到菜单——尽管它和页面仍然很乱——因为我们标题中的下面一行仍然输出包装在`div`和它自己的`ul`标签中的菜单，所以它不受我们的引导样式的影响:

```
<?php wp_nav_menu( array( 'theme_location' => 'primary', 'items_wrap' => '%3$s' ) ); ?> 
```

为了解决这个问题，我们首先需要进入我们的`wp-admin`仪表板，在定制器中创建一个新菜单。我们将把它命名为*顶层菜单*。

<video class="wp-video-shortcode" id="video-170160-3" width="1280" height="720" preload="metadata" controls=""><source type="video/mp4" src="https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542595661customizer.mp4?_=3">[https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542595661customizer.mp4](https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542595661customizer.mp4)</video>

完成这些后，我们将转到我们的`header.php`文件，删除这些行:

```
<ul class="nav navbar-nav navbar-right">
<?php wp_nav_menu( array( 'theme_location' => 'primary', 'items_wrap' => '%3$s' ) ); ?>
</ul> 
```

在它们的位置上，我们放上这些线:

```
<ul class="navbar-nav ml-auto">
    <?php wp_nav_menu(array(
      'menu' => 'Top Menu',
      'items_wrap'=>'%3$s',
      'container' => false,
      )); ?>
</ul> 
```

这将为我们移除`div`标签和重复的`ul`标签，但是我们仍然需要将`nav-item`和`nav-link`应用到我们的菜单项(分别应用到`li`和`a`标签)。我们将如何着手此事？`wp_nav_menu` [不为此](https://developer.wordpress.org/reference/functions/wp_nav_menu/)提供自变量。我们将使用`nav_menu_link_attributes`和`nav_menu_css_class`过滤钩。我们将此放入我们的`functions.php`文件:

```
function add_menu_link_class( $atts, $item, $args ) {
    if($args->link_class) {
        $atts['class'] = $args->link_class;
    }
    return $atts;
}
add_filter( 'nav_menu_link_attributes', 'add_menu_link_class', 1, 3 );

function add_menu_list_item_class($classes, $item, $args) {
    if($args->list_item_class) {
        $classes[] = $args->list_item_class;
    }
    return $classes;
}
add_filter('nav_menu_css_class', 'add_menu_list_item_class', 1, 3); 
```

现在我们可以在我们的`header.php`中指定新的属性:

```
<ul class="navbar-nav ml-auto">
      <?php wp_nav_menu(array(
        'menu' => 'Top Menu',
        'items_wrap'=>'%3$s',
        'container' => false,
        'list_item_class' => "nav-item",
        'link_class' => "nav-link",
        )); ?>
</ul> 
```

现在我们的顶部菜单链接可以利用已经在 Bootstrap 主题的 CSS 中定义的样式。

## 动态标题

为了能够使用动态标题——也就是说，首页、其他选定页面或归档的不同标题——我们将在我们的`functions.php`文件中定义一个`dynamic_header()`函数，其中我们将根据访问者加载的页面输出我们的标题标记。

所以现在我们的`header.php`文件将会这样结束:

```
</nav>

<?php dynamic_header(); ?>

<div class="container">
    <div class="row"> 
```

我们还将这样定义函数:

```
if(!function_exists('dynamic_header')){

function dynamic_header(){

    global $post;

    ?>

    <?php if (is_front_page()){ ?>

        <header class="masthead" style="background:#ccc;">
            <div class="overlay"></div>
            <div class="container">
                <div class="row">
                    <div class="col-lg-8 col-md-10 mx-auto">
                        <div class="site-heading">
                        <h1 class="site-title"><a href="<?php echo esc_url( home_url( '/' ) ); ?>" rel="home"><?php bloginfo( 'name' ); ?></a></h1>
                        <span class="subheading"><?php get_bloginfo( 'description', 'display' );?></span>
                        </div>
                    </div>
                </div>
            </div>
        </header>

    <?php } else if (is_home()){ ?>

        <header class="masthead" style="background:#ccc;">
            <div class="overlay"></div>
            <div class="container">
                <div class="row">
                    <div class="col-lg-8 col-md-10 mx-auto">
                        <div class="site-heading">
                        <h1 class="site-title"><a href="<?php echo esc_url( home_url( '/' ) ); ?>" rel="home"><?php bloginfo( 'name' ); ?></a></h1>
                        <span class="subheading"><?php get_bloginfo( 'description', 'display' );?></span>
                        </div>
                    </div>
                </div>
            </div>
        </header>

    <?php } else if (is_page()){ ?>

        <header class="masthead" style="background:#ccc;">
            <div class="overlay"></div>
            <div class="container">
                <div class="row">
                    <div class="col-lg-8 col-md-10 mx-auto">
                        <div class="site-heading">
                        <h1 class="site-title"><a href="<?php echo esc_url( home_url( '/' ) ); ?>" rel="home"><?php bloginfo( 'name' ); ?></a></h1>
                        <span class="subheading"><?php get_bloginfo( 'description', 'display' );?></span>
                        </div>
                    </div>
                </div>
            </div>
        </header>

    <?php } else if (is_single()){ ?>

        <header class="masthead" style="background:#ccc;">
            <div class="overlay"></div>
            <div class="container">
                <div class="row">
                    <div class="col-lg-8 col-md-10 mx-auto">
                        <div class="site-heading">
                        <h1 class="site-title"><a href="<?php echo esc_url( home_url( '/' ) ); ?>" rel="home"><?php bloginfo( 'name' ); ?></a></h1>
                        <span class="subheading"><?php get_bloginfo( 'description', 'display' );?></span>
                        </div>
                    </div>
                </div>
            </div>
        </header>

    <?php } else { ?>

        <header class="masthead" style="background:#ccc;">
            <div class="overlay"></div>
            <div class="container">
                <div class="row">
                    <div class="col-lg-8 col-md-10 mx-auto">
                        <div class="site-heading">
                        <h1 class="site-title"><a href="<?php echo esc_url( home_url( '/' ) ); ?>" rel="home"><?php bloginfo( 'name' ); ?></a></h1>
                        <span class="subheading"><?php get_bloginfo( 'description', 'display' );?></span>
                        </div>
                    </div>
                </div>
            </div>
        </header>

    <?php }
}

} 
```

为了能够使用所有当前的 URL 或 post 数据——就像在循环中一样——我们声明了一个`$post`变量`global`。然后，我们只需用填充标题 HTML 填充不同的页面或请求案例，这将在稍后完成。这为真正的动态标题奠定了基础。

我们需要确保我们的首页——带有动态顶部菜单——即使在用户登录时也很好看。当访问者登录时，WordPress 会显示一个*管理栏*，甚至当他们访问首页时。因为它有`position: fixed`，它覆盖了我们网站的顶部区域，覆盖了那里的任何东西，所以我们需要为我们的顶部菜单指定一个偏移量。

我们将把它添加到我们的`style.css`:

```
body.logged-in.admin-bar #mainNav {
    margin-top: 32px;
}

@media screen and (max-width: 782px) {
    body.logged-in.admin-bar #mainNav {
        margin-top: 46px;
    }
} 
```

这确保了`#mainNav`——我们的菜单容器——与顶部有足够的偏移量，所以当用户登录时它不会被覆盖。在这些情况下，WordPress 为`body`添加了`logged-in`和`admin-bar`类，所以我们可以很容易地锁定它。

我们可以看到，我们在 CSS 中处理了两种情况——一种是默认情况，另一种是小屏幕情况。这是因为 WordPress 在移动设备上输出更宽的管理栏，所以我们需要提供 46px 的偏移量。

在移动设备上，我们现在应该有一个响应迅速的 JavaScript 支持的下拉菜单:

![Our dropdown on mobile](img/7e844aecd9ecc114b8f1dc8e0b68a3a4.png)

## 结论

在从头开始创建 WordPress 主题的第二部分中，我们创建了一个非常基本的 WordPress 主题，并在其中加入了引导样式和脚本。我们调整了菜单输出以适应我们预定义的样式。我们还将页眉和页脚输出分成各自的部分。

`functions.php`文件——主题开发中的一个关键文件——是我们引入并利用的另一个主题。标题输出被分离到它自己的函数中，该函数将使用页面访问的细节和站点所有者定义的变量来确定最终输出。

在指南的第三部分，我们将完成特定模板的构建，为我们的主题功能和部分提供更好的结构，并完成我们网站的样式。

* * *

这个系列中有三篇文章是关于从头开始构建 WordPress 主题的:

*   [理解主题的结构](https://www.sitepoint.com/build-wordpress-theme-from-scratch-first-steps/)
*   **主题基础知识**
*   [提炼主题](https://www.sitepoint.com/build-wordpress-theme-from-scratch-final)

## 分享这篇文章