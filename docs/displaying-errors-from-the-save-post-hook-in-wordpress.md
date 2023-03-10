# 在 WordPress 中显示来自 save_post 钩子的错误

> 原文：<https://www.sitepoint.com/displaying-errors-from-the-save-post-hook-in-wordpress/>

尽管存在`WP_Error`和所有需要的部分，但是没有单一的“WordPress 方式”来处理和显示错误。您可以通过返回`WP_Error`对象来指出函数或方法中发生的错误(这是我在工作中常用的方式)，但是一旦我们这样做了，我们如何处理这些错误对象呢？

![WordPress Errors](img/e2baae0f04277b4680bacf584b47bce6.png)

在 AJAX 方面很简单:使用`wp_send_json_{error/success}`并相应地处理响应。然而，生成的错误需要显示给用户的一个常见区域是在`save_post`钩子上，它实际上比看起来稍微复杂一些。

原因在于 WordPress 处理保存的方式。如果你在一个类似于`post.php?post=1234`的页面上，你做了编辑并点击**保存**，WordPress `POST`将信息发送给`post.php`。在`save_post`钩子触发后，所有该做的都完成了，然后*将*重定向回`post.php?post=1234`编辑器页面。

这使事情变得困难，因为重定向意味着加载编辑器页面的执行线程与保存文章的线程不同，我们不再能够访问保存时的全局变量。因此，我们需要一种方法将信息从操作传递到我们被重定向到的页面。

让我们看一下解决这个问题的 3 种可能的方法。我将解释如何实现每一个，指出它们的优缺点，并解释它们最适合什么样的环境。

## 参考样板文件

在我们开始之前，我将使用`admin_notices`钩子来显示错误，如下所示:

```
add_action( 'admin_notices', 'my_error_message' );
```

这个钩子给了我们一个在页面标题上方显示消息的地方。我将详细介绍每种方法的`my_error_message`函数。我也将忽略你在`save_post`钩子中决定做的任何事情，只假设你在钩子的末尾有一个`$error`，如果没有错误，它是`false`，如果有错误，它是`WP_Error`对象。执行流程如下所示:

```
add_action( 'save_post', 'my_save_post_function' );
function my_save_function( $post_id ) {
    $error = false;

    // Do stuff.

    if ($something_went_wrong) {
        $error = new WP_Error($code, $msg);
    }

    if ($error) {
        // Handle error.
    }

    return true;
}
```

用`$post_id`发生了一些事情(没有画出来)，如果出了问题，我们将创建一个`WP_Error`对象来处理它。

## 保存在`$_SESSION`中

PHP(实际上还有 WordPress)因其全球性而闻名。类似于`$_GET`和`$_POST`变量保存那些 HTTP 动词的数据，`$_SESSION`全局变量保存当前会话的数据。`$_SESSION`数据保存在已配置的会话存储中(例如 memcache、Redis、文件系统),并通过 cookie 绑定到用户的会话。我们可以用它来保存和检索我们的错误消息。

### 它是如何工作的

首先，你必须启动会话，因为 WordPress 本身不使用会话:

```
if ( !session_id() ) {
    session_start();
}
```

这应该在 WordPress 生命周期的早期就被挂钩，尽管我认为如果你愿意，你可以在`save_post`挂钩本身中开始会话。然后，我们将错误保存到会话中:

```
if ($error) {
    $_SESSION['my_plugin_errors'] = $error->get_error_message();
}
```

最后，在`admin_notices`钩子中:

```
if ( array_key_exists( 'my_plugin_errors', $_SESSION ) ) {?>
    <div class="error">
        <p><?php echo $_SESSION['my_plugin_errors']; ?></p>
    </div><?php

    unset( $_SESSION['my_plugin_errors'] );
}
```

我们从会话中提取错误消息，并将其输出到页面标题上方。

### 利弊

从好的方面来看，这个解决方案很容易实现，并且不需要像其他两个解决方案那样访问数据库。它的主要问题是: *WordPress 不使用会话*。它被设计成“无状态的”，所以它不是为了在操作过程中维护信息而设计的，如果一些用户没有会话存储，这会给他们带来问题。从根本上来说，这不是“WordPress 的方式”,也与项目的哲学背道而驰，但是如果你正在为自己或客户构建一个工具，在这里你可以完全控制环境，这是一个简单而轻量级的解决方案。

## 暂时保存它

瞬态是一个 WordPress 缓存 API。它允许您保存任何类型的有截止日期的信息，就像在任何其他缓存中一样。在幕后，瞬态 API 将尝试将您的数据保存到对象缓存，*(如果您启用了持久缓存*)。如果没有，它会将您的数据保存到`wp_options`表中。这样，无论站点的后端如何配置，都有一个(某种程度上)可靠的 API 来保存短期数据。它为你抽象了很多辛苦的工作。

一个警告:当我们获得数据时，数据应该在那里(并且在我们保存它的短时间内，它几乎总是会在那里)，但有理由它可能会丢失，特别是当它被保存到对象缓存时。你不能假设数据会一直存在。如果您绝对需要持久保存这些信息，您应该直接将其保存到数据库中。

### 它是如何工作的

我们不需要启动任何东西来开始瞬变。当您得到一个错误时，只需用信息设置一个 transient(您需要预先获取`$user_id`):

```
if ($error) {
    set_transient("my_save_post_errors_{$post_id}_{$user_id}", $error, 45);
}
```

[这里是那个函数](https://developer.wordpress.org/reference/functions/set_transient/)的引用。请注意，最后一个参数是瞬变持续的秒数。45 秒的时间应该足够保存和获取数据，如果不是这样，一定是出了严重的问题。

然后，在`admin_notices`钩子中:

```
if ( $error = get_transient( "my_save_post_errors_{$post_id}_{$user_id}" ) ) { ?>
    <div class="error">
        <p><?php echo $error->get_error_message(); ?></p>
    </div><?php

    delete_transient("my_save_post_errors_{$post_id}_{$user_id}");
}
```

我们从缓存中获取`WP_Error`并显示它的消息。别忘了自己收拾干净！

### 利弊

从好的方面来说，这是一种 WordPress 友好的管理这些错误的方式，是为缓存这样的东西而设计的。数据将被自动清除，即使您忘记自己清除，因此它为您提供了一种健全检查，清除或忽略陈旧的错误消息。这种解决方案的主要问题是，如果您没有安装对象缓存，它必须访问数据库，所以如果您在`save_post`钩子中做了很多工作，并且不想添加另一个查询，这可能不是理想的解决方案。

我还会提到你可以用同样的方法处理`post_meta`。如果我没记错的话，这可能不会添加额外的查询，因为它只是为帖子的查询添加了一个语句，但我不确定。将`post_meta`用于临时数据并不是它的理想目的，但是如果它能提升性能，那就值得了。那就一定要确保自己清理干净，因为这不会像`post_meta`和`transients`做的那样为你完成。

## 向重定向添加一个`GET`参数

第三种方法实际上是 WordPress 显示“更新后”消息的方式:用一个`$_GET`参数。在 WordPress 的实例中，它将`message`的值设置为一个数字，并使用该数字来显示特定的消息。您可以做类似的事情，将`WP_Error`的代码，而不是它的完整信息，作为查询变量添加到 URL 中。

在我们的`save_post`钩子的末尾，我们将错误代码添加到 URL 参数中:

```
if ($error) {
    add_filter('redirect_post_location', function( $location ) use ( $error ) {
        return add_query_arg( 'my-plugin-error', $error->get_error_code(), $location );
    });
}
```

直到 PHP 5.3 才添加了匿名函数，WordPress 支持 5.2+，所以在这里使用它们可能会使你失去使用这种方法的资格，但是这对于它们来说是一个非常理想的用例，从上下文中提取变量并在过滤器中使用它。

然后，我们根据代码在 admin_notices 挂钩中显示错误消息:

```
if ( array_key_exists( 'my-plugin-error', $_GET) ) { ?>
    <div class="error">
        <p>
            <?php
                switch($_GET['my-plugin-error']) {
                    case 'an_error_code':
                        echo 'The post failed to save because problems.';
                        break;
                    case 'another_error_code':
                        echo 'The post failed to save because reasons.';
                        break;
                    default:
                        echo 'An error ocurred when saving the post.';
                        break;
                }
            ?>
        </p>
    </div><?php
}
```

### 利弊

最大的好处是你永远都不需要访问数据库。所有的错误代码都存储在内存中，并在内存中检索，这使得它比其他的性能更好。不利的一面是，您必须在`WP_Error`对象的实例化和 switch 语句中复制消息。你不能用一个常量，或者一组变量，或者类似的东西，因为这样字符串就不能被翻译了。如果您的错误代码数量有限，这应该不是一个问题，但是如果您的应用程序变得很大，长期维护可能会更加困难。

## 结论

在各种情况下，这三种方法中的每一种都更好或更容易实现。我喜欢使用瞬态来跨页面负载传递数据，尤其是在显示用户需要看到并可能做出反应的错误消息时。在所有三个变量中，查询变量的开销最低，当您的应用程序只生成少量错误时，它非常有用。虽然 WordPress 并不真正支持这个会话，但它是在其他应用程序中向用户显示消息的常用方法。

我希望这些显示错误的潜在方法对您有用。如果你有任何问题，请给我留言。

## 分享这篇文章