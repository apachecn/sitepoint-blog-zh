# 如何在 WordPress 中使用 Ajax 一个真实的例子

> 原文：<https://www.sitepoint.com/how-to-use-ajax-in-wordpress-a-real-world-example/>

Ajax 已经迅速成为一种流行的 web 技术，你会发现它被用在大多数网站上。Ajax 的关键特性是它可以管理数据库操作，而无需重新加载网页。这意味着，您可以从数据库获取数据并在前端显示它，而无需刷新页面。

这是一种快速而流畅的显示内容的方式，因此 Ajax 现在被用在网站的许多方面，比如提交博客评论、喜欢帖子和上传文件。你甚至可以让你的网站完全 Ajax 化，这样你网站的每个页面都可以异步加载。

鉴于 Ajax 的流行，大多数领先的 CMS 平台都在其架构中使用它。WordPress 也不例外。实际上，WordPress 以一种非常健壮和简单的方式使用 Ajax，今天我将通过一个真实的例子向你展示如何在 WordPress 中使用 Ajax。在我们开始之前，我假设你对 jQuery Ajax 方法和 WordPress 钩子有一些了解，因为我们两者都需要！

如果你正在寻找一些基本的 WordPress 开发教程，请查看 [WordPress 插件初学者开发](https://www.sitepoint.com/wordpress-plugin-development-beginners/)。

![WordPress jQuery UI](img/5eaf3282183fdda5b2fa6012e389816b.png)

## 我们要做什么？

你可能已经知道在 WordPress 中使用 Ajax 与在 WordPress 之外使用略有不同，你需要考虑两件事:

1.  WordPress `admin-ajax.php`文件的 URL，数据被发送到这里进行处理。
2.  名为`wp_ajax_`的 Ajax 动作钩子。您需要在其中挂接一个自定义函数，该函数将在 Ajax 调用期间执行。

让我们创建一个插件来理解这是如何工作的。假设我们想开发一个插件，允许用户将他们喜欢的博客文章保存在某个区域，以便他们以后可以阅读。这个特性对于杂志风格的博客非常有用，因为它每天都提供大量的内容。它将允许登录的用户将感兴趣的帖子保存在会员专用区，以便他们以后可以回来阅读。

因此，我们的“稍后读我”插件将做几件事:

*   首先，我们会在每篇博文内容的底部做一个链接。
*   当用户点击链接时，链接文章的 ID 将保存在 usermeta 数据库表中，无需重新加载页面。
*   最后，我们将创建一个小部件，根据保存在数据库中的文章 id 来显示博客文章。

明白了吗？让我们现在做一些跑腿的工作。

## 准备我们的插件文件和目录设置

首先，我们将在 WordPress 主安装的插件目录中创建一个名为`read-me-later`的文件夹。这个文件夹将包含插件所需的所有文件和子目录。现在在`read-me-later`文件夹中，我们需要创建另外两个名为`js`和`css`的文件夹。

然后，我们需要创建四个文件，我在下面列出了它们的正确文件扩展名:

*   `read-me-later.php`
*   `widget.php`
*   `read-me-later.js`
*   `read-me-later. css`

前两个文件将直接放在主插件文件夹中。`js`和`css`文件将分别放在`js`和`css`文件夹中。

现在我们将填充`read-me-later.php`文件。包括插件标题:

```
/**
 * Plugin Name: Read Me Later
 * Plugin URI: https://github.com/iamsayed/read-me-later
 * Description: This plugin allow you to add blog posts in read me later lists using Ajax
 * Version: 1.0.0
 * Author: Sayed Rahman
 * Author URI: https://github.com/iamsayed/
 * License: GPL3
 */ 
```

这段代码很重要，因为它被用来识别它是 WordPress 的一个插件。在上面的代码之后，我们将创建名为`ReadMeLater`的主插件类:

`class ReadMeLater {}`

## 包括基本的 JS 和 CSS 文件

接下来，我们需要用适当的 WordPress 钩子注册并排队我们的 JavaScript 和 CSS 文件。我们将创建几个方法来执行这个步骤。将这段代码复制到`ReadMeLater`类中:

```
/*
 * Action hooks
 */
public function run() {     
    // Enqueue plugin styles and scripts
    add_action( ‘plugins_loaded’, array( $this, 'enqueue_rml_scripts' ) );
    add_action( ‘plugins_loaded’, array( $this, 'enqueue_rml_styles' ) );      
}   
/**
 * Register plugin styles and scripts
 */
public function register_rml_scripts() {
    wp_register_script( 'rml-script', plugins_url( 'js/read-me-later.js', __FILE__ ), array('jquery'), null, true );
    wp_register_style( 'rml-style', plugins_url( 'css/read-me-later.css' ) );
}   
/**
 * Enqueues plugin-specific scripts.
 */
public function enqueue_rml_scripts() {        
    wp_enqueue_script( 'rml-script' );
}   
/**
 * Enqueues plugin-specific styles.
 */
public function enqueue_rml_styles() {         
    wp_enqueue_style( 'rml-style' ); 
} 
```

该代码相当简单明了。这里，我们创建了一个名为`register_rml_scripts()`的公共方法。在这个方法中，我们使用适当的 WordPress 函数注册了我们的`read-me-later.js`和`read-me-later.css`文件。

接下来的两个方法`enqueue_rml_scripts()`和`enqueue_rml_styles()`用于将 JavaScript 和样式表入队。我们还创建了一个`run`方法，它将包含我们所有的动作(和过滤)钩子。

如果你是 WordPress 的新手，你可以看看 Younes Rafie 的 WordPress 中的[入队脚本和样式，或者搜索 WordPress codex 来学习如何正确地注册和入队 JavaScript 和 CSS 文件。](https://www.sitepoint.com/enqueuing-scripts-styles-wordpress/)

## 在每个帖子下面创建“以后再看”链接

现在我们需要在每篇博文下面创建一个*稍后阅读我的文章*链接。通过点击该链接，用户可以选择将该文章保存在“稍后阅读”列表中。他们点击后，链接将从文章中消失，文章 ID 将保存在数据库中。当我们创建链接时，有两个注意事项:

*   只有登录的用户才能看到该链接。
*   该链接将包含“相关的”文章 ID，供以后使用。

为此，向`ReadMeLater`类添加以下函数:

```
/**
 * Adds a read me later button at the bottom of each post excerpt that allows logged in users to save those posts in their read me later list.
 *
 * @param string $content
 * @returns string
 */
public function rml_button( $content ) {   
    // Show read me later link only when the user is logged in
    if( is_user_logged_in() && get_post_type() == post ) {
        $html .= '<a href="#" class="rml_bttn" data-id="' . get_the_id() . '">Read Me Later</a>';
        $content .= $html;
    }
    return $content;       
} 
```

这里，我们检查了用户是否登录，以及帖子类型是否是帖子。检查后，我们创建链接。注意，我们使用 HTML5 数据属性来包含博客文章的 ID，可以使用函数`get_the_id()`来检索该 ID。由于链接将被放在 post 循环中，这正是我们需要的函数。

要将链接放在每篇博客文章下面，请将下面的代码添加到`run`方法中:

```
// Setup filter hook to show Read Me Later link
add_filter( 'the_excerpt', array( $this, 'rml_button' ) );
add_filter( 'the_content', array( $this, 'rml_button' ) ); 
```

这将过滤文章摘录并将链接放入循环中。现在，当你登录你的 WordPress 网站，浏览你的主页(或者显示你的文章的页面)时，你会在每篇文章的底部看到“稍后阅读”链接。

## 定义 Ajax URL

当你要进行 Ajax 调用时，你需要发送请求到`admin-ajax.php`文件，它是 WordPress 核心的一部分。这个文件负责处理 WordPress 上下文中的所有 Ajax 请求。不要使用文件路径的直接 URL。相反，使用将输出正确 URL 的`admin_url('admin-ajax.php')`。这样做的唯一问题是，不能将任何 PHP 函数放入 JavaScript 中。所以我们需要一个小技巧，请看下面的代码:

```
wp_localize_script( 'rml-script', 'readmelater_ajax', array( 'ajax_url' => admin_url('admin-ajax.php')) ); 
```

这里，我们使用一个名为`wp_localize_script()`的[函数](https://codex.wordpress.org/Function_Reference/wp_localize_script)。它需要三个参数:

1.  `rml-script`,`read-me-later.js`脚本的注册处理程序。
2.  一个类似 JavaScript 对象的字符串。
3.  一个数组，它是我们希望从 JavaScript 中传递的实际数据。

所以，如果我们写`rml_obj.ajax_url`，它将输出`admin_url('admin-ajax.php')`的值，换句话说，就是`admin-ajax.php`文件的 URL。我们将在 JavaScript 部分使用它。

不要忘记将上面的代码放在我们之前创建的`enqueue_rml_scripts()`方法中。

## 添加 JavaScript 和您的第一个 Ajax 调用

现在该创建我们的 Ajax 调用了。从我们的`js`文件夹中打开`read-me-later.js`文件。添加以下代码:

```
jQuery(document).ready( function(){         
    jQuery('#content').on('click', 'a.rml_bttn', function(e) { 
        e.preventDefault();
        var rml_post_id = jQuery(this).data( 'id' );    
        jQuery.ajax({
            url : readmelater_ajax.ajax_url,
            type : 'post',
            data : {
                action : 'read_me_later',
                post_id : rml_post_id
            },
            success : function( response ) {
                jQuery('.rml_contents').html(response);
            }
        });
        jQuery(this).hide();            
    });     
}); 
```

在上面的代码中，我们创建了一个函数，当用户点击“稍后阅读”链接时，这个函数将被调用。在这个函数中，我们使用数据方法获取文章 ID，并将其存储到“rml_post_id”变量中。之后，我们使用 jQuery '$ '进行 Ajax 调用。ajax()”方法。正如我们在本文前面提到的，这个方法有几个属性。我来一一解释一下。

`url`包含了`admin-ajax.php`文件的 URL。还记得我们在上一步是如何定义`rml_obj.ajax_url`的吗？这就是我们在这里使用 URL 的方式。我们的 Ajax 请求将被发送到那里进行处理。

`type`表示请求将使用 HTTP `‘$_GET[]’`还是`‘$_POST[]’`方法发送。我们在这里使用`‘$_POST[]’`方法，因为我们将其设置为`post`。

包含了我们想通过 Ajax 调用发送的数据。这里，我们的数据是作为键值对的对象。`post_id`包含帖子 ID，`action`包含`read_me_later`，是`wp_ajax_`钩子的后缀。我们将在下一步定义 Ajax 动作钩子及其回调函数。

最后一个是包含匿名函数的`success`。它将在 Ajax 调用完成时触发。

确保您的`read me later`链接用一个带有`#content` `id`属性的 div 标签包装，否则 jQuery 将无法工作。

现在我们需要在用户点击后立即删除`Read Me Later`链接，这样用户就不能保存一篇文章两次。为了实现这一点，我们在`jQuery.ajax()`方法后添加了以下代码:

```
jQuery(this).hide(); 
```

这将在用户点击“稍后阅读”链接时删除该链接。

## Ajax 操作挂钩

现在是最重要的部分。

到目前为止，我们已经创建了`Read Me Later`链接，并用 Ajax 连接了它。但是链接还没有做任何事情，因为我们还没有编写任何服务器端代码来处理 Ajax 请求。当用户点击链接时，我们需要将帖子 ID 保存在数据库中，然后根据数据库信息在前端显示帖子。

为了完成这种服务器端处理，WordPress 给了我们两个动作钩子，`wp_ajax_my_action`和`wp_ajax_nopriv_my_action`。第一个选项只对已登录的用户有效，第二个选项在用户未登录时有用。因为我们的示例插件只为登录用户设计，所以我们将使用第一个插件。注意`my_action`是`wp_ajax_`钩子的后缀，你可以随意命名。

在`run()`方法中添加以下代码片段:

```
// Setup Ajax action hook
add_action( 'wp_ajax_read_me_later', array( $this, 'read_me_later' ) ); 
```

对于上面的代码，您唯一需要注意的是确保您的 Ajax 钩子后缀与您的`jQuery.ajax()`方法的`action`属性值相匹配(见上一步)。你可能会注意到，我们给回调函数取了相同的名字，这样我们就可以很容易地记住它。现在我们将定义我们的回调函数:

```
public function read_me_later() {
    $rml_post_id = $_POST['post_id']; 
    $echo = array();       
    if(get_user_meta( wp_get_current_user()->ID, 'rml_post_ids', true ) !== null ) {
        $value = get_user_meta( wp_get_current_user()->ID, 'rml_post_ids', true );
    }

    if( $value ) {
        $echo = $value;
        array_push( $echo, $rml_post_id );
    }
    else {
        $echo = array( $rml_post_id );
    }

    update_user_meta( wp_get_current_user()->ID, 'rml_post_ids', $echo );
    $ids = get_user_meta( wp_get_current_user()->ID, 'rml_post_ids', true );
} 
```

上面的代码应该放在我们的主插件类中。让我解释一下我在这里做了什么。

首先，我们将文章 ID 存储在`$rml_post_id`变量中。然后我们声明了一个名为`$echo`的空数组。

之后，我们检查数据库的`usermeta`表中是否有一个带有关键字`rml_post_ids`的字段。如果有一行，我们使用`get_user_meta()` WordPress 函数获取元值，并将其存储在`$value`中。

同样，我们检查`$value`是否存在。如果为真，我们将它存储在之前声明的`$echo`数组中。然后我们使用`array_push()`函数将`$rml_post_id`的值推入数组。如果没有`$value`，那么我们只需将`$rml_post_id`存储在`$echo`中。

`update_user_meta()`负责用`$echo`中存储的数据更新(或创建，如果该字段尚未创建)元字段。

最后，我们使用`$ids`中的`get_user_meta()`将最近填充的元字段存储为一个数组。

现在我们已经得到了用户选择的文章 id，我们需要显示这些文章。添加以下代码:

```
// Query read me later posts
$args = array( 
    'post_type' => 'post',
    'orderby' => 'DESC', 
    'posts_per_page' => -1, 
    'numberposts' => -1,
    'post__in' => $ids
);

$rmlposts = get_posts( $args );
if( $ids ) :
    global $post;
    foreach ( $rmlposts as $post ) :
        setup_postdata( $post );
        $img = wp_get_attachment_image_src( get_post_thumbnail_id() ); 
    ?>          
        <div class="rml_posts">                 
            <div class="rml_post_content">
                <h5><a href="<?php echo get_the_permalink(); ?>"><?php the_title(); ?></a></h5>
                <p><?php the_excerpt(); ?></p>
            </div>
            <img src="<?php echo $img[0]; ?>" alt="<?php echo get_the_title(); ?>" class="rml_img">                    
        </div>
    <?php 
    endforeach; 
    wp_reset_postdata(); 
endif;      

// Always die in functions echoing Ajax content
die(); 
```

这里，我们使用 WordPress `get_posts()`函数根据用户的选择获取所有的帖子。这里唯一需要的参数是包含文章 id 数组的`post__in`。最后，我们使用`die()`,这样我们的 Ajax 内容将会正确地回显。

下面是`read_me_later()`函数的完整代码:

```
/**
 * Hook into wp_ajax_ to save post ids, then display those posts using get_posts() function
 */
public function read_me_later() {

    $rml_post_id = $_POST['post_id']; 
    $echo = array();

    if( get_user_meta( wp_get_current_user()->ID, 'rml_post_ids', true ) !== null ) {
        $value = get_user_meta( wp_get_current_user()->ID, 'rml_post_ids', true );
    }

    if( $value ) {
        $echo = $value;
        array_push( $echo, $rml_post_id );
    }
    else {
        $echo = array( $rml_post_id );
    }

    update_user_meta( wp_get_current_user()->ID, 'rml_post_ids', $echo );
    $ids = get_user_meta( wp_get_current_user()->ID, 'rml_post_ids', true );

    // Query read me later posts
    $args = array( 
        'post_type' => 'post',
        'orderby' => 'DESC', 
        'posts_per_page' => -1, 
        'numberposts' => -1,
        'post__in' => $ids
    );

    $rmlposts = get_posts( $args );
    if( $ids ) :
        global $post;
        foreach ( $rmlposts as $post ) :
            setup_postdata( $post );
            $img = wp_get_attachment_image_src( get_post_thumbnail_id() ); 
        ?>          
            <div class="rml_posts">                 
                <div class="rml_post_content">
                    <h5><a href="<?php echo get_the_permalink(); ?>"><?php the_title(); ?></a></h5>
                    <p><?php the_excerpt(); ?></p>
                </div>
                <img src="<?php echo $img[0]; ?>" alt="<?php echo get_the_title(); ?>" class="rml_img">                    
            </div>
        <?php 
        endforeach; 
        wp_reset_postdata(); 
    endif;      

    // Always die in functions echoing Ajax content
    die();

} 
```

## 为稍后阅读帖子创建小部件

现在我们需要一个小部件来显示用户保存的帖子。为了简单起见，我们将制作一个非常基本的小部件。我不打算在这里详细介绍，我们只需要扩展 WordPress 的`WP_Widget`类来创建一个定制的小部件。让我们开始吧，打开`widget.php`文件并创建一个名为`RML_Widget`的子类，它扩展了`WP_Widget`类。

```
class RML_Widget extends WP_Widget {} 
```

在类中创建`__construct()`魔法方法来初始化我们的小部件:

```
function __construct() {
    parent::__construct(
       'rml_widget', // Base ID
        __( 'Read Me Later', 'text_domain' ), // Name
        array( 'classname' => 'rml_widgt', 'description' => __( 'Read Me Later widget for displaying saved posts', 'text_domain' ), ) // Args
    );
} 
```

在这里，我们已经为小部件设置了名称和描述，它将显示在 dashboard 小部件部分。

一个后端小部件表单将由`form()`方法创建，如下所示:

```
public function form( $instance ) {
    if ( isset( $instance['title'] ) ) {
        $title = $instance['title'];
    } else {
        $title = __( 'Read Me Later Posts', 'text_domain' );
    }
    ?>
    <p>
        <label for="<?php echo $this->get_field_id( 'title' ); ?>"><?php _e( 'Title:' ); ?></label>
        <input class="widefat" id="<?php echo $this->get_field_id( 'title' ); ?>"
            name="<?php echo $this->get_field_name( 'title' ); ?>" type="text"
            value="<?php echo esc_attr( $title ); ?>">
        </p>
    <?php
} 
```

如您所见，我们的表单由一个包含小部件标题的文本字段组成。我们在`$title`变量中分配我们的标题。`get_field_id()`和`get_field_name()`分别给我们的文本字段一个惟一的 ID 和名称。

`update()`方法负责净化和更新用户输入值。

```
public function update( $new_instance, $old_instance ) {
    $instance          = array();
    $instance['title'] = ( ! empty( $new_instance['title'] ) ) ? strip_tags( $new_instance['title'] ) : '';

    return $instance;
} 
```

它需要两个参数:

1.  `$new_instance`包含用户使用我们通过`form()`方法创建的后端表单输入的值。
2.  `$old_instance`则相反，它包含以前的值。

现在我们将创建`widget()`方法，该方法将在前端显示“稍后阅读我的文章”。

```
public function widget( $args, $instance ) {      
    $title = apply_filters( 'widget_title', $instance['title'] ); 
    echo $args['before_widget'];
    if ( ! empty( $title ) ) {
        echo $args['before_title'] . $title . $args['after_title'];
    }

    echo '<div class="rml_contents">';

        $ids = get_user_meta( wp_get_current_user()->ID, 'rml_post_ids', true );

        $args = array( 
            'post_type' => 'post',
            'orderby' => 'DESC', 
            'posts_per_page' => -1, 
            'numberposts' => -1,
            'post__in' => $ids
        );

        $rmlposts = get_posts( $args );
        if( $ids ) :
            global $post;
            foreach ( $rmlposts as $post ) :
                setup_postdata( $post );
                $img = wp_get_attachment_image_src( get_post_thumbnail_id() ); 
                ?>          
                <div class="rml_posts">                 
                    <div class="rml_post_content">
                        <h4><a href="<?php echo get_the_permalink(); ?>"><?php the_title(); ?></a></h4>
                        <p><?php the_excerpt; ?></p>
                    </div>
                    <img src="<?php echo $img[0]; ?>" alt="<?php echo get_the_title(); ?>" class="rml_img">                    
                </div>
            <?php 
            endforeach;
            wp_reset_postdata();
        else :
        echo '<p>You have no saved posts now.</p>';
        endif;  

    echo '</div>';      
    echo $args['after_widget'];
} 
```

这里我们使用`get_posts()`函数来显示文章。和`read_me_later()`方法差不多。

不要忘记通过在`read-me-later.php`文件的顶部添加以下代码来包含`widget.php`文件:

```
require(plugin_dir_path( __FILE__ ).'widget.php'); 
```

## 确保 Ajax 调用的安全性

在使用 Ajax 时，您应该采取必要的措施来保证代码的安全。如果您要从用户那里接收任何数据，请在将数据保存到数据库之前对其进行清理。使用`nonce`检查请求是否来自正确的位置，是否由经过验证的用户发出。在这里，我将向你展示如何在 Ajax 调用中使用 WordPress `nonce`。

首先，我们将使用`wp_create_nonce()`方法创建一个 nonce，并从 JavaScript 传递它。为此，使用来自`enqueue_rml_scripts()`方法的代码:

```
wp_localize_script( 'read-me-later', 'readmelater_ajax', array( 'ajax_url' => admin_url('admin-ajax.php') ) ); 
```

并用下面的代码替换它:

```
wp_localize_script( 'read-me-later', 'readmelater_ajax', array( 'ajax_url' => admin_url('admin-ajax.php'), 'check_nonce' => wp_create_nonce('rml-nonce') ) ); 
```

现在我们可以使用`readmelater_ajax.check_nonce`从 JavaScript 中访问 nonce 值。在 JavaScript 文件的`jQuery.ajax()`方法中添加一个安全属性，如下所示:

```
security : readmelater_ajax.check_nonce 
```

我们最终的 JavaScript 将如下所示:

```
jQuery(document).ready( function(){ 

    jQuery('#content').on('click', 'a.rml_bttn', function(e) { 
        e.preventDefault();

        var rml_post_id = jQuery(this).data( 'id' );

        jQuery.ajax({
            url : readmelater_ajax.ajax_url,
            type : 'post',
            data : {
                action : 'read_me_later',
                security : readmelater_ajax.check_nonce,               
                post_id : rml_post_id
            },
            success : function( response ) {
                jQuery('.rml_contents').html(response);
            }
        });

        jQuery(this).hide();
    }); 

}); 
```

最后，我们需要检查 Ajax 回调中的 nonce。我们将使用`check_ajax_referer()`函数来实现。在我们之前创建的`read_me_later()`方法的开头添加以下代码:

```
check_ajax_referer( 'rml-nonce', 'security' ); 
```

这需要两个参数。第一个是我们使用`wp_create_nonce()`创建的密钥。第二个是我们从 JavaScript 传递的`security`属性。

如果 nonce 不正确或者没有设置，Ajax 调用将会终止。这样，我们的脚本将阻止无效的 Ajax 请求。

## 结论

在本教程中，我们制作了一个系统，用户可以将他们喜欢的帖子保存在一个列表中，并在以后阅读。你可以随时添加更多的功能，例如创建一个不同的页面来显示所有保存的文章，能够将页面添加到列表中，或者添加来自自定义文章类型的文章。您甚至可以创建一个仪表板设置页面来配置所有选项。这取决于你和你想为你的用户创造什么样的功能。

如你所见，在 WordPress 中使用 Ajax 很容易。第一次可能会让人望而生畏，但是一旦你明白了如何去做，它就工作了，看起来也很棒。WordPress 钩子无处不在，它们让你的生活变得更容易。我希望你已经从本教程中学到了一些有用的技术，现在开始使用 Ajax 并做出你喜欢的东西吧！

## 分享这篇文章