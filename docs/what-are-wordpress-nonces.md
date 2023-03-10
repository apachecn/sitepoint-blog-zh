# 什么是 WordPress 随机数？

> 原文：<https://www.sitepoint.com/what-are-wordpress-nonces/>

保持你的 WordPress 主题或插件代码的安全对于防止恶意用户的攻击是很重要的。

我们之前已经介绍过如何在 WordPress 中清理、转义和验证表单数据，以及用 VIP 扫描仪提高你的 WordPress 主题质量的[。](https://www.sitepoint.com/improve-wordpress-theme-quality-vip-scanner/ "Improve your WordPress theme Quality with VIP Scanner")

今天我们将看看 nonces(使用过一次的数字)是如何帮助你的 WordPress 主题和插件保持安全的。

## 什么是 WordPress 随机数？

WordPress 随机数被定义为:

> …一个“使用过一次的号码”,帮助保护 URL 和表单免受某些类型的误用，无论是恶意的还是其他的。
> [https://codex.wordpress.org/WordPress_Nonces](https://codex.wordpress.org/WordPress_Nonces)

虽然在 WordPress 中，nonce 在技术上不是一个数字(它是由字母和数字组成的散列)，但它确实有助于防止恶意用户运行操作。

WordPress nonces 分两部分工作:

*   创建一个 nonce (hash ),通过表单或动作提交它，以及
*   在接受表单数据或运行操作之前，验证随机数。

例如，当你在 WordPress 管理界面删除一篇文章时，你会注意到 URL 包含一个`_wpnonce`参数:
`http://127.0.0.1/tuts/wp-admin/post.php?post=542&action=trash&_wpnonce=a03ac85772`

删除帖子的例程将在删除帖子之前检查帖子 542 的 nonce 值是否为 a03ac85772。如果 nonce 不存在，或者与预期值不匹配，则帖子不会被删除。

这可以防止恶意用户删除大量帖子。例如，由于 nonce 属于帖子 ID 542，因此下面的代码不起作用:
`http://127.0.0.1/tuts/wp-admin/post.php?post=642&action=trash&_wpnonce=a03ac85772
http://127.0.0.1/tuts/wp-admin/post.php?post=742&action=trash&_wpnonce=a03ac85772
http://127.0.0.1/tuts/wp-admin/post.php?post=842&action=trash&_wpnonce=a03ac85772` 

![Are you sure you want to do this](img/652416494feae42e161392dfc1f55ba4.png)

现在让我们看看如何在插件中实现 WordPress 随机数

## 设置我们的 WordPress 插件

让我们从一个基本插件开始，它有自己的设置屏幕。设置屏幕有一个字段，可以提交并保存在 WordPress 选项表中。

在`wp-content/plugins/implementing-wordpress-nonces/implementing-wordpress-nonces.php`的新文件中输入以下代码:

```
plugin = new stdClass;
        $this->plugin->name         = 'implementing-wordpress-nonces'; // Plugin Folder
        $this->plugin->displayName  = 'Nonces'; // Plugin Name

		add_action( 'admin_menu', array( &$this, 'admin_menu' ) );

	}

	/**
    * Register the plugin settings panel
    *
    * @since 1.0.0
    */
    function admin_menu() {

        add_menu_page( $this->plugin->displayName, $this->plugin->displayName, 'manage_options', $this->plugin->name, array( &$this, 'admin_screen' ), 'dashicons-admin-network' );

    }

    /**
    * Output the Administration Screens
    * Save POSTed data from the Administration Panel into a WordPress option
    *
    * @since 1.0.0
    */
    function admin_screen() {

        // Save Settings
        if ( isset( $_REQUEST['submit'] ) ) {
        	update_option( 'implementing_wordpress_nonces', sanitize_text_field( $_REQUEST[ 'implementing_wordpress_nonces' ] ) );
        	$message = __( 'Settings saved', $this->plugin->name );
        }

        // Output form
        ?>

## plugin->displayName; ?>

<form id="implementing-wordpress-nonces" name="post" method="post" action="admin.php?page=<?php%20echo%20%24this->plugin->name;%20?>">

	        		<label for="implementing_wordpress_nonces">plugin->name ); ?></label>
					<input type="text" id="implementing_wordpress_nonces" name="implementing_wordpress_nonces" value="<?php echo get_option( 'implementing_wordpress_nonces' ); ?/>">
			    	<input type="submit" name="submit" value="<?php _e( 'Save', $this-/>plugin->name ); ?>" class="button button-primary">            

	        </form>

        Activate the plugin via the WordPress Administration > Plugins screen, and you’ll see a new *Nonces* menu item displayed:

![Nonces](img/d0a4224a716de1f444583126893a2587.png)

点击此按钮，您将进入设置屏幕，其中只有一个字段:
 ![Nonces](img/fc68962b36a3e3ae7fc44e57fa4b059f.png)

输入任意值，单击 Save，如果一切正常，您将看到确认，以及您刚刚输入的值:
 ![Nonces](img/1ca6e51b03044b405808332dd69de28f.png)  `## 展示安全漏洞

在你的网络浏览器的地址栏中输入以下网址(用你安装 WordPress 的地方替换域名):
 `http://127.0.0.1/tuts/wp-admin/admin.php?page=implementing-wordpress-nonces&implementing_wordpress_nonces=abc`

注意到发生了什么吗？简单地通过直接访问一个 URL 并登录到 WordPress: 
 ![Nonces](img/1bf2777cf4277107f0d4c198fde6bf73.png)，该值被保存为`abc`

虽然我们可以在代码中使用`$_POST`而不是`$_REQUEST`(我们使用了`$_REQUEST`来更容易地演示安全问题)，但这不会有帮助——恶意用户仍然可以自己或通过诱骗您点击链接——让您向该屏幕发送 POST 请求，导致选项值被更改。

这被称为跨站请求伪造(或 CSRF)。恶意网站、电子邮件、应用程序等。导致用户的 web 浏览器执行不需要的操作。

我们现在将创建并验证一个 WordPress 随机数，以防止这种攻击成为可能。

## 用随机数保护我们的插件

如前所述，这个过程有两个步骤:首先，我们需要创建一个 nonce，它将与我们的表单一起提交。其次，我们需要在提交表单时验证 nonce。

为了在表单中创建一个 nonce 字段，我们可以使用`wp_nonce_field()`:

> 检索或显示 nonce 隐藏表单域…用于验证表单请求的内容来自当前网站而不是其他地方…

在我们的输入按钮上方添加以下代码:

[php]wp_nonce_field( 'implementing_wordpress_nonces_save', 'implementing_wordpress_nonces_nonce' );`

`wp_nonce_field`接受四个论点——前两个最重要:

*   这决定了我们正在运行的特定动作，并且应该是唯一的。在你的动作前加上插件名是一个好习惯，因为可能会有几个动作运行。在这种情况下，我们要保存一些东西，所以我们使用了`implementing_wordpress_nonces_save`

*   `$name`:决定该函数创建的隐藏字段的名称。如上所述，我们用插件名作为前缀，因此称之为`implementing_wordpress_nonces_nonce`

如果我们重新加载设置屏幕，更改我们的值并单击保存，您会注意到该值仍然会更改。我们现在需要使用`wp_verify_nonce( $name, $action )`实现对提交的 nonce 字段的检查:

> 验证 nonce 相对于指定的操作是否正确且未过期。该函数用于验证当前请求中发送的随机数，通常由$_REQUEST PHP 变量访问。

用下面的代码替换我们插件的`admin_screen()`函数的`Save Settings`部分:

```
// Save Settings
if ( isset( $_REQUEST['implementing_wordpress_nonces'] ) ) {
	if ( isset( $_REQUEST[ 'implementing_wordpress_nonces_nonce' ] ) && wp_verify_nonce( $_REQUEST[ 'implementing_wordpress_nonces_nonce' ], 'implementing_wordpress_nonces_save' ) ) {
    	update_option( 'implementing_wordpress_nonces', sanitize_text_field( $_REQUEST[ 'implementing_wordpress_nonces' ] ) );
    	$message = __( 'Settings saved', $this->plugin->name );
    } else {
    	// Nonce could not be verified - bail
    	wp_die( __( 'Invalid nonce specified', $this->plugin->name ), __( 'Error', $this->plugin->name ), array(
    		'response' 	=> 403,
    		'back_link' => 'admin.php?page=' . $this->plugin->name,
    	) );
	}
}
```

这段代码执行一些操作:

*   首先，它检查我们是否提交了什么。

*   然后，它检查我们的 nonce 字段是否存在，如果存在，就尝试根据我们期望的操作来验证 nonce 的值。

*   如果检查通过，选项将被更新。

*   如果检查失败，我们抛出一个 403 错误，并显示消息“指定了无效的 nonce”。

为了确保我们的随机数被创建和验证，让我们再次尝试访问我们的“恶意”直接 URL:
`http://127.0.0.1/tuts/wp-admin/admin.php?page=implementing-wordpress-nonces&implementing_wordpress_nonces=abc`。

如果我们的随机数被实现并被验证，你会看到*指定的无效随机数*通知:
 ![Nonces](img/666b41351366cfbb1c6d2b1f39ffd58c.png)

## 在 AJAX 请求中使用随机数

假设我们想通过 AJAX 调用保存更改，而不是重新加载整个屏幕。我们可以通过一些代码调整来做到这一点。

首先，让我们将一些 JavaScript 加载到我们的插件中，并注册一个 AJAX 处理程序，方法是将以下内容添加到我们的`__construct()`:

```
add_action( 'admin_enqueue_scripts', array( &$this, 'admin_scripts_css' ) );
add_action( 'wp_ajax_implementing_wp_nonces', array( &$this, 'admin_ajax_save' )  );
```

在我们的类中，添加相应的`admin_scripts_css`函数:

```
/**
* Register and enqueue any JS and CSS for the WordPress Administration
*
* @since 1.0.0
*/
function admin_scripts_css() {

	// JS
	wp_enqueue_script( $this->plugin->name, plugin_dir_url( __FILE__ ) . 'admin.js', array( 'jquery' ), '1.0', true );

}
```

对于我们的 AJAX 调用，添加相应的`admin_ajax_save`函数:

```
/**
 * Saves POSTed settings data
 *
 * @since 1.0.0
 */
function admin_ajax_save() {

	// Save option and return 1
	update_option( 'implementing_wordpress_nonces', sanitize_text_field( $_REQUEST[ 'implementing_wordpress_nonces' ] ) );
	echo 1;
	die();

}
```

最后，我们需要在插件文件夹中为 JavaScript 例程创建一个新文件，当表单被提交时，它将通过 AJAX 发布数据。让我们创建一个名为`admin.js`的新文件，插入以下内容:

```
jQuery( document ).ready( function( $ ) {

	$( 'form#implementing-wordpress-nonces' ).submit( function( e ) {

		// Prevent form submission
		e.preventDefault();

		// Submit form via AJAX
		$.post(
	        ajaxurl, // Set by WordPress
	        {
	        	'action': 'implementing_wp_nonces',
	        	'implementing_wordpress_nonces': $( 'input#implementing_wordpress_nonces' ).val()
	        },
	        function(response) {
	            if ( response ) {
	            	alert( 'Settings Saved' );
	            }
	        }
	    );
    });

} );
```

重新加载我们的设置屏幕，如果一切正常，当你提交表单时，你会得到一个屏幕确认，说设置已保存。
 ![Nonces](img/1b13d223557784d84bed74ea8085492d.png)

我们可以再次重新加载设置屏幕，以确保数值更新成功:
 ![Nonces](img/12ea874ac5d06ac90239fe594b149c77.png)

同样，我们现在需要实现 WordPress nonce 的创建和验证，以确保 CSRF 攻击不会通过 AJAX 请求发生。

为了给 AJAX 请求创建随机数，我们可以使用 WordPress `wp_create_nonce`函数:

> 生成并返回一个随机数。nonce 是基于当前时间、$action 参数和当前用户 ID 生成的。

它接受单个`$action`参数，所以我们使用:

```
$nonce = wp_create_nonce( 'implementing_wordpress_nonces_ajax_save' );
```

为了让这个 nonce hash 进入 JavaScript 的范围，我们可以使用`wp_localize_script()`发送一个 JS 对象。让我们在插件中的`wp_enqueue_script`调用下面添加以下代码:

```
wp_localize_script( $this->plugin->name, 'implementing_wordpress_nonces', array(
	'nonce' => wp_create_nonce( 'implementing_wordpress_nonces_ajax_save' ),
) );
```

我们还需要更新 JavaScript 文件，以便通过 AJAX POST 请求发送这个 nonce 值:

```
jQuery( document ).ready( function( $ ) {

	$( 'form#implementing-wordpress-nonces' ).submit( function( e ) {

		// Prevent form submission
		e.preventDefault();

		// Submit form via AJAX
		$.post(
	        ajaxurl, // Set by WordPress
	        {
	        	'action': 'implementing_wp_nonces',
	        	'nonce':  implementing_wordpress_nonces.nonce,
	        	'implementing_wordpress_nonces': $( 'input#implementing_wordpress_nonces' ).val()
	        },
	        function(response) {
	        	if ( response == 1 ) {
	            	alert( 'Settings Saved' );
	            } else {
	            	alert( 'Invalid nonce specified' );
	            }
	        }
	    );
    });

} );
```

JavaScript 文件现在发送一个名为`nonce`的`$_POST`变量，其值为`wp_create_nonce`。

最后，我们需要验证在 AJAX 请求中发送的 nonce。更新我们的`admin_ajax_save`功能:

```
/**
 * Saves POSTed settings data
 *
 * @since 1.0.0
 */
function admin_ajax_save() {

	// Run a security check first.
	check_ajax_referer( 'implementing_wordpress_nonces_ajax_save', 'nonce' );

	// Save option and return 1
	update_option( 'implementing_wordpress_nonces', sanitize_text_field( $_REQUEST[ 'implementing_wordpress_nonces' ] ) );
	echo 1;
	die();

}
```

这使用了`check_ajax_referer`，它检查给定的帖子字段`$name` (nonce)是否存在给定的`$action`(implementing _ WordPress _ nonces _ Ajax _ save)。

如果失败，将返回-1，触发我们的 JavaScript 文件显示一个警告:

![Nonces](img/0aa5ab36035877c0a61acb1444420f31.png)

如果成功，将返回 1，触发我们的 JavaScript 文件显示一条成功消息:

![Nonces](img/1b13d223557784d84bed74ea8085492d.png)

## 结论

我们已经学习了什么是 WordPress 随机数，以及 WordPress 如何使用随机数来防止恶意的 CSRF 攻击。通过构建一个基本的 WordPress 插件，我们已经展示了一个插件——以及 WordPress——如果 nonces 没有被创建和验证，可能会被利用。

使用`wp_nonce_field()`和`wp_verify_nonce`，我们已经展示了如何验证随机数以防止 CSRF 攻击。最后，我们实现了帖子数据的 AJAX 保存，并使用`wp_create_nonce`和`check_ajax_referer`来确保我们的 AJAX 请求尽可能安全。

完整源代码，[查看 GitHub 库](https://github.com/n7studios/implementing-wordpress-nonces)或[直接下载代码](https://github.com/n7studios/implementing-wordpress-nonces/archive/master.zip)。

## 分享这篇文章

```