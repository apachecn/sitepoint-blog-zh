# 揭开 WordPress 钩子系统的神秘面纱

> 原文：<https://www.sitepoint.com/wordpress-hook-system/>

如果你一直在用 WordPress 开发网站(包括插件和主题开发)，你可能听说过这些术语:`Hooks`、`Actions`和`Filters`。这些是 WordPress 使用的[事件驱动架构模式](https://en.wikipedia.org/wiki/Event-driven_architecture)的一部分。

你是 WordPress 开发的新手还是觉得理解基本概念很难？我怎么推荐都不够高[西蒙·柯灵顿的](https://www.sitepoint.com/author/scodrington/)[WordPress 插件开发教程简介](https://www.sitepoint.com/an-introduction-to-wordpress-plugin-development/)。他很好地解释了`Actions`和`Filters`。

在本教程中，我将不遗余力地揭开 WordPress hook 系统的神秘面纱。事不宜迟，我们开始吧。

## 钩子，动作，过滤器。它们是什么？

“钩子”基本上是由 WordPress 核心、主题和插件在 PHP 执行或解释的不同阶段触发的事件。当这些事件被触发时，所有与它们相关联的函数和/或类方法都以正确的顺序执行。

钩子有两种形式，动作和过滤器。前者用于在流程执行的不同阶段添加和删除特性或功能，而后者修改各种特性和实现的行为。如果你还是不明白，不要担心。当我们开始看到下面的一些代码示例时，你就会明白了。

## 钩子系统在 WordPress 中的重要性

hook 系统在 WordPress 中的重要性简单来说就是**扩展性**。它可以添加和删除功能，以及调整/修改 WordPress 核心、插件和主题中功能的实现。

当你编写可扩展的插件和主题时，你使得其他开发者在不编辑核心源代码的情况下改进和扩展它们成为可能。

![WordPress Hooks](img/ba920396a155624590c6a71dd959ad04.png)

请允许我举一个例子。与大多数支付网关不同，我的用于 WooCommerce 的 [2Checkout 支付网关插件没有在结账页面上显示支持的信用卡类型的图标，因为我觉得没有必要。但是你知道吗？我添加了一个`filter`以防用户有不同的感觉。](https://omnipay.io/downloads/better-2checkout-payment-gateway-for-woocommerce/)

碰巧我们收到了一个客户的支持请求，要求加入图标。我们能够向客户提供一个代码片段，它与过滤器挂钩并包含图标。

## 深入研究 WordPress 钩子系统

在 WordPress 执行的不同阶段，大量的事件通常使用 PHP 函数`do_actions()`和`apply_filters()`来触发。这些活动可以通过`add_action()`和`add_filter()`订阅或挂钩。

请注意“通常”一词的用法。还有其他方式可以触发事件。我们将在本教程的第二部分探讨这个问题。

下面是一个插件中的`action`的例子。在我的 [ProfilePress 用户注册插件](http://profilepress.net/)中成功注册用户后，该操作被触发。

```
/**
 * Fires after a user registration is completed.
 *
 * @param int $form_id ID of the registration form.
 * @param mixed $user_data array of registered user info.
 * @param int $user_id ID of the registered user.
 */
do_action( 'pp_after_registration', $form_id, $user_data, $user_id ); 
```

在 WordPress 执行过程中，所有与这个动作相关的功能都将被处理。

过滤钩子的一个例子是 WordPress core 中的`the_content`,它过滤每个帖子的内容。

```
 /**
     * Filter the post content.
     *
     * @since 0.71
     *
     * @param string $content Content of the current post.
     */
    $content = apply_filters( 'the_content', $content ); 
```

### 注意到

在`do_action()`中，第一个参数是动作挂钩的名称，随后的参数是挂钩动作的函数可用的变量。

在`apply_filters()`中，第一个参数是过滤器挂钩的名称，第二个参数是挂钩到过滤器的函数被修改或应用的数据或值。后续参数是挂钩到过滤器的函数可用的变量/值。

不要担心，当我们检查代码示例时，所有这些都会变得更有意义。

### 动作挂钩示例

#### 示例#1

带着我的 [ProfilePress 插件](http://profilepress.net/)的`pp_after_registration`动作兜一圈；假设我们想要实现一个功能，用户在注册后会收到一条短信(通过一个叫做 **Dolio** 的短信服务)欢迎他们立即来到你的网站。我们的函数挂钩可以是这样的形式:

```
add_action( 'pp_after_registration', 'send_users_welcome_sms', 20, 3 );

function send_users_welcome_sms( $form_id, $user_data, $user_id ) {
    global $service_locator;

    $username    = $user_data['username'];
    $firstName   = $user_data['first_name'];
    $lastName    = $user_data['last_name'];
    $phoneNumber = $user_data['phone_number'];

    $text = <<<SMS_CONTENT
Hello $firstName $lastName, Welcome to SitePoint. "\r\n"

User ID: $user_id "\r\n"
Username: $username "\r\n"
Password: The password you sign up with "\r\n"

SMS_CONTENT;

    $dolio = $service_locator->get( 'dolio_sdk' );
    $dolio->phone_number( $phoneNumber );
    $dolio->sms_content( $text );
    $dolio->send();

} 
```

上面代码中`add_action`的第三个参数是挂钩优先级，它指定了挂钩到`pp_after_registration`动作的函数的执行顺序。留空将默认为`10`。而第四个参数指定函数挂钩将接受的参数数量。如果为空，默认为`1`。

假设我省略了第四个参数，因此默认为`1`，`$user_data`和`$user_id`变量将是`null`，因为我们只告诉函数只接受一个参数。

#### 实施例 2

WordPress 包括下面的`action`钩子——`wp_head`和`wp_footer`——它们分别在前端的 head 标签中和结束 body 标签之前被触发。

这些钩子可以用来在那些关键位置显示脚本和数据。

让我们来看看一些代码示例。

下面的代码使用`wp_head`将 Google 的站点验证 meta 标签包含到 WordPress 前端的头部。

```
add_action( 'wp_head', 'google_site_verification' );

function google_site_verification() {
    echo '<meta name="google-site-verification" content="ytl89rlFsAzH7dWLs_U2mdlivbrr_jgV4Gq7wClHDUJ8" />';
} 
```

所有的钩子函数都将是匿名的，以避免函数名的不必要重复。例如，上面的 Google 站点验证 Meta 标记的代码现在将变成:

```
add_action( 'wp_head', function () {
    echo '<meta name="google-site-verification" content="ytl89rlFsAzH7dWLs_U2mdlivbrr_jgV4Gq7wClHDUJ8" />';
}); 
```

下面的代码使用`wp_footer`在 WordPress 前端的页脚区域添加 JavaScript。

```
add_action( 'wp_footer', function () {
    echo '<script type="text/javascript" src="http://example.com/wp-content/plugins/site-specific-plugin/hello-bar.js"></script>';
}); 
```

动作挂钩代码示例已经讲得够多了，让我们来看看过滤器。

### 过滤器挂钩示例

#### 示例#1

假设我们正在开发一个`ad inserter`插件，它将在每个帖子内容的前后有计划地插入广告，那么`the_content`过滤器就是我们所需要的。

下面的代码包括每个帖子内容前后的文本“我们爱 SitePoint”。

```
add_filter( 'the_content',
    function ( $content ) {
        $text = sprintf( '<div class="notice alert">%s</div>', __( 'We love SitePoint', 'sp' ) );
        $content = $text . $content . $text;
        return $content;
    }); 
```

**代码解释:**`$text`变量的内容与`<div class="notice alert">We love SitePoint</div>`相同，尽管已经国际化，因此可以本地化。迷茫？请看一下我在 [WordPress i18n](https://www.sitepoint.com/wordpress-i18n-make-your-plugin-translation-ready/) 和 [l10n](https://www.sitepoint.com/wordpress-l10n-translate-your-plugin/) 上的教程。

注意，函数参数`$content`是提供文章内容的变量。

然后，我们在文章内容的前后添加自定义文本，将结果数据保存到`$content`并随后返回。

注意:所有`filter`钩子函数在操作或修改后必须返回变量参数。

#### 实施例 2

我们要看的另一个滤波器例子是`the_title`。下面是`wp-includes/post-template.php`的 **158** 行的定义。

```
 /**
     * Filter the post title.
     *
     * @since 0.71
     *
     * @param string $title The post title.
     * @param int    $id    The post ID.
     */
    return apply_filters( 'the_title', $title, $id ); 
```

下面的代码只修改了 ID 为 5978 的文章的标题，在标题后面添加了`- WeLoveSitePoint`。多亏了`$id`参数，这才成为可能。

```
add_filter( 'the_title', function ( $title, $id ) {
        if ( $id == '5978' ) {
            $title .= ' - WeLoveSitePoint';
        }

        return $title;

    }, 10, 2 
); 
```

## 结论

WordPress 仍然是领先的内容管理系统的原因是因为它的可扩展性。

WordPress Hook 系统使得 WordPress 有可能转变成强大的网络应用，无论是 WooCommerce 的电子商务商店，bbPress 的论坛，还是 BuddyPress 的社交网站。

在本教程的第 2 部分，我将分享一些很酷但鲜为人知的关于 WordPress 中 hook 系统的事实，比如如何:在类中使用它们，将静态和非静态方法与动作和过滤器挂钩，使用名称空间，注意事项和解决方案等等。所以敬请关注，祝编码愉快。

## 分享这篇文章