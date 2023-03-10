# 准备好你的插件翻译

> 原文：<https://www.sitepoint.com/wordpress-i18n-make-your-plugin-translation-ready/>

在之前的一篇文章中，我介绍了[WordPress 国际化的基础知识](https://www.sitepoint.com/wordpress-i18n/)(缩写为`i18n`)；如何安装本地化版本的 WordPress，以及如何将现有的 WordPress 站点轻松转换为本地化版本。

在这篇文章中，我将带你了解 WordPress 插件的国际化过程。这个过程并不困难，一旦掌握了知识，你就可以很容易地将你的 WordPress 插件翻译成其他语言。

## 国际化和本地化的区别

多年来，开发人员往往会误解这些术语的含义—`Internationalization`和`Localization`。

*   国际化是开发你的插件的过程，因此它可以很容易地被翻译成其他语言。
*   **本地化**描述了将国际化插件翻译成新语言的后续过程。

值得注意的是，`Internationalization`常缩写为`i18n`(因为‘I’和‘n’之间有 18 个字母，`Localization`缩写为`l10n`(因为‘l’和‘n’之间有 10 个字母。)

## 为什么要国际化？

答案很简单；WordPress 在世界各地以多种不同的语言使用。当插件国际化时，它们会吸引来自世界其他地方的大量观众，这些观众显然会通过使用他们自己语言的插件而受益。

作为开发人员，你可能没有时间提供插件的本地化版本，因为你不会说其他语言。然而，当您国际化您的插件时，您为其他人打开了创建本地化的大门，而不必修改源代码。

## 插件国际化

现在，我们已经熟悉了插件国际化和本地化的概念，让我们深入到让插件为翻译做好准备的过程中。

### 设置翻译标题

使插件可翻译的第一步是在插件头中包含翻译头。

翻译头是`Text Domain`和`Domain Path`。

`Text Domain`用来表示属于一个插件的所有文本。这是一个唯一的标识符，确保 WordPress 能够区分所有加载的翻译。这增加了可移植性，可以更好地使用现有的 WordPress 工具。

文本域必须与插件的 slug 相匹配。例如，如果你的插件是一个名为`sample-plugin.php`的文件，或者它包含在一个名为`sample-plugin`的文件夹中，那么*文本域*应该是`sample-plugin`。

#### 关于文本域的一个注记

文本域名必须使用破折号，而不是下划线。

记得我说过文本域必须与插件匹配吗？那可能终究不是真的。我用我的一个插件进行了一个快速实验，我使用了一个独特的文本，而不是插件 slug，它工作起来没有任何问题。

寓意:确保`Text Domain`是独一无二的，这样它就不会和其他插件冲突。

`Domain Path`是 WordPress 将搜索`.mo`翻译文件的文件夹。

默认情况下，WordPress 在插件目录中搜索要使用的翻译文件。将翻译文件放在插件的根文件夹中会打乱你的插件结构。

例如，如果您希望将翻译文件保存在一个文件夹中；`/languages`，你需要使用`Domain Path`头通知 WordPress。

下面是一个 WordPress 插件的典型标题，包括翻译。

```
<?php
/*
 Plugin Name: Enable Shortcode and PHP in Text widget
 Plugin URI: http://w3guy.com/shortcode-php-support-wordpress-text-widget/
 Description: Enable shortcode support and execute PHP in WordPress's Text Widget
 Author: Agbonghama Collins
 Version: 1.2
 Author URI: http://w3guy.com
 Text Domain: espw-plugin
 Domain Path: /languages/
 */
```

### 加载文本域

现在，我们将使用 [load_plugin_textdomain()函数](http://codex.wordpress.org/Function_Reference/load_plugin_textdomain)告诉 WordPress 加载用户语言的翻译文件。

下面是功能简介。

```
<?php load_plugin_textdomain( $domain, $abs_rel_path, $plugin_rel_path ) ?>
```

第一个参数`$domain`应该是文本域；`$abs_rel_path`已被弃用，应设置为`false`；最后，`$plugin_rel_path`是翻译文件的相对路径。

如果翻译维护对象文件位于插件自己的目录中，请按如下方式使用:

```
load_plugin_textdomain( 'espw-plugin', false, dirname( plugin_basename( __FILE__ ) ) );
```

如果翻译维护对象文件在插件的语言子目录中。按如下方式使用:

```
load_plugin_textdomain( 'espw-plugin', false, dirname( plugin_basename( __FILE__ ) ) . '/languages/' );
```

你不只是调用`load_plugin_textdomain`函数，它应该早在`plugins_loaded`动作时就在你的插件中被调用，就像这样:

```
function load_plugin_textdomain() {
  load_plugin_textdomain( 'espw-plugin', FALSE, basename( dirname( __FILE__ ) ) . '/languages/' );
}

add_action( 'plugins_loaded', 'load_plugin_textdomain' );
```

### 深入探究插件 I18n

既然文本域和域路径头已经设置好了，是时候学习如何国际化插件了。

教程的这一部分将分为以下几个部分:

1.  字符串翻译
2.  使用占位符
3.  HTML 翻译
4.  处理复数
5.  通过上下文消除歧义
6.  转义翻译字符串

请注意:在本教程中，字符串`espw-plugin`将被用作文本域。

#### 1.字符串翻译

要使一个字符串在插件中可翻译，将原始字符串包装在一个`__()`函数调用中，如下所示:

```
$text =  __( 'Hello, SitePoint Readers!', 'espw-plugin' );
```

如果您想将字符串回显到浏览器，而不是使用`echo`语言结构，请使用`_e`函数:

```
_e( 'Hello, SitePoint Readers!', 'espw-plugin' );
```

#### 2.使用占位符

作为 PHP 和 WordPress 开发者，我假设你知道什么是`placeholders`。你可以快速浏览一下 [sprintf](http://php.net/sprintf) 和 [printf()](http://php.net/manual/en/function.printf.php) PHP 文档以获得更多信息。

如果您在字符串中使用变量，如下例所示，您应该使用占位符。

```
echo 'Your city is $city.'
```

正确的方法是如下使用`printf()`功能:

```
printf(
    __( 'Your city is %s.', 'espw-plugin' ),
    $city
);
```

浏览 WordPress 插件库中托管的一些插件的代码，我确实看到了这样的事情:

```
echo __('Your city is $city', 'espw-plugin');
```

```
_e('Your city is $city', 'espw-plugin');
```

尽管字符串现在是可翻译的，PHP 变量`$city`也是可翻译的。

这是一种不好的做法，因为翻译人员可能会错误地改变变量或将恶意代码注入插件代码库，这最终会使插件出现故障。

`sprintf`函数与`printf`类似，它们使用占位符格式化字符串，而`printf`输出格式化字符串，`sprintf`返回字符串。

示例:以下代码将格式化字符串赋给变量`$text`。

```
$text = sprintf( __('Your city is %s.', 'espw-plugin'), $city );
```

#### 3.HTML 翻译

在可翻译的字符串中包含 HTML 取决于上下文。

一个例子是链接(与其周围的文本分开):

```
<div class="site-info">
  <a href="http://wordpress.org/" >< ?php _e( 'Proudly powered by WordPress.', 'espw-plugin' ); ?></a>
</div>
```

另一个例子是段落中的链接(不与它周围的文本分开):

```
<?php
$url = 'http://example.com';
$link = sprintf( __( 'Check out this link to my <a href="%s">website</a> made with WordPress.', 'espw-plugin' ), $url );
echo $link;
?>
```

#### 4.处理复数

可以使用`_n()`函数国际化当项目数量改变时改变的字符串。

该函数接受 4 个参数，即:

*   **单数**–字符串的单数形式
*   **复数**–字符串的复数形式
*   **Count**–对象的数量，这将决定应该返回单数还是复数形式
*   **文本域**–插件文本域

让我们看一些例子来理解`_n()`函数是如何工作的。

英语中有`"One comment"`和`"Two comments"`。在其他语言中，你可以有多个复数形式。

下面的代码演示了如何使用`_n()`函数处理这种情况。

```
printf(
    _n(
        'One comment',
        '%s comments',
        get_comments_number(),
        'espw-plugin'
    ),
    number_format_i18n( get_comments_number() )
);
```

**代码解释**上面的代码由这三个函数组成——`printf`、`_n`和`number_format_i18n`。

为了便于理解，将对功能代码进行剖析，并解释每个功能组件。

```
_n(
        'One comment',
        '%s comments',
        get_comments_number(),
        'espw-plugin'
    )
```

传递给`_n`函数的第一个参数是注释数量为单数时要显示的文本。

第二个是评论数大于 1 时显示的文本。

占位符`%s`将包含`number_format_i18n( get_comments_number() )`的值，稍后将对其进行解释。

假设第三个参数`get_comments_number()`是一个返回注释计数的函数。

如果返回`1`，第一个参数`One comment`由`printf`输出，否则第二个参数`%s comments`如果大于`1`则返回。

请注意:占位符`%s`被传递给`printf`函数的第二个参数`number_format_i18n( get_comments_number() )`返回的整数所代替。

最后，第四个参数是翻译*文本域*。

函数`number_format_i18n()`将注释计数转换为基于地区的格式。更多信息参见[文档](http://codex.wordpress.org/Function_Reference/number_format_i18n)。

与`number_format_i18n()`相似的是 [date_i18n](http://codex.wordpress.org/Function_Reference/date_i18n) ，它基于时间戳以本地化格式检索日期。

还是关于`_n()`函数，下面是该函数如何工作的另一个演示。

```
printf( _n( 'We deleted one spam message.', 'We deleted %d spam messages.', $count, 'my-text-domain' ), $count );
```

如果变量`$count`返回`1`，将显示文本`We deleted one spam message`；但如果大于`1`，将显示`We deleted %d spam messages`，占位符`%d`被整数值`$count`所取代。

#### 5.通过上下文消除歧义

有时一个术语在几个上下文中使用，尽管它在英语中是同一个词，但在其他语言中必须有不同的翻译。

例如，单词`Post`既可以用作动词`"Click here to post your comment"`，也可以用作名词`"Edit this post"`。

在这种情况下，应使用 [_x](http://codex.wordpress.org/Function_Reference/_x) 或 [_ex](http://codex.wordpress.org/Function_Reference/_ex) 功能。

它类似于`__()`和`_e()`，但是它有一个额外的参数——`$context`。

```
_x( 'Post', 'noun', 'espw-plugin' );
_x( 'Post', 'verb', 'espw-plugin' );
```

在这两种情况下使用这种方法，我们将得到原始版本的字符串`Comment`，但是翻译者将看到两个用于翻译的`Comment`字符串，每个都在不同的上下文中。

当通过函数`_x()`变得可翻译的字符串被诸如 [Poedit](http://www.poedit.net/) 之类的翻译工具解析时，上下文参数向翻译者提供了使用字符串/文本的上下文的提示。

在德语中，作为名词的 Post 是`Beitrag`，而作为动词的 Post 是 verbuchen。

下面是 Poedit 将字符串`Post`翻译成德语的截图，上下文用方括号括起来。

![Poedit Translating to German](img/30e9053366421341f9da724301147721.png)

当`_x()`检索翻译后的字符串时，`_ex()`显示它。

#### 6.转义翻译字符串

WordPress 有很多功能用于验证和净化数据。

列表中有用于转义翻译文本的函数-ESC _ html()、esc_html_e()、esc_html_x()、esc_attr()、esc_attr_e()和 esc_attr_x()。你可以在 WordPress Codex 获得更多关于这些功能的信息。

我不需要解释其中的每一个，但它们所做的基本上是逃避可翻译的文本。

## 包裹

WordPress 的目标之一是让世界各地的用户轻松发布内容。作为插件开发人员，当您国际化插件时，您可以帮助进一步简化用户的发布过程。

本教程的第一部分基本上是关于你需要知道的关于插件 i18n 的一切。

最后一部分将是如何准备好插件翻译，以及学习如何将插件本地化为一种新语言的演练。

我真的希望你从这篇教程中学到了一些新的东西。

编码快乐！

## 分享这篇文章