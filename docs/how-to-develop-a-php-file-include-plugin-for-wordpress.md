# 如何为 WordPress 开发一个 PHP 文件包含插件

> 原文：<https://www.sitepoint.com/how-to-develop-a-php-file-include-plugin-for-wordpress/>

WordPress 可以节省开发者几周的时间。当 CMS 提供如此多的功能时，很难证明编写自己的 CMS 是正确的。也就是说，有时候你想注入自己的 PHP 代码；可能是一个自定义表单或由另一个系统生成的报告。

要在每一个 WordPress 页面或帖子上插入你的代码，你可以简单地给 page.php 或 single.php 添加一个 PHP include()函数

```
 include('mycode.php'); 
```

假设 mycode.php 位于你的主题文件夹(WP-content/themes/*theme-name*)中，它将被包含并执行。

但是，如果您需要在特定的帖子/页面上或内容块的中间包含一个文件，该怎么办呢？我们需要一个 WordPress 插件

## 创建插件

在你的插件文件夹(wp-content/plugins)中创建一个名为**php-include.php**的新文件，并添加一个标题，这样它可以被 WordPress 识别:

```
 <?php
/*
Plugin Name: PHP File Includer
Plugin URI: https://www.sitepoint.com/
Description: Include PHP files using a shortcode
Version: 1.0
Author: Craig Buckler
Author URI: http://optimalworks.net/
License: Use this how you like!
*/ 
```

接下来是我们的主要函数 PHP_Include()。它需要一个参数数组，尽管我们只使用了一个文件。如果没有通过，文件设置为“默认”:

```
 // include PHP file
function PHP_Include($params = array()) {

	extract(shortcode_atts(array(
	    'file' => 'default'
	), $params));

	ob_start();
	include(get_theme_root() . '/' . get_template() . "/$file.php");
	return ob_get_clean();
} 
```

该函数假设您正在请求一个驻留在主题文件夹中的文件。最后三行包含文件，并使用 PHP 的输出缓冲函数返回其执行内容。

**warning:** With great power…

…随之而来的是巨大的责任。这个插件可能很小，但它允许任何人执行任意代码。如果你是自己博客的唯一编辑，这不成问题，但你应该警惕其他用户。

或者，您可以修改 include 语句的位置，或者只允许具有特定名称模式的文件，例如“/include/mycode-$file.php”。这应该可以防止用户包括任何 PHP 文件。

最后，我们将把我们的函数注册为短代码处理程序:

```
 // register shortcode
add_shortcode('phpinclude', 'PHP_Include'); 
```

保存文件并在你的 WordPress 控制面板中激活插件。

## 包括一个 PHP 文件

现在可以将以下短代码添加到任何页面或帖子中:

```
 [phpinclude file='mycode'] 
```

假设 mycode.php 存在于你的主题文件夹中，它将被插入到内容中的那个位置。

希望你觉得有用。

## 分享这篇文章