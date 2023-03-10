# 使用 wp-config 定制 WordPress

> 原文：<https://www.sitepoint.com/using-wp-config-customize-wordpress/>

正如你可能已经知道的，由于不同的现有主题，你可以完全定制 WordPress 网站的布局，如果你想拥有一个完全个性化的网站，你甚至可以创建自己的网站。

这是好事，但是你知道 WordPress 本身的配置也是可以修改的吗？这就是 wp-config 文件的目的。

在本文中，我们将看一看 wp-config 文件，以便了解它是什么，它包含什么，以及我们可以(和不可以！)随它去吧。

## 什么是 wp-config 文件？

当我提到 wp-config 文件时，我指的是位于 WordPress 安装根目录下的文件`wp-config.php`。它包含了 WordPress 运行所需的大部分信息。如果你没有正确填写这个文件，你的 WordPress 安装将无法工作。这意味着您将无法启动您的网站，甚至可能因为在编辑该文件时出错而中断您的安装。

为了避免这些错误，确切地知道 wp-config 文件包含什么是很重要的。这样，您将能够在没有任何风险的情况下修改您想要的内容(但是在将它们发送到您的服务器之前，请确保在本地测试您的更改)。

此外，理解 wp-config 文件将允许你添加一些参数，修改 WordPress 的默认行为。

注意，你不必手动编辑 wp-config 文件，即使 WordPress 还没有配置。事实上，WordPress 安装助手会询问你需要的信息，如果它不存在的话。这意味着如果你想定制一些设置，你只需要编辑 wp-config 文件。

在本文的下一节，我们将介绍如果您想自己编辑 wp-config 文件，您必须在其中包含的不同设置。

如果你刚刚下载了 WordPress 文档，那么你还没有 wp-config 文件。取而代之的是一个名为`wp-config-sample.php`的文件。如果你不想使用安装助手，编辑`wp-config-sample.php`文件并将其重命名为`wp-config.php`。

## 我们能在这个文件里找到什么？

在这一节中，我们将介绍在 WordPress 4.1 中当前状态的 wp-config 文件中可以找到的列表。同时，如果需要，我们将看到如何编辑这些行。

### 数据库设置

数据库设置是唯一的强制设置。您必须指出正确的值。否则，WordPress 无法创建它需要的表格。这些设置和 wp-config 文件中的许多其他设置一样，包含在一个常量列表中。

要在 PHP 中定义一个常量，必须使用`define()`函数。在第一个参数中，给出常数的名称，在第二个参数中，给出它的值，可以是字符串、数字或任何您想要的值。

正如你可能意识到的，为了让 WordPress 使用它，我们需要指明数据库凭证。如果你不知道所有需要的信息，你可以在你的虚拟主机的管理面板中搜索细节。

定义数据库设置的第一个常量是`DB_NAME`。顾名思义，它必须包含你想用来存储 WordPress 所需内容的数据库的名称。这里的值应该是表示相应名称的字符串。

```
define('DB_NAME', 'wordpressdatabase');
```

在数据库名称之后，我们找到了`DB_USER`常量，它必须包含有权使用数据库的用户的名称。该用户应该有一个在`DB_PASSWORD`常量中显示的密码。

```
define('DB_USER', 'databaseuser');
define('DB_PASSWORD', 'dBpAsSwOrD');
```

最后，最新的强制数据库设置是`DB_HOST`常量，它必须包含我们可以在其上找到您的数据库的服务器。通常，`'localhost'`值就足够了，但是也许你的虚拟主机会给你一个 IP 地址，甚至一个子域。

```
define('DB_HOST', 'localhost');
```

接下来的两个常量也与数据库相关，但是与 WordPress 表使用的字符集有关。默认情况下，WordPress 将使用 UTF-8，但是如果你想改变它，在`DB_CHARSET`常量中指明你的偏好。您甚至可以用`DB_COLLATE`常量设置自己的排序规则。

在这些常量定义的几行之后，我们找到了一个名为`$table_prefix`的变量的声明。WordPress 使用这个变量来命名它创建的表格。默认情况下，它的值是`wp_`，所以 WordPress 创建的所有表格都会以`wp_`开头，像`wp_posts`或者`wp_options`。

### 认证密钥

为了自动登录你的用户，WordPress 使用 cookies。存储在这些 cookies 中的信息是加密的，由于有八个常量，您可以获得更好的加密。

如果您想要更好的加密和安全性，则需要`AUTH_KEY`、`SECURE_AUTH_KEY`、`LOGGED_IN_KEY`和`NONCE_KEY`常量。多亏了`AUTH_SALT`、`SECURE_AUTH_SALT`、`LOGGED_IN_SALT`和`NONCE_SALT`常量，您可以用 salts 增强这种加密。

这些常量接受的值是字符串。这些字符串应该是唯一的，以便有更好的安全性。此外，尽量使用不同的特殊字符，否则你的短语会被猜到。

为了帮助你，WordPress 为你提供了一个工具:在线生成器。该工具将生成独特的短语，显示在定义正确常数的代码中:您所要做的就是将结果复制并粘贴到您当前定义的位置。

你可以随时更换你的钥匙和盐。当您这样做时，存储在用户 cookies 中的信息将失效:您的用户下次将不得不手动登录。

### 调试方式

在声明了`$tables_prefix`变量之后，我们发现了`WP_DEBUG`常量，默认情况下定义为`false`。如果你把它改成`true`，WordPress 会显示一些信息，如果你为这个平台开发的话，这些信息会很有用。

如果这个 WordPress 安装不仅仅是为了开发，你应该避免这个选项。但是，如果您激活了调试模式，那么您将能够添加新的有用的选项，我们将在下一节中介绍。

### 下面的不要碰！

`WP_DEBUG`常量是您可以修改的最后一个默认值。然而，在这个常数之后，我们发现另一个常数叫做`ABSPATH`。不要修改此常数。WordPress 用它来检索 WordPress 安装的绝对路径。

最后，wp-config 文件以包含另一个文件而结束:`wp-settings.php`，它位于您的安装的根目录下。这个文件设置了一些 WordPress 使用的常量、变量或函数。同样，不要碰这个文件，或者 wp-config 文件中调用的`require_once()`函数中使用的路径。

## 我们可以在这个文件中添加什么？

上面我们探讨了如何编辑 wp-config 文件，但是我们也可以在这个文件中添加一些东西，通常是一些常量。

要在 wp-config 文件中添加内容，您必须遵守一条规则:添加必须在定义`ABSPATH`常量之前完成，默认情况下就在定义`WP_DEBUG`常量之后。

我们现在将回顾一些可以包含在 wp-config 文件中的有用的附加内容，但是这个列表并不详尽。

### 自动更新

如果你阅读了我们关于[更新 WordPress](https://www.sitepoint.com/a-guide-to-updating-wordpress/) 的文章，你已经看到了在 wp-config 文件中添加内容的例子。事实上，如果您想禁用自动更新，您可以添加以下常量。

```
define('AUTOMATIC_UPDATER_DISABLED', true);
```

但是您也可以使用下面的常量来激活主要的自动更新。

```
define('WP_AUTO_UPDATE_CORE', true);
```

### 移动文件夹

默认情况下，WordPress 会将你的插件和主题存储在你的安装根目录下的`wp-content`子目录中。如果您愿意，您可以通过在 wp-config 文件中自己定义`WP_CONTENT_DIR`常量来改变这种行为。

要找到文件夹`wp-content`，你可以使用 PHP 函数`dirname()`来获取包含你的 wp-config 文件的目录的路径，这是你的 WordPress 安装的根目录。

```
define('WP_CONTENT_DIR', dirname(__FILE__) . '/path/to/my/content-dir');
```

不要在此路径中添加结尾斜杠。如果您想要更改这个目录，您还应该通过定义`WP_CONTENT_URL`常量的值来更改它的 URL，同样不要使用尾随斜杠。

```
define('WP_CONTENT_URL', 'http://mysite.com/path/to/my/content-dir');
```

遵循相同的格式，您也可以为插件目录定义自己的路径。例如，您可以使用刚刚定义的内容目录。

```
define('WP_PLUGIN_DIR', WP_CONTENT_DIR . '/myplugins');
define('WP_PLUGIN_URL', WP_CONTENT_URL . '/myplugins');
```

由于有了`UPLOADS`常量，您还可以更改上传目录。

```
define('UPLOADS', 'my/subdirectory/for/uploads');
```

如果后面没有斜杠，这个路径一定不是绝对的:它是相对于 WordPress 定义的`ABSPATH`常量的。

注意，不能移动主题目录，该目录必须是用`WP_CONTENT_DIR`常量定义的内容目录的`themes`子目录。

### 排除故障

#### 错误报告

我们已经看到，由于将`WP_DEBUG`常量设置为`true`，我们可以激活调试模式。如果你激活了这个选项，WordPress 将通过提高错误报告级别到`E_ALL`来显示所有的错误，你将会知道你什么时候使用了被否决的功能。

您可以通过使用`WP_DEBUG_LOG`和`WP_DEBUG_DISPLAY`常量来控制向您报告错误的方式。设置为`true`，前者会将错误发送到日志文件中，而后者会直接显示错误。

```
// I don't want to miss any errors!
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', true);
```

#### WordPress 脚本和样式

另一个有用的常数是`SCRIPT_DEBUG`常数。同样，它是一个默认设置为`false`的布尔值。通过将其设置为`true`，WordPress 将改变其包含 JavaScript 和 CSS 文件的方式。默认情况下，它包括他们的缩小版本，但当此选项被激活时，它包括他们的完整版本，这在您想要编辑他们时很有用。

```
define('SCRIPT_DEBUG', true);
```

#### 问题

如果您需要分析 WordPress 在页面显示期间所做的查询，您可以将`SAVEQUERIES`常量设置为`true`。

```
define('SAVEQUERIES', true);
```

这样，您将能够检索到`$wpdb`对象的`queries`属性中的所有查询。例如，在页脚中，如果当前用户是管理员，您可以显示查询。

```
if (current_user_can('administrator')) {
global $wpdb;
echo '<pre>' . print_r($wpdb->queries, true) . '</pre>';
}
```

### Posts 相对常数

#### 修订

如你所知，每次你编辑一篇文章或一个页面，WordPress 都会将你的编辑保存到一个“修订版”中。通过这种方式，你将能够检索到你文章的前一个版本，并在需要时取消一些编辑。

如果您确定不需要修改，您可以通过将`WP_POST_REVISIONS`常量设置为`false`来停用此行为。默认情况下，它的值是`true`，每次你编辑一篇文章，WordPress 都会创建一个新的修订，但是你也可以通过给同一个常量一个整数来限制修订的次数。

```
// I don't want to create revisions
define('WP_POST_REVISIONS', false);

// I love revisions!
define('WP_POST_REVISIONS', true);

// Too many revisions?
define('WP_POST_REVISIONS', 3);
```

#### 自动保存

当你编辑一篇文章时(或者当你创建一篇新文章时)，WordPress 不会等待你完成编辑来保存草稿或修改:使用 AJAX，它会定期保存你的修改，考虑到由`AUTOSAVE_INTERVAL`常量给出的延迟。默认情况下，此延迟为 60 秒，但您可以通过指定您想要的秒数来更改它。

```
// Save my changes two times per minute
define('AUTOSAVE_INTERVAL', 30);
```

#### 垃圾箱

有时候，我们会删帖。为了做到这一点，WordPress 为我们提供了一个垃圾桶，它每 30 天清空这个垃圾桶。您可以通过将您想要的天数设置为`EMPTY_TRASH_DAYS`常量来更改此延迟。如果你将这个值设置为`0`，WordPress 将禁用回收站:通过删除一篇文章，它将被永久删除，无需任何确认。

```
// Delete permanently items from the trash bin every two months
define('EMPTY_TRASH_DAYS', 60);
```

```
// Disabling the trash bin
define('EMPTY_TRASH_DAYS', 0);
```

请注意，所有垃圾箱都将被删除，因此不仅帖子会被永久删除，页面、评论和附件也会被永久删除。

## 最后

wp-config 文件在任何 WordPress 安装中都很重要。它定义了存储数据所需的数据库设置，也定义了当你为 WordPress 开发插件或主题时一些有用的选项。

现在您知道了在这个文件中可以找到什么，所以您可以毫无顾虑地编辑它。不要忘记总是在 WordPress 的本地版本上测试你的编辑，以防止在实时网站上出现任何潜在的问题。

如上所述，我们在本文中提到的编辑和添加的列表绝不是详尽的。我试图向你展示我认为最有用的常量，但是如果你想更深入地探索 wp-config 文件，WordPress Codex 给了你更多的可能性。

## 分享这篇文章