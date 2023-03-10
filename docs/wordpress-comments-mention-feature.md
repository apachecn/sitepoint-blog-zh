# 用提及功能增强你的 WordPress 评论

> 原文：<https://www.sitepoint.com/wordpress-comments-mention-feature/>

许多 WordPress 网站允许访问者在博客文章上分享评论。有几个 WordPress 插件也可以让访问者回复评论。然而，当评论中有多个作者被回复或引用时，这个过程会变得混乱。

在本教程中，我将讲述如何编写一个方便的 WordPress 插件，将类似 Twitter 的提及功能引入我们的 WordPress 网站，允许读者在评论中互相标记。

在本教程的最后，我会提供一个下载插件的链接，这个插件也有一些额外的特性，还有一个在 GitHub 上查看插件的链接。

![mention feature comments](img/d1203f6958812077e74195c204aeef7a.png)

## 编写插件代码

所有的 WordPress 插件都是位于`/wp-content/plugins/`目录下的 PHP 文件。在这里，我们创建一个 PHP 文件**wp-mention-plugin.php**。

接下来，我们包含插件头，否则 WordPress 不会识别我们的插件。

```
<?php
/**
 * Plugin Name: WP Mention Plugin
 * Plugin URI: https://sitepoint.com
 * Description: Mention both registered and non registered @comment__author in comments
 * Version: 1.0.0
 * Author: Doyin Faith Kasumu
 * Author URI: http://t2techblog.com
 * License: GPL2
 */ 
```

现在我们创建插件类如下:

```
class wp_mention_plugin {

//codes 
```

我们所有的动作和过滤挂钩都将进入`initialize()`方法，因为我们将它作为我们的构造函数，当类被实例化时调用的第一个方法。

```
 public static function initialize() {

        add_filter( 'comment_text', array ( 'wp_mention_plugin', 'wpmp_mod_comment' ) );

        add_action( 'wp_set_comment_status', array ( 'wp_mention_plugin', 'wpmp_approved' ), 10, 2 );

        add_action( 'wp_insert_comment', array ( 'wp_mention_plugin', 'wpmp_no_approve' ), 10, 2 );
    } 
```

### 代码解释

`comment_text`过滤钩过滤要显示的注释文本。当评论状态改变时，立即触发`wp_set_comment_status`动作钩子，当提交评论时，立即触发`wp_insert_comment`动作钩子。

我们将使用不带引号的“@”符号对任何名称进行样式化，这就是为什么我们需要`comment_text`过滤器挂钩。

在 WordPress 网站中，评论在被批准前会被保留以供审核，我们需要`wp_set_comment_status` action 钩子来跟踪上述作者新批准的评论。

需要使用`wp_insert_comment`来跟踪提到的作者的评论，以防评论没有被审核而是被自动批准。

现在让我们创建在我们的`initialize()`方法中调用的函数。首先是`wpmp_mod_comment()`

```
 public static function wpmp_mod_comment( $comment ) {

        // we set our preferred @mention text color here
        $color_code  = '#00BFFF';

        $pattern     = "/(^|\s)@(\w+)/";
        $replacement = "<span style='color: $color_code;'>$0</span>";
        $mod_comment = preg_replace( $pattern, $replacement, $comment );

        return $mod_comment;
    } 
```

在这里，我们创建一个方法`wpmp_mod_comment()`含义: **WP 提插件修改评论**。该函数接受单个参数，即我们正在修改的注释。这个参数不用担心，WordPress 处理。

`$pattern`变量创建一个简单的正则表达式模式，该模式确定**@ any _ _ comment _ _ author _ _ name**是否在任何评论中被提及。当像 **admin@sitepoint** 这样的东西被包含在评论中时，它们不会被记录为有效提及。有效的提及将被设计成深蓝色。

如果你想改变@提及文本的颜色，只需改变变量`$color_code`的值。

在我们继续之前，让我们创建一个方法来处理发送给提到的评论作者的通知邮件。

```
 private static function wpmp_send_mail( $comment ) {
        $the_related_post        = $comment->comment_post_ID;
        $the_related_comment     = $comment->comment_ID;
        $the_related_post_url    = get_permalink( $the_related_post );
        $the_related_comment_url = get_comment_link( $the_related_comment );

        // we get the mentions here
        $the_comment = $comment->comment_content;
        $pattern     = "/(^|\s)@(\w+)/";

        // if we find a match of the mention pattern
        if ( preg_match_all( $pattern, $the_comment, $match ) ) {

            // remove all @s from comment author names to effectively
            // generate appropriate email addresses of authors mentioned
            foreach ( $match as $m ) {
                $email_owner_name = preg_replace( '/@/', '', $m );
                $email_owner_name = array_map( 'trim', $email_owner_name );
            }

            /**
             * This part is for For full names, make comment authors replace spaces them  with
             * two underscores. e.g, John Doe would be mentioned as @John__Doe
             *
             * PS: Mentions are case insensitive
             */
            if ( preg_match_all( '/\w+__\w+/', implode( '', $email_owner_name ) ) ) {
                $email_owner_name = str_ireplace( '__', ' ', $email_owner_name );
            }

            // we generate all the mentioned comment author(s) email addresses
            $author_emails = array_map( 'self::wpmp_gen_email', $email_owner_name );

            // ensure at least one valid comment author is mentioned before sending email
            if ( ! is_null( $author_emails ) ) {
                $subj = '[' . get_bloginfo( 'name' ) . '] Someone mentioned you in a comment';

                $email_body = "Hello, someone mentioned you in a comment($the_related_comment_url)".
                              " at MyBlog.com. Here is a link to the related post: $the_related_post_url";

                wp_mail( $author_emails, $subj, $email_body );

            }
        }
    } 
```

### 代码解释

在上面的方法中，我们过滤评论来检查是否有人被提到。如果为真，我们删除评论作者姓名前的“@”符号，这样我们就可以使用该姓名从数据库中生成评论作者信息。

有时，评论作者会填写全名，例如“John Doe ”,或者以空格作为评论作者的姓名。在这种情况下，任何提到它们的人都必须用双下划线替换名称中的空格。因此，从第`if ( preg_match_all( '/\w+__\w+/', implode( '', $email_owner_name ) ) ) ....`行开始，我们确保@ note 文本中的所有双下划线都被转换成空格。

然后，我们使用接下来将创建的`wpmp_gen_email()`方法生成提到的评论作者电子邮件地址。`$email_body`变量和`wp_mail()`函数分别存储和发送通知邮件，并带有相应评论和帖子的链接。

**注意**如果你注意到在代码中，提到的评论作者和他们的电子邮件地址被存储在一个数组中，这只是为了以防不止一个评论作者被提到。

记得将`$email_body`中的行`MyBlog.com`更改为您想要的站点名称。

```
 public static function wpmp_gen_email( $name ) {
        global $wpdb;

        $name  = sanitize_text_field( $name );
        $query = "SELECT comment_author_email FROM {$wpdb->comments} WHERE comment_author = %s ";

        $prepare_email_address = $wpdb->prepare( $query, $name );
        $email_address         = $wpdb->get_var( $prepare_email_address );

        return $email_address;
    } 
```

### 代码解释

我之前已经谈了很多关于这种方法的内容，它只是生成提到的评论作者的电子邮件。它接受的`$name`字符串参数是评论作者姓名。然后，它使用评论作者名从数据库中获取提到的评论作者信息。为了防止 SQL 攻击，我们使用 WordPress `prepare()`函数来确保查询得到安全处理。

现在，我们又创建了两个方法，在发送通知电子邮件之前，跟踪评论是否首先被保留以供审核。

```
 public static function wpmp_approved( $comment_id, $status ) {
        $comment = get_comment( $comment_id, OBJECT );
        ( $comment && $status == 'approve' ? self::wpmp_send_mail( $comment ) : null );
    }

    public static function wpmp_no_approve( $comment_id, $comment_object ) {
        ( wp_get_comment_status( $comment_id ) == 'approved' ? self::wpmp_send_mail( $comment_object ) : null );
    } 
```

在调用发送通知邮件的`wpmp_send_mail()`方法之前，`wpmp_approved()`首先检查带有`$comment_id`的评论是否被批准。

而`wpmp_no_approve()`调用`wpmp_send_mail()`方法，该方法在评论被自动批准时立即发送电子邮件通知，无需等待审核。

在插件完成我们刚刚编写的任务之前，我们必须实例化我们刚刚处理过的类`wp_mention_plugin`，并调用`initialize()`方法来注册我们需要的动作和过滤钩子。

```
}

$wp_mention_plugin = new wp_mention_plugin;
$wp_mention_plugin->initialize();

?> 
```

![plugin notification feature](img/403ee65afde2d096f574775e28e63798.png)

## 结论

在本教程中，我介绍了如何创建一个方便的 WordPress 插件，将类似 Twitter 的提及功能引入我们的 WordPress 站点。

你可以在 Comment here 中下载 [Atmention，这是我创建的一个插件，处理与其他一些特性的集成。否则可以在 GitHub 上查看一下插件: **WP 提插件**](https://wordpress.org/plugins/atmention-in-comments) [代码。](https://github.com/Turn2honey/plugins/blob/master/wp-mention-plugin/wp-mention-plugin.php)

## 分享这篇文章