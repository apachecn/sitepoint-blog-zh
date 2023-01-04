# 让你的 WordPress 插件对开发者友好

> 原文：<https://www.sitepoint.com/making-your-wordpress-plugins-developer-friendly/>

WordPress 插件通常由两类人使用:WordPress 网站所有者和开发者。满足两组用户的需求是很重要的。

开发人员可能是具备 WordPress / PHP 基础知识的人，他们想修补你的插件。他们也可能是有经验的 WordPress 开发者，想要使用你的插件为客户的网站提供特定的功能。

如果你想了解 WordPress 插件开发的介绍，Tim Smith 的文章 [WordPress 插件开发](https://www.sitepoint.com/wordpress-plugin-development/)值得一读。

![WordPress Logo](img/7c81df121ce03113683306b6ad807c97.png)

### 为什么要让我的 WordPress 插件对开发者友好？

主要原因是你将有助于防止开发者不得不编辑核心插件文件来实现他们需要的功能。

反过来，这减少了支持请求的数量和潜在的来自开发者的挫折，他们做出了改变，却发现他们的改变在插件更新中被恢复。

开发人员友好的插件也意味着你更有可能从更有经验的开发人员那里得到有价值的反馈，他们可能已经扩展了你的插件的功能，并且很乐意提供他们的代码增强。这允许你的插件自然增长，变得更加稳定，安全，对更多的用户有用。

有几种方法可以让你的 WordPress 插件对开发者友好，今天我们将讨论如何使用 WordPress 的动作和过滤器。

### 动作和过滤器:入门

WordPress 的核心特性之一是它的插件 API。具体来说，它提供了过滤器和动作，也称为钩子，在特定的地方“钩”进 WordPress 的核心。

#### 行动

动作允许我们在特定的时间运行特定的功能。例如，如果我们想在有人试图找回他们的密码时发送一封电子邮件，我们可以使用(或“挂钩”到)动作`retrieve_password`:

```
add_action( 'retrieve_password', 'my_plugin_retrieve_password' );
```

`add_action`的第一个参数告诉 WordPress 我们想要挂钩哪个动作。第二个参数告诉 WordPress 当执行`retrieve_password`动作时调用哪个函数——在这个例子中，我们调用`my_plugin_retrieve_password`函数。

因此，我们的代码将被更新，以包含一个在执行`retrieve_password`动作时执行某些操作的函数:

```
add_action( 'retrieve_password', 'my_plugin_retrieve_password' );
function my_plugin_retrieve_password( $user_login ) {
    wp_mail( 'someone@example.com', 'Reset Password Attempt', $user_login . ' attempted to request a password reset.' );
}
```

#### 过滤

过滤器的工作方式与动作类似，只是它们允许我们修改在例程中返回、输出或传递的数据。例如，如果我们希望总是阻止用户请求密码重置电子邮件，我们可以通过注册我们的过滤器来连接到`allow_password_reset`过滤器:

```
add_filter( 'allow_password_reset', 'my_plugin_allow_password_reset', 999, 2 );
function my_plugin_allow_password_reset( $enabled, $user_id ) {
    return false;
}
```

这会将 false 返回给 WordPress 中的主变量，该变量决定是否可以执行密码重置请求。

### 在插件中添加动作和过滤器

鉴于 WordPress 为开发者注册了大量的动作和过滤器，在你的插件中注册你自己的动作和过滤器也同样有用，开发者也可以使用。

有两种主要方法可以确定在哪里运行操作和过滤器:

#### 对新功能的常见请求

如果你的插件有很多对特定功能的请求，你可能会想简单地把它添加到你的插件中。然而，如果只有少数用户想要特定的功能，你可以在插件中添加你的动作/过滤器，然后生成可以在一个单独的、独立的插件中运行的代码，该插件与那些动作和过滤器挂钩。

这允许开发者和最终用户获得他们需要的功能，而不需要对你的插件核心做大的改动。这也是一个有用的模型，在把新特性合并到你的核心插件之前，把它们作为插件进行测试。

#### 代码扩展性

这个有点棘手。开发人员倾向于为短期的、直接的问题构建解决方案。优秀的开发人员还会考虑确保可扩展性，以便这些解决方案可以在未来重用、扩展或扩展。

无论你在哪里看到这种情况发生的可能性，但是你还没有一个具体的用例，是时候插入一个动作或者过滤调用了。

#### 真实世界的例子

WordPress to Buffer Pro 是我过去几年开发的一个插件。我收到越来越多的添加或删除功能的请求——具体来说，一些开发人员希望他们的插件最终用户有更少的调度选项，而一些则希望更多。

为了满足所有开发人员及其最终用户的需求，我构建了以下函数:

```
/**
 * Helper method to retrieve schedule options
 *
 * @since 3.0
 *
 * @return array Schedule Options
 */
static public function get_schedule_options() {

    // Build schedule options
    $schedule = array(
        'queue_bottom'  => __( 'Add to End of Buffer Queue', 'wp-to-buffer-pro' ),
        'queue_top'     => __( 'Add to Start of Buffer Queue', 'wp-to-buffer-pro' ),
        'now'           => __( 'Post Immediately', 'wp-to-buffer-pro' ),
        'custom'        => __( 'Custom Time', 'wp-to-buffer-pro' ),
    );

    // Return filtered results
    return apply_filters( 'wp_to_buffer_pro_get_schedule_options', $schedule );

}
```

这里的关键代码是最后一行，我们在这里返回`apply_filters`调用的结果。通过引入`apply_filters`，WordPress 将检查 WordPress 主题或其他插件中任何已注册的过滤器，这些过滤器是使用`add_filter`注册的。

如果发现任何过滤器，它们将在该点被执行。

因此，虽然调度选项的数组默认为 4，但开发人员现在可以构建自己的函数，通过`add_filter`注册它，并选择只返回 2 个选项:

```
add_filter( 'wp_to_buffer_pro_get_schedule_options', 'my_plugin_get_schedule_options', 10, 1 );
function my_plugin_get_schedule_options( $schedule ) {

    // Remove the 'custom' option from the scheduling options
    unset( $schedule['custom'] );
    return $schedule;

}
```

`add_filter`调用末尾有两个数字参数:

*   **10** :这是运行该过滤器的优先顺序。如果针对此特定筛选器注册了多个筛选器，则它们将按优先级从低到高的顺序运行。
*   **1** :这是指函数调用可以预期接收的参数个数。总会有至少一个参数——我们正在修改的变量——但在某些情况下，过滤器也可能提供额外的变量/数据。在这种情况下，我们会相应地增加这个数字。

动作以类似的方式工作；只需将`do_action()`调用添加到您的插件代码中，您希望开发人员可以随意地将他们自己的操作挂接到:

```
function send_emails( $emails ) {
    // Some code here...

    // Allow developers to perhaps send their own emails too
    do_action( 'send_emails', $emails );
}
```

开发人员可以通过以下方式加入到这个活动中:

```
add_action( 'send_emails', 'my_plugin_send_emails', 10, 1 );
function my_plugin_send_emails( $emails ) {
    wp_mail( 'someone@example.com', 'Subject', 'Message' );
}
```

### 结论

我们发现了 WordPress 是如何允许开发者在 WordPress 核心中挂钩关键动作和修改变量的，而不需要对核心代码进行代码修改。由此，我们将动作和过滤器的原则扩展到我们自己的插件中，以展示我们如何允许开发者修改和扩展我们的插件代码，而无需对我们的核心插件进行更改。

下一次，我们将研究如何记录动作和过滤器，以及如何提供更多开发人员友好的文档。

## 分享这篇文章