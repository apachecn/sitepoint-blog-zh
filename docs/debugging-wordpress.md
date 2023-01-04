# 调试 WordPress: 11 个强大的技巧和技术

> 原文：<https://www.sitepoint.com/debugging-wordpress/>

对于任何开发人员来说，调试都是一项基本技能。本教程将向你展示 11 种调试 WordPress 和 PHP 错误的有效方法。

列表中的第一项是著名的 WP_Debug，然后我们将跳转到一些更高级的方法。

![Debugging WordPress](img/d424205e86c74937b6c850af2fc0a878.png)

首先，让我们列出常见 PHP 错误的类型:

注意:这是你在 PHP 中看到的最不重要的错误信息。这不一定意味着有问题，但有可能提出改进建议。

示例:null 元素传递给了需要字符串的函数。

警告:这是一个更严重的错误，但不会导致脚本终止。

示例:为 include()提供一个不存在的文件。

致命错误:这是一个危险的信号，表明事情真的出错了，脚本终止了。

示例:调用不存在的函数。

## 1–WP _ DEBUG

WordPress 有一个全局常量来指定所需的调试级别:WP_DEBUG。除了这个常量，还有另外两个重要的常量:WP_DEUBG_DISPLAY 和 WP_DEBUG_LOG。

WP_DEBUG 用于设置调试模式的开或关。WP_DEUBG_DISPLAY 显示或隐藏错误。最后，WP_DEBUG_LOG 将错误信息保存在 wp-content/debug.log 中。

这三个全局常量可以在 wp-config.php 中设置为 true 或 false，如下所示:

```
define("WP_DEBUG", true);

define("WP_DEBUG_DISPLAY", true);

define("WP_DEBUG_LOG", true); 
```

## 2–is _ WP _ error()

另一个 WordPress 内置的调试工具是 is _ WP _ error()；。它是一种检查某个结果是否属于 WP_Error 类型的方法。WP_Error 实际上是当 WordPress 方法失败时您应该收到的返回对象。

示例:

```
$post = array(
    'post_title'    => 'Test post',
    'post_content'  => 'This is my post.',
    'post_status'   => 'publish',
    'post_author'   => 1
);
$result = wp_insert_post( $my_post );

if(is_wp_error($result)){
    echo $return->get_error_message();
} 
```

上面的代码将尝试使用 wp_insert_post()添加一个新帖子。如果该方法失败，将返回 WP_Error 对象。然后，您可以捕捉并获得错误消息。

## 3–调试栏

另一个调试 WordPress 错误的有用工具是[调试栏](https://wordpress.org/plugins/debug-bar/)。这是一个非常方便的工具，可以获取网站中每个页面的有用信息。

安装后，你会发现一个新的调试按钮。当你点击它，一个查询分析，模板，PHP 安装，和许多其他有用的信息。

## 4–测试网站

将网站从测试和开发中分离出来是非常重要的。我通常在我的网站上安装两个 WordPress。这很重要，因为您不希望在打开错误报告时脚本终止。

## 5–简单展示挂钩

简单地显示钩子是一个很好的插件，可以显示任何页面上运行的每个钩子。如果您遇到所有错误报告思想都不起作用的情况，您将会遇到这种情况，那么就到了取出所有正在运行的钩子的时候了。

这个插件会告诉你在任何页面上运行的操作或过滤挂钩。然后，您可以开始分析和调试每个钩子代码。您还可以看到每个钩子所附带的方法。甚至找出每个的优先级。

## 6–WPDB 错误报告

如果您使用 wpdb 类来处理数据库，那么您将总是需要错误报告。无论是为了确保查询正确运行，还是为了显示错误消息以便调试。

示例:

```
global $wpdb;
// Before running your query:
$wpdb->show_errors = TRUE;
$result = $wpdb->get_results("SELECT field_value FROM table_name");
if(! $result){
    $wpdb->print_error();
    // Or you can choose to show the last tried query.
    echo $wpdb->last_query;
} 
```

## 7–服务器错误日志

在某些时候，WordPress 和 PHP 都不能捕捉一些编码错误。例如，如果您的脚本超过了最大允许运行时间，您将不会得到 PHP 错误消息。相反，Apache(或您的服务器安装系统)会弹出类似“内部服务器错误”的消息

这是你应该查看你的错误日志的时候了，看看你的 PHP 代码，或者你的 WordPress 安装的某个部分做错了什么。

你可以参考你的主机提供的日志存储位置。通常是日志文件夹下的东西。

## 8–PHP 错误记录

PHP 有自己的错误报告级别来存储解决 WordPress 之外的问题。这是非常有用的，尤其是当 WordPress 之外的东西正在运行并导致一些问题的时候。

您可以从配置 php.ini 文件来打开错误报告开始，然后选择存储这些消息的位置。

```
error_reporting = E_ALL | E_STRICT 
error_log = /var/log/php_error.log 
```

设置上面两行将打开错误报告，并将错误设置为保存在指定的路径。

也可以运行 phpinfo()；并检查 error_log 选项。

## 9–PHP 语法检查器

如果你的主机提供商限制了对 php.ini 文件的访问，或者你不能访问错误日志，事情就有点难了。但是，有许多工具可以克服当你只得到一个没有错误信息的空白页面的情况。一个快捷的工具是 PHP 代码检查器。

PHP 代码检查器是一个语法错误检查工具。如果你遗漏了一个分号或者一个花括号，并且找不到你遗漏的地方，这真的很有用。

## 10-PHP 到

如果 PHP 代码检查器没有发现语法错误，那么你将需要一个更强大的工具。像 PhpStorm 这样强大的 IDE 将是更高级调试的答案，并将代码分解成几个部分。

在这种情况下，当你在一个变量中存储一个字符串，并试图打印它，但什么也没发生。使用 IDE 可以发现你做错了什么，比如在代码中重写这个变量。这就是为什么强烈建议依赖一个强大的 IDE，比如 PhpStorm、Eclipse 或任何您喜欢的工具。

## 11–禁用浏览器缓存

如果您在网站的前端工作，那么您需要禁用浏览器缓存。这是必要的，因为如果您正在处理您的 JavaScript 代码，并且它没有更新，或者更糟糕的是，更新了但是没有显示新的错误，那么您需要禁用缓存。

禁用缓存意味着你要求你的浏览器停止处理你网站的旧文件。这包括 CSS 和 JavaScript 文件。

要禁用 Google Chrome 浏览器缓存，右键单击任何 web 元素打开 DevTools。然后，点击工具栏中的网络选项卡。最后，选中顶部的禁用缓存复选框。

## 结论

以上 11 个提示可以作为你调试过程的指南。虽然调试很多时候会很繁琐，但是上面的小技巧可以让整个过程轻松很多。

如果你有任何想要分享的 WordPress 调试技巧，请让我知道，请在下面的评论中这样做！

## 分享这篇文章