# 处理帖子请求的 WordPress 方式

> 原文：<https://www.sitepoint.com/handling-post-requests-the-wordpress-way/>

使用我们的教程，在你的 WordPress 网站获得正确的文件权限。

交互式网站需要能够与用户输入交互，用户输入通常以表单提交的形式出现。WordPress 网站也不例外。网站上每天都有各种各样的互动发生。例如，订阅时事通讯、向网站所有者发送消息以及填写订单。所有这些通常发生在表单提交期间的`POST`请求中。

![WordPress Transients API](img/012c2815249c57578225e53ef87eca03.png)

在本文中，我们将举一个处理来自用户的`POST`请求的简单例子，该请求是通过联系表单提交的。我们将使用一个内部的 WordPress 钩子来正确地获取数据，相应地处理数据，并将用户重定向回一个特定的页面。

本文假设你对 [WordPress 插件 API](https://codex.wordpress.org/Plugin_API) 有基本的了解。如果您不熟悉，强烈建议您先查看一下 Codex 页面。

## 背景

WordPress 基于一个[事件驱动架构](http://tommcfarlin.com/wordpress-and-mvc/)。这意味着在内部，WordPress 核心充满了各种动作和过滤器，以修改程序执行或在运行时改变内容。程序执行期间运行的动作的例子有`init`、`wp`、`template_redirect`和`wp_head`。许多插件利用这些动作和过滤器来修改 WordPress 的工作方式。

这与我们将要实现的目标没有什么不同。我们需要知道的只是`POST`请求所需的适当挂钩，并相应地修改我们的代码。这可以通过将我们所有的表单提交指向`wp-admin`目录中一个名为`admin-post.php`的特定文件来实现。如果你有将你的应用程序与 [WordPress 内部 AJAX API](https://codex.wordpress.org/AJAX_in_Plugins) 集成的经验，那么你会注意到`admin-post.php`的基本结构与`admin-ajax.php`没有太大的不同。

## 解剖 admin-post.php

在最基本的层面上，`admin-post.php`只包含 [71 行代码](https://core.trac.wordpress.org/browser/tags/4.2.4/src/wp-admin/admin-post.php)。它首先定义`WP_ADMIN`常量，然后通过要求`wp-load.php`来加载 WordPress。之后，它发送一个适当的报头并触发`admin_init`动作。

当前操作由这行代码决定:

```
$action = empty( $_REQUEST['action'] ) ? '' : $_REQUEST['action']; 
```

尽管名字如此，`admin-post.php`实际上可以处理`POST`和`GET`请求。然而，在本文的上下文中，我们感兴趣的只是与`POST`请求相关的内容。接下来，

```
if ( ! wp_validate_auth_cookie() ) {
    if ( empty( $action ) ) {
        /**
         * Fires on a non-authenticated admin post request where no action was supplied.
         *
         * @since 2.6.0
         */
        do_action( 'admin_post_nopriv' );
    } else {
        /**
         * Fires on a non-authenticated admin post request for the given action.
         *
         * The dynamic portion of the hook name, `$action`, refers to the given
         * request action.
         *
         * @since 2.6.0
         */
        do_action( "admin_post_nopriv_{$action}" );
    }
} else {
    if ( empty( $action ) ) {
        /**
         * Fires on an authenticated admin post request where no action was supplied.
         *
         * @since 2.6.0
         */
        do_action( 'admin_post' );
    } else {
        /**
         * Fires on an authenticated admin post request for the given action.
         *
         * The dynamic portion of the hook name, `$action`, refers to the given
         * request action.
         *
         * @since 2.6.0
         */
        do_action( "admin_post_{$action}" );
    }
} 
```

我们可以看到，根据用户的登录状态，将触发两个不同的操作挂钩，分别是登录用户的`admin_post`和未登录用户的`admin_post_nopriv`。如果您需要一个更精确的控制来只处理与您的请求相关的数据，那么还会触发一个更具体的动作，即`admin_post_nopriv_{$action}`和`admin_post_{$action}`，同样基于用户的登录状态。

假设我们的`POST`请求有一个值为`foobar`的`action`。如果当前用户未登录，将触发这两个操作:

*   `admin_post_nopriv`
*   `admin_post_nopriv_foobar`

如果您当前已登录，将会触发两个不同的操作:

*   `admin_post`
*   `admin_post_foobar`

对于`GET`请求，当您像这样访问 URL 时:

```
http://www.example.com/wp-admin/admin-post.php?action=foobar&data=test 
```

以上四个挂钩都还可以给你用。

有了这些知识，我们就可以很容易地挂钩到适当的动作来处理联系人表单提交，而不会真正弄乱我们的主题模板。

## 概念证明

我们以一个联系人表单为例。最简单的方法(不推荐)可能是基于基本的`page.php`创建一个定制的页面模板，硬编码一个表单并在文件本身中进行处理。大致看起来是这样的:

```
<?php
/*
 * Template Name: Contact Page
 */

get_header();

if ( ! empty( $_POST ) ) {
    // Sanitize the POST field
    // Generate email content
    // Send to appropriate email
}

?>

<div id="content">
    <form action="" method="post">
        <label for="fullname">Full Name</label>
        <input type="text" name="fullname" id="fullname" required>
        <label for="email">Email Address</label>
        <input type="email" name="email" id="email" required>
        <label for="message">Your Message</label>
        <textarea name="message" id="message"></textarea>
        <input type="submit" value="Send My Message">
    </form>
</div>

<?php
get_footer(); 
```

虽然这种实现是可行的，但是从长远来看，没有任何会导致麻烦的分离或关注。维护表单是一场噩梦，因为处理只发生在一个地方，如果我们要在其他地方复制表单，我们需要再次重做处理。

为了充分利用 WordPress 的事件驱动特性，也为了提供关注点的分离，我们可以使用提供的`admin-post.php`。转换现有表单使其与`admin-post.php`兼容也相当简单。

我们首先将这一行从:

```
<form action="" method="post"> 
```

收件人:

```
<form action="<?php echo esc_url( admin_url('admin-post.php') ); ?>" method="post"> 
```

为了生成指向`admin-post.php`的正确 URL，我们使用内置函数 [`admin_url`](https://codex.wordpress.org/Function_Reference/admin_url) 。这将确保我们的网址总是正确的参考当前网站正在运行。

我们还需要添加`action`隐藏输入，这样我们就可以触发与我们的联系表单提交相关的更具体的钩子。让我们以`contact_form`作为`action`值的例子。

我们在标签`<form>`之间的某处添加了这一行。

```
<input type="hidden" name="action" value="contact_form"> 
```

这是修改后页面模板的外观:

```
<?php
/*
 * Template Name: Contact Page
 */

get_header();
?>

<div id="content">
    <form action="<?php echo esc_url( admin_url('admin-post.php') ); ?>" method="post">
        <label for="fullname">Full Name</label>
        <input type="text" name="fullname" id="fullname" required>
        <label for="email">Email Address</label>
        <input type="email" name="email" id="email" required>
        <label for="message">Your Message</label>
        <textarea name="message" id="message"></textarea>
        <input type="hidden" name="action" value="contact_form">
        <input type="submit" value="Send My Message">
    </form>
</div>

<?php
get_footer(); 
```

请注意，我们正在删除模板顶部的后处理功能，因为我们将在稍后挂钩到该操作。

## POST 请求的实际处理

对于这一部分，我们有两个选择，选择任何一个都可以。我们可以通过主题的`functions.php`来挂钩到`admin_post_*`动作，或者我们可以创建一个简单的插件来处理联系人表单。为了简单起见，让我们将它编码到`functions.php`中。

请记住，我们在表单中有自己的自定义操作`contact_form`,所以按理说，我们可以使用这四个钩子:

*   `admin_post_nopriv`
*   `admin_post_nopriv_contact_form`
*   `admin_post`
*   `admin_post_contact_form`

打开当前主题的`functions.php`,添加以下内容:

```
function prefix_send_email_to_admin() {
    /**
     * At this point, $_GET/$_POST variable are available
     *
     * We can do our normal processing here
     */ 

    // Sanitize the POST field
    // Generate email content
    // Send to appropriate email
}
add_action( 'admin_post_nopriv_contact_form', 'prefix_send_email_to_admin` );
add_action( 'admin_post_contact_form', 'prefix_send_email_to_admin` ); 
```

因为无论用户当前是否登录，我们都希望以相同的方式处理表单提交，所以我们为`admin_post_nopriv_contact_form`和`admin_post_contact_form`指向相同的回调函数。实际的回调函数，`prefix_send_email_to_admin`将是你做数据处理的主要点。

## 结论

是一个有用的，隐藏在 WordPress 核心中的宝石。通过利用它，我们可以将实际模板和不需要显示的无关代码分离开来，通过挂钩到适当的钩子进行处理。

使用我们的教程，在你的 WordPress 网站获得正确的文件权限。

## 分享这篇文章