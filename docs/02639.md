# 快速提示:防止 WordPress 插件不兼容

> 原文：<https://www.sitepoint.com/preventing-wordpress-plugin-incompatibilities/>

随着 WordPress 占据了超过 25%的网页，开发者需要准备好他们的插件以获得最大的兼容性。根据[最新统计数据](https://wordpress.org/about/stats/)，WordPress 可以安装在六个不同的 PHP 版本上，但不能保证运行该网站的用户使用的是 WordPress 的最新版本。

据统计仍然有人运行 WordPress 版本。

如果我们也依赖于第三方库，我们也需要考虑这些库的需求。如果正在使用 PHP 扩展，这同样适用。

![WordPress Plugin Incompatibilities](img/e2baae0f04277b4680bacf584b47bce6.png)

## 检测插件激活时的不兼容性

通过在插件激活中采取防御方法，我们可以确保我们的插件不会安装在不兼容的环境中。

其他一些好处包括:

*   当我们的插件不工作时，减少最终用户的挫折感。
*   增加我们的插件安装在最兼容的环境中的保证。
*   鼓励网站所有者升级他们的网站。
*   通过尽早拦截问题，我们可以为用户提供替代方案，或者将他们重定向到首选的支持渠道。

## 基本概念

为了正确地做到这一点，需要做几件事情:

*   首先，我们需要创建一个函数来检查环境，以确保所有需求都得到满足。
*   如果不满足要求，在仪表板上显示管理通知并禁用插件。
*   如果需求通过，我们将继续我们的插件代码的其余部分。

让我们根据上面建立的流程来编写代码。

### 检查是否满足要求

首先，我们将创建一个函数，根据我们的需求检查环境，该函数将返回`true`或`false`。在这个函数中，我们可以自由地检查我们想要的任何东西，无论是 WordPress 版本、PHP 版本还是其他任何需要确保我们的插件可以顺利运行的东西。让我们从简单的需求开始，至少需要 WordPress 4.0 和 PHP 5.3。为了增加一点复杂性，我们还需要激活`ftp` PHP 扩展。

```
function prefix_is_requirements_met() {
    $min_wp  = '4.0';
    $min_php = '5.3';
    $exts    = array('ftp');

    // Check for WordPress version
    if ( version_compare( get_bloginfo('version'), $min_wp, '<' ) ) {
        return false;
    }

    // Check the PHP version
    if ( version_compare( PHP_VERSION, $min_php, '<' ) ) {
        return false;
    }

    // Check PHP loaded extensions
    foreach ( $exts as $ext ) {
        if ( ! extension_loaded( $ext ) ) {
            return false;
        }
    }

    return true;
} 
```

对于我们的目的来说，一个简单的`version_compare`和`extension_loaded`就足够了，但是可以随意扩展这个函数以适应任何需要满足的额外需求。

### 禁用插件并显示激活失败通知

然后我们可以在我们的插件主文件中运行这个函数，如果不满足要求，我们将添加两个回调函数，分别添加到`admin_init`和`admin_notices`中。

```
if ( ! prefix_is_requirements_met() ) {
    add_action( 'admin_init', 'prefix_disable_plugin' );
    add_action( 'admin_notices', 'prefix_show_notice' );
} 
```

让我们看看每个回调函数都做些什么。

```
function prefix_disable_plugin() {
    if ( current_user_can('activate_plugins') && is_plugin_active( plugin_basename( __FILE__ ) ) ) {
        deactivate_plugins( plugin_basename( __FILE__ ) );

        // Hide the default "Plugin activated" notice
        if ( isset( $_GET['activate'] ) ) {
            unset( $_GET['activate'] );
        }
    }
} 
```

在我们可以安全地调用`deactivate_plugins`函数之前，我们验证当前登录用户的权限，以及检查插件是否是活动的。这是为了处理一些边缘情况，在这种情况下，插件可能由于某种原因已经激活，例如在插件更新期间。请注意，我们进一步取消了`$_GET['activate']`变量的设置，否则，即使插件没有激活，仍然会显示“插件已激活”的通知。这是为了防止最终用户产生任何混淆。

显示激活失败的通知相当简单，我们需要做的就是遵循 WordPress 用来显示通知的原始标记。

```
function prefix_show_notice() {
    echo '<div class="error"><p><strong>Plugin Name</strong> cannot be activated due to incompatible environment.</p></div>';
} 
```

### 有条件地加载插件的其余代码

为此，只需在我们进行检查的前一个代码块中包含`else`语句。因此，代码的最终版本将是:

```
if ( ! prefix_is_requirements_met() ) {
    add_action( 'admin_init', 'prefix_disable_plugin' );
    add_action( 'admin_notices', 'prefix_show_notice' );
} else {
    // The rest of plugin's code
} 
```

完成后，我们可以测试代码是否真的工作。只需将 WordPress 或 PHP 版本的最低要求值更改为一个会失败的值，然后激活插件。我们应该能够看到自定义通知以及插件被正确停用。

## 结论

通过几行样板代码，我们可以降低插件在不兼容环境中被激活的风险。这不仅有助于减少最终用户的挫折感，还能让我们确信我们的插件是在尽可能好的环境中激活的。

## 分享这篇文章