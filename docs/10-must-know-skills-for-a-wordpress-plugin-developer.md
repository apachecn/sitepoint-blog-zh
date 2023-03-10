# WordPress 插件开发者的 10 个必备技能

> 原文：<https://www.sitepoint.com/10-must-know-skills-for-a-wordpress-plugin-developer/>

WordPress 是目前最强大的 CMS 框架。它可以用来创建高度灵活和可扩展的网站，只需很少的工作。我相信插件和主题结构是它作为 CMS 成功的主要原因。WordPress 官方网站上有大约 21000 个免费插件。

只要有可能，我总是喜欢创建自己的插件。推荐你学插件开发；作为一名 WordPress 开发者，你将能够非常容易地定制你的网站，并且赚很多钱。在这篇文章中，我将讨论关于 WordPress 插件开发你需要知道的最基本的事情；我假设你对这里的 WordPress 文件夹结构有基本的了解。

## 1.创建插件

第一步是在 **/wp-content/plugins** 文件夹中创建你自己的插件文件夹。一旦文件夹被创建，你应该把你的插件文件放在那个文件夹里。你应该为你的插件准备一个主文件。文件应该用简单的字母命名，用连字符(-)分隔单词。

*   样本文件名:**wp-multi-slider.php**

为了让 WordPress 识别你的插件，在你的主文件中应该有如下的注释结构。

```
 <?php 

/* Plugin Name:  Sample Name 
Plugin URI: https://www.sitepoint.com/tutorials/wordpress-plugin-url 
Description: Get email notifications when your favorite author publishes a post. 
Version: 1.0 
Author URI: http://www.sitepoint.com 
Author: Rakhitha Nimesh 
License: GPL2 
*/ 
```

您将能够在仪表板插件部分看到该插件，如下所示。

![](img/40a221559ddaa7861c0abe62eaecc631.png)

## 2.插件激活/停用

点击插件列表中的**激活链接**可以激活插件。在一个简单的插件中，你不需要在激活时做任何事情。但是一个高级插件需要初始化插件选项，创建插件表等任务。因此，让我们看看如何处理插件激活和停用。

### 插件激活挂钩

WordPress 提供了一个名为`register_activation_hook`的功能，它将在插件激活时被触发。我们可以使用如下所示的方法添加一个自定义函数在插件激活时执行。

```
 function wp_sample_activation() { 
} 
register_activation_hook(__FILE__, 'wp_sample_activation'); 
```

您必须将包含激活函数的文件路径作为第一个参数传递，将函数名作为第二个参数传递。

如果激活函数在主插件文件中，你可以使用上面代码所示的`__FILE__` 。您可以在激活函数中执行验证、初始化和创建表等任务。

### 插件停用挂钩

我们可以使用与激活类似的语法，用`register_deactivation_hook`来处理插件的停用。你可以在去激活功能中清理插件资源、选项和表格。

```
 function wp_sample_deactivation() { 

} 

register_deactivation_hook(__FILE__, 'wp_sample_deactivation'); 
```

## 3.创建自定义表格

WordPress 数据库的表格结构非常灵活，你可以使用可用的表格实现大多数定制功能。但是有些情况下，您可能希望包含更高级的系统，如购物车、任务管理系统或预订系统。

在这些情况下，您需要知道如何以及何时创建自定义表。首先，考虑你项目的需求，尝试使用 **wp_options 表**和**元表**来存储你项目的具体数据。如果您觉得上述表格的结构不足以实现所需的功能，请使用以下方法创建自定义表格。

```
 global $wpdb; 
$wpdb->query("DROP TABLE IF EXISTS {$wpdb->prefix}sample_table"); 
$sql1 = "CREATE TABLE {$wpdb->prefix}sample_table ( id int(11) NOT NULL AUTO_INCREMENT, 
                                                       activation_code varchar(255) NOT NULL, 
                                                       email varchar(75) NOT NULL, 
                                                       status int(11) NOT NULL, PRIMARY KEY  (id) ) 
         ENGINE=InnoDB AUTO_INCREMENT=1;"; 

require_once(ABSPATH . 'wp-admin/includes/upgrade.php'); 
dbDelta($sql1); 
```

首先，在创建表之前检查它是否存在。您可以根据自己的需求决定在激活时删除并创建该表。你可以看到我在表名前使用了`{$wpdb->prefix}` 。一般来说，WordPress 表格都有前缀`wp_.` 。这可以在安装数据库时更改；所以你不应该硬编码`wp_`作为前缀来增加灵活性。

**{$wpdb- >前缀}** 会给你为当前安装定义的前缀吗。因此，在创建新表名之前，请始终使用该语法。

尽管您可以使用 **$wpdb- >查询**函数来创建表，但建议使用 **dbDelta** 函数，因为它会比较当前的表结构。默认情况下不会加载它，所以您需要首先包含该文件。

## 4.包括脚本和样式

即使您可以在任何地方回显脚本和样式，也建议使用`wp_enqueue_script`函数添加脚本。该函数检查文件是否已经可用，以及与其他脚本的依赖关系。下面的例子说明了`wp_enque_script.`的有效使用

```
 add_action('wp_enqueue_scripts', 'sample_scripts'); 

function sample_scripts() { 
  wp_enqueue_script('jquery'); 
  wp_register_style('sample_style', plugins_url('styles.css', __FILE__)); 
  wp_enqueue_style('sample_style'); 
  wp_register_script('jqueryUICore', plugins_url('ui/jquery.ui.core.js', __FILE__),array(“jQuery”)); 
  wp_enqueue_script('jqueryUICore'); 
  $config_array = array(“sample_name”=>”sample_value”]); 
  wp_localize_script('jqueryUICore', 'sampleData', $config_array); 
} 
```

可以先用`wp_register_style`注册样式文件，用`wp_enqueue_style`包含文件。必须提供样式文件的唯一标识符和路径。然后使用`wp_enqueue_script`函数包含脚本。如果依赖于其他脚本，可以把它作为第三个参数来提。我使用了 **jQuery** 作为依赖。

最后，您可以通过使用`wp_localize_script` 函数添加要在特定脚本中使用的数据。你可以随时包含这些脚本，但是一定要使用`wp_enqueue_scripts`和`wp_enqueue_styles`函数。

确保在管理端使用`admin_enqueue_script` 动作而不是`wp_enqueue_script` 。

## 5.创建短代码

短码是预定义的代码块，您可以在任何地方使用。作为一个插件开发者，了解短代码是至关重要的，因为你可以用它们为自定义页面添加动态行为。

您可以使用以下语法创建短代码。

```
 add_shortcode("shortcode_name", "shortcode_function"); 
function shortcode_function() { 
  return “<input type=’button’ value=’Share’ /> “; 
}
```

给`add_shortcode` 功能分配一个短代码名称和功能。然后在函数中返回您希望在浏览器中显示的内容类型。上面的短代码创建了一个简单的 HTML 按钮。

使用页面、帖子或插件中的短代码，使用以下语法显示按钮:

**【短代码名称/】**

## 6.过滤内容

当你开发一个博客相关的插件时，考虑如何过滤文章或页面内容是很重要的。考虑下面的例子。

```
 function sample_content_filter($content) { 
  $banners = “HTML for banners”; 
  $author = “HTML for author info”; 
  return $banners.$content.$author; 
} 
add_filter( 'the_content', 'sample_content_filter' ); 
```

每次页面或帖子被查看时，内容都会被传递给该函数。你可以修改，添加或删除内容，如我上面所示。

你也可以使用 WordPress 条件标签来过滤特定页面的内容。以下代码过滤帖子详细信息页面上的内容。

```
 function sample_content_filter($content) {
  if(is_single()){ 
  return $banners.$content.$author; 
  } 
} 
```

## 7.使用 Ajax

理想情况下，你应该知道如何在 WordPress 中使用 Ajax 为用户提供交互式内容。jQuery 的 Ajax 功能是掌握这一点的简单方法。

```
 $.post("admin-ajax.php", { action:"sample_ajax_action" }, 
function(result, textStatus) { 
}, "json"); 
```

上述 Ajax 请求中最重要的是动作。它将在 WordPress 代码中用来识别请求。同样，所有的 Ajax 请求都应该被发送到**admin-ajax.php**文件。

```
 function sample_ajax_action() { 
echo json_encode($your_result_array); exit; 
} 
add_action('wp_ajax_nopriv_sample_ajax_action', 'sample_ajax_action'); 
add_action('wp_ajax_sample_ajax_action', 'sample_ajax_action'); 
```

你可以看到 Ajax 中使用的相同动作名称在这里使用了前缀 **wp_ajax_nopriv_** 和 **wp_ajax_** 。 **wp_ajax_** 用于登录用户， **wp_ajax_nopriv_** 用于未登录用户。

## 8.编写 SQL 查询

在 WordPress 中，为了防止 SQL 注入，我们必须考虑查询的安全性。在将用户数据应用于查询之前，我们可以使用`prepare`方法过滤用户数据。在处理之前，始终使用以下代码过滤用户提交的数据。

```
 $wpdb->query($wpdb->prepare("update wp_sample_table set status=1 where activation_code=%s and status=%d",$activationCode,$status)); 
```

如上所示，在 SQL 中执行之前，总是在最后给 SQL 和变量值分配转义字符来过滤数据。

## 9.添加选项框

WordPress 在内容创建界面中提供了一组默认的字段，如**标题**、**内容**、**图片**和**摘录**。我们需要自定义字段来添加额外的行为。尽管我们可以使用自定义字段部分，但它只提供文本框。如果我们需要复选框，单选按钮，下拉框等，我们需要添加单独的字段。

我们可以很容易地创建选项框来提供额外的字段，如下所示。

```
 add_action('add_meta_boxes', 'add_custom_fields_box'); 
function add_custom_fields_box() { 
  add_meta_box('custom_fields_box_id', 'Custom Info', 'display_custom_info_box', 'post', 'normal', 'high'); 
} 

function display_custom_info_box() { 
global $post; 
$html = "<table><tr><td>Custom Checkbox</td><td><input id='custom_checkbox' type='checkbox' name='custom_checkbox'  /></td></tr> <tr><td>Custom Selecy</td><td><select name='custom_select'  > <option>Option 1</option> </select></td></tr> <tr><td>Custom Upload</td><td><input id='custom_file' type='file' name='custom_file'  /></td></tr></table>"; 

echo $html; 

} 
```

字段的值将作为自定义字段保存到 **wp_options** 表中。您应该在选项字段名前加上一个**下划线**，以防止与定制字段部分重复。

## 10.为插件安全性使用随机数

安全性是创建 WordPress 插件的主要关注点。您不应该相信用户提供的数据，并且您总是需要在执行之前验证数据。WordPress 提供了一个叫做 **nonce** 的概念，它创建一个 nonce 值，或者一个表单生成时只使用一次的任意数字。然后，一旦表单被提交，我们就可以检查相同的 nonce 值，以确保它是一个有效的请求——否则就是无效的。

您可以使用以下代码创建一个 nonce 值:

```
 wp_nonce_field('sample_frm_nonce', 'sample_frm_nonce'); 
```

第一个参数是唯一的标识符，第二个参数将用作隐藏表单域名。提交表单后，您可以使用下面的代码验证 nonce 值。

```
 if (!isset($_POST['sample_frm_nonce']) || !wp_verify_nonce($_POST['sample_frm_nonce'], 'sample_frm_nonce')){
  return; 
} 
```

如果 nonce 未通过验证，则不会进一步处理请求。

WordPress 插件 API 非常庞大，要涵盖它所涉及的一切并不容易。我在这里所经历的只是你在插件创建过程中可能需要的 10 件最常见的事情。作为一个插件开发者，你可以随意提出更多你可能想要考虑的概念。

## 分享这篇文章