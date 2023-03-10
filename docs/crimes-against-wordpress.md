# 针对 WordPress 的犯罪:如果你制作了 WordPress 主题和插件，如何成为一个真正的混蛋

> 原文：<https://www.sitepoint.com/crimes-against-wordpress/>

想对你的网络开发人员和设计人员产生一种深深的蔑视感吗？然后尝试管理一个使用 SSL 的多站点 WordPress 安装。

有些人会让你相信 WordPress 生态系统是名副其实的主题和插件的香格里拉。我想摧毁这种错觉:它不是。如果你曾经管理过一两个 WordPress 站点，你就会明白我说的 WordPress 主题和插件可能是兼容性问题的迷宫和糟糕编程实践的泥沼的意思；充满空洞的承诺。这篇文章是我对让世界变得更美好的小小贡献；让我的生活有意义；减轻痛苦。

这是针对 WordPress 的犯罪清单。我在主题和插件中发现了所有这些可疑的做法，让我的生活变成了人间地狱。

## 包括您自己的过期版本的 jQuery

我在一个主题中发现了这一点:

```
$my_jquery = get_bloginfo('template_url'). "/js/jquery.old.version.js";
wp_deregister_script( 'jquery' );
wp_register_script( 'jquery', $my_jquery, false, '' );
wp_enqueue_script('jquery');
```

WordPress 附带了 jQuery 请确保你的插件或主题与 WordPress 包含的 jQuery 版本兼容。永远不要在你的插件或主题中包含 jQuery。当你的 jQuery 版本比 WordPress 收录的版本落后一两步，你觉得会发生什么，hmmm？

添加上面那样的代码感觉像是在侵犯我的个人空间。如果您的 JavaScript 代码需要 jQuery，那么只需这样做:

```
wp_enqueue_script('jquery');
```

## 不可编辑的模板文本

你认识替你完成所有句子的人吗？输出你不能改变的页面文本的主题和插件也有同样的感觉。

这里有一个例子:如果你是一个主题制作人，你想在侧栏显示页面子菜单，避免把它硬编码到你的模板中；让它成为一个小部件。允许网站所有者对其进行定位并添加自定义标题。网站所有者可能不喜欢标题“子导航”，而更喜欢“在此部分”为什么你认为你有权做所有的决定？

至少提供一个选项面板。

## `IE=EmulateIE7`

这简直太残忍了；你也可以开始往我的眼睛里扔生锈的钉子，因为这比追查 CSS 错误的原因更有趣。

```
<meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7">
```

我在一个我花钱买的主题中发现了这一点。如果你是一个主题制作人，你不能让你的 CSS 在 IE 中工作，除非它模拟 IE7，你需要回到绘图板。非常糟糕的形式。

## 添加多余的主题功能

WordPress 主题是关于展示的。提供像谷歌分析支持这样的功能是不必要的，只会增加你的主题选项页面的混乱，就像一个从不闭嘴的同事。

如果我想使用分析，我可以得到一个插件。此外，如果我想使用谷歌分析以外的网站统计服务呢？

## 没有清理自己

如果网站所有者想删除你的插件或主题，不要吝惜他们这样做的权利；毕竟这是他们的地盘。把你所有的数据到处乱放就像是一个坏房客，当他们离开的时候会把房间弄得乱七八糟。

插件作者们，请确保你们添加了停用插件的动作。例如:

```
register_deactivation_hook(__FILE__,'my_plugin_deactivation');
function my_plugin_deactivation()
{
  //get rid of stored data here
}
```

添加起来很简单，你可以在 [WordPress Codex](http://codex.wordpress.org/Function_Reference/register_deactivation_hook) 上阅读所有相关内容。

目前还没有官方的方法让主题为注销事件分配动作，但是这里有一个看起来很有希望的方法。

## 忘记在你的主题中打电话给`wp_footer`

如果你不打算在你的主题中加入对`wp_footer`的召唤，你还不如偷我的鞋子。这是最基本的禁忌——许多插件都依赖于此——然而你仍然会发现主题，甚至是商业主题，无法调用`wp_footer`。

您需要将`<?php wp_footer(); ?>`放在结束的`</body>`标签之前。

这里没有争论。去做吧。

## 在`wp_head`内部执行初始化

就像在你最好朋友的婚礼上迟到一样，我注意到主题和插件开发者有时会在绑定到`wp_head`的函数中初始化重要的全局变量或常量。例如:

```
function my_init() {
define('REALLY_IMPORTANT_CONSTANT', 'really_important_value');
}
add_action('wp_head', 'my_init');
```

简单地说，`wp_head`动作应该只用于将标签输出到 HTML `head`中。如果你忽略了这条规则，你就把代码逻辑和 HTML 输出绑定在了一起，这通常是不好的。

## 承诺自定义菜单而不使用 WordPress 自定义菜单

你会答应带一辆货车帮你的朋友搬家，然后骑着摩托车出现吗？那么为什么你会承诺自定义菜单的功能，而不使用 WordPress 自定义菜单功能呢？我最近购买了一个主题，该主题提供了一个字段，网站所有者应该在其中输入逗号分隔的页面 id 列表:

> 输入要在主菜单中显示的页面的查询字符串(页面的左上角)。示例:include=9，10，20，21，32(这将为 id 为 9，10，20，21，32 的页面显示 5 个菜单项)。

如果你在你的主题中提供自定义菜单，定义菜单位置并允许网站所有者使用 WordPress 自定义菜单生成器；在 WordPress 3.0 中可用。对于网站所有者来说，它灵活且易于使用；向主题添加自定义菜单槽的代码非常简单。法典有你需要的所有代码。

## 不使用帖子缩略图的缩略图

这里有一个很好的方法来判网站所有者终身苦役。WordPress 从 2.9 版本开始就有了文章缩略图功能，但是你仍然会发现一些主题和插件迫使网站所有者手动将图片 URL 粘贴到自定义的文章字段中。

相反，创建一些自定义图像大小，然后使用后缩略图功能。它把艰苦的劳动变成了一劳永逸的选择:

```
add_theme_support( 'post-thumbnails');
if ( function_exists( 'add_image_size' ) ) {
  add_image_size( 'frontpage-news-thumb', 75, 45, true );
  add_image_size( 'frontpage-news-large', 570, 300, true );
  add_image_size( 'portfolio-page-thumb', 44, 30, true );
}
```

使用上面的代码，每当编辑器上传一个图像时，额外的尺寸将会自动生成。然后，编辑需要做的就是选择该图像作为文章的特征图像。

在你的主题或插件中，你可以用`the_post_thumbnail`函数输出合适的图像标签:

```
<?php the_post_thumbnail( 'frontpage-news-thumb' ); ?>
```

在法典上阅读所有相关内容。求你了。

## 不使用`add_meta_box`插入自定义邮箱

从 WordPress 2.5 开始就有了`add_meta_box`功能，但我还是看到了以下内容:

```
add_action('edit_page_form', custom_write_panel);
function custom_write_panel() {
  //echo lots of form HTML
}
```

上面的示例`custom_write_panel`函数将 HTML 表单回显到编辑后的表单中。这相当于把你最好的朋友放她母亲骨灰的骨灰盒吐了。

`add_meta_box`函数将您的自定义书写面板添加到全局`$wp_meta_boxes`数组中。一旦它可以被访问、覆盖——通常由其他代码或插件管理。这是做这件事的礼貌方式。

阅读[Codex](http://codex.wordpress.org/Function_Reference/add_meta_box)上的函数参考，获取详细信息和简单教程的链接

## 未能支持 HTTPS

如果你在 HTTPS 名下运行一个 WordPress 网站，持续的浏览器混合内容安全警告几乎是不可避免的，要求你手动修改插件和主题。这是因为许多主题和插件作者在包含 JavaScript 文件和图像等页面资源时，会硬编码“http”。这感觉就像被骗去买了一套全新的西装，却发现只有模特的前半部分被遮住了。现在你的屁股在微风中晃荡，而你却在向股东们发表你的重要演讲。

从 2.6 版本开始，WordPress 就有了`is_ssl`功能:

```
$scheme = ( is_ssl() ? 'https' : 'http' );
```

在有人受伤之前用它。

抛开硬编码不谈，有时候你真的不能责怪插件和主题创建者。毕竟，他们中的许多人只是简单的复制了 WordPress 本身的代码。在 WordPress 3.0.4，`default-constants.php`，第 77 行，我们发现:

```
if ( !defined('WP_CONTENT_URL') )
define( 'WP_CONTENT_URL', get_option('siteurl') . '/wp-content');
```

使用`get_option('siteurl')`只是从数据库中检索值，而没有任何 SSL 检查。更好的方法是使用`site_url` / `get_site_url`函数，因为这些函数执行适当的检查。

或者，您可以像我一样自己修补这个:

```
function fix_ssl_siteurl($url) {
  if ( 0 === strpos($url, 'http') && is_ssl() )
    $url = str_replace( 'http://', 'https://', $url );
  return $url;
}

add_filter('option_siteurl', 'fix_ssl_siteurl');
add_filter('option_home', 'fix_ssl_siteurl');
add_filter('option_url', 'fix_ssl_siteurl');
add_filter('option_wpurl', 'fix_ssl_siteurl');
add_filter('option_stylesheet_url', 'fix_ssl_siteurl');
add_filter('option_template_url', 'fix_ssl_siteurl');
```

你可以把上面的代码放在一个插件中，放在一个主题的`functions.php`文件中，或者，如果你运行一个多站点安装，把它放在你的`mu-plugins`文件夹中就可以了。当然，如果每个人都适当地支持 HTTPS 会更好。

## 不支持多站点 WordPress

如果你声称支持 WordPress 3.0 及以上版本，那么你必须支持多站点 WordPress 安装。如果你做不到这一点，你就是一个笨蛋。在我看来，这种经历就像赢得了一次家庭海洋世界之旅，但却发现我们中只有一个人被允许和海豚一起游泳。

在 WordPress 安装上启用多站点会改变文件上传路径、管理表单的 URL，并且数据库表名称对于每个博客都是唯一的。此外，插件可以激活整个网络。关于这个主题的文章本身就可以写满整个教程。但是这里有一些提示:

在多站点 WordPress 安装中，内容文件路径依赖于 URL 重定向。主题或插件制作者通常会包括一个支持库，比如图像处理库。如果你的主题或插件是这种情况，确保你的库使用 URL 重定向。他们中的许多人在这方面都失败了。

我使用的一个插件写了这样的选项表单动作:

```
<form method="post" action="<?php echo $_SERVER['PHP_SELF'] . '?page='
. basename(__FILE__); ?>&updated=true">
```

问题是，对于多站点 WordPress，每个博客的管理表单的路径是不同的，不等于`$_SERVER['PHP_SELF']`。这是我对那个插件的修复；它基本上将操作从根相对路径更改为普通相对路径:

```
<form method="post" action="<?php echo 'options-general.php?page=' .
basename(__FILE__); ?>&updated=true">
```

如果你的主题或插件需要自定义数据库表，那么不要忘记在多站点安装中每个站点都有一个唯一的表名前缀。这通常意味着使用全局`$wpdb`对象进行数据库访问，并使用`$wpdb->prefix`属性。这些都在[的法典](http://codex.wordpress.org/Creating_Tables_with_Plugins)和[的这篇博客](http://shibashake.com/wordpress-theme/write-a-plugin-for-wordpress-multi-site)中有所解释。

上面的博文也有一些处理网络激活和停用的有用例子。

## 总结和冷静

我现在感觉好一点了，因为我已经把那件事说了出来。归结起来就是:尽可能少的破坏，尽可能多的使用现有的 WordPress 特性，让一切都可以定制，并且认识到一些站点需要 SSL 或者是多站点安装。

如果网站所有者被迫对你的代码进行编辑，仅仅是为了让它可以正常工作，那么你失败了，WordPress 生态系统没有正常工作。

## 分享这篇文章