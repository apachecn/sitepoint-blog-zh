# 构建一个标签式的 WordPress 登录和注册窗口小部件

> 原文：<https://www.sitepoint.com/build-a-tabbed-wordpress-login-and-registration-widget/>

在过去的几年里，对 WordPress 定制注册窗口小部件的需求越来越大。对 WordPress 默认登录和注册页面的不尊重是健康的，仅仅是因为它们不直观，因为它们可能没有反映客户或 web 开发者的设计和品牌。

为了解决这个问题，一些开发人员发布了几种登录和注册页面，但是好的页面是有价格的，而且价格不菲。

然而，如果你对 WordPress 插件库中的免费内容持观望态度或总体上感到失望，你可以振作精神，因为你可以学习如何免费创建一个标签式的 WordPress 登录和注册窗口小部件！你只需要牺牲一小段时间就能掌握整个过程。一旦你获得了相当简单的知识；你应该在你的 WordPress powered 站点上有一个功能丰富的注册页面。

在本文中，我们将构建一个简单的选项卡式登录和注册表单小部件，通过 [QuickFlip](http://jonraasch.com/blog/quickflip-2-jquery-plugin) jQuery 库实现翻转效果。

如果你想跳过教程，你可以[观看*登录和注册小工具*的演示](http://fifa-wc.tech4sky.com/)和[下载小工具插件](https://github.com/Collizo4sky/tab-login-registration-widget/archive/master.zip)。

为了简单起见，注册表单将包含用户名、密码和电子邮件字段。登录表单将不包含验证码。

不再赘述，让我们开始小部件开发吧。

## 小部件开发

首先，包括插件头。

```
<?php
/*
Plugin Name: Tabbed Login Registration Widget
Plugin URI: https://www.sitepoint.com
Description: A tabbed login and registration widget for WordPress
Version: 1.0
Author: Agbonghama Collins
Author URI: http://w3guy.com
License: GPL2
*/
```

在我们正确地进入小部件开发之前，我们需要打开输出缓冲以防止任何**警告:不能修改头信息——头已经发送了**错误。

```
// Turn on output buffering
ob_start();
```

要创建一个 WordPress 小部件，扩展标准的`WP_Widget`类，包含必要的方法，最后注册小部件。

创建一个子类来扩展`WP_Widget`类。

```
class Tab_Login_Registration extends WP_Widget {
```

静态属性`$login_registration_status`将保存注册和登录表单生成的错误消息。

```
static private $login_registration_status;
```

使用`__construct()`魔术方法给小部件命名和描述。

```
/**
	 * Register widget with WordPress.
	 */
	function __construct() {
		parent::__construct(
			'tab_login_registration', // Base ID
			__( 'Tabbed Login Registration Widget', 'text_domain' ), // Name
			array( 'description' => __( 'A tabbed login and registration widget for WordPress', 'text_domain' ), ) // Args
		);
	}
```

返回登录和注册表单的 HTML 代码的两个方法—`login_form()`和`registration_form()`—将被创建以供以后重用。

```
/**
	 * Returns the HTML for the login form
	 * @return string
	 */
	static function login_form() {
		$html = '<form method="post" action="' . esc_url( $_SERVER['REQUEST_URI'] ) . '">';
		$html .= '<input type="text" name="login_username" placeholder="Username" /><br/>';
		$html .= '<input type="password" name="login_password" placeholder="Password" /><br/>';
		$html .= '<input type="checkbox" name="remember_login" value="true" checked="checked"/> Remember Me<br/>';
		$html .= '<input type="submit" name="login_submit" value="Login" /><br/>';
		$html .= '</form>';

		return $html;

	}
```

```
/**
	 * Returns the HTML code for the registration form
	 * @return string
	 */
	static function registration_form() {
		$html = '<form method="post" action="' . esc_url( $_SERVER['REQUEST_URI'] ) . '">';
		$html .= '<input type="text" name="registration_username" placeholder="Username" /><br/>';
		$html .= '<input type="password" name="registration_password" placeholder="Password" /><br/>';
		$html .= '<input type="email" name="registration_email" placeholder="Email" /><br/>';
		$html .= '<input type="submit" name="reg_submit" value="Sign Up" /><br/>';
		$html .= '</form>';

		return $html;
	}
```

下面的`register_user()`处理新用户的注册。

```
/**
	 * Register new users
	 */
	function register_user() {

		if ( isset( $_POST['reg_submit'] ) ) {

			$username = esc_attr( $_POST['registration_username'] );
			$password = esc_attr( $_POST['registration_password'] );
			$email    = esc_attr( $_POST['registration_email'] );

			$register_user = wp_create_user( $username, $password, $email );

			if ( $register_user && ! is_wp_error( $register_user ) ) {

				self::$login_registration_status = 'Registration completed.';
			} elseif ( is_wp_error( $register_user ) ) {
				self::$login_registration_status = $register_user->get_error_message();
			}

		}
	}
```

下面是该方法注册新用户的方式。

首先，我们确保用户确实注册了一个帐户。这是通过检测“注册”按钮被点击，即`$_POST['reg_submit']`已被设置来完成的。

然后使用 [esc_attr](http://codex.wordpress.org/Function_Reference/esc_attr) 函数对 HTML 属性的表单数据进行转义。

然后，用户输入的用户名、密码和电子邮件被传递给 [wp_create_user()](https://codex.wordpress.org/Function_Reference/wp_create_user) 函数，以将新用户插入到 WordPress 数据库中。

如果一切顺利，属性`$login_registration_status`将被设置为文本`Registration completed`，否则`wp_create_user()`返回的错误将是它的值。

当提交登录表单时，下面的`login_user()`功能登录用户。

```
/**
	 * Login registered users
	 */
	function login_user() {
		if ( isset( $_POST['login_submit'] ) ) {

			$creds                  = array();
			$creds['user_login']    = esc_attr( $_POST['login_username'] );
			$creds['user_password'] = esc_attr( $_POST['login_password'] );
			$creds['remember']      = esc_attr( $_POST['remember_login'] );

			$login_user = wp_signon( $creds, false );

			if ( ! is_wp_error( $login_user ) ) {
				wp_redirect( home_url( 'wp-admin' ) );
			} elseif ( is_wp_error( $login_user ) ) {
				self::$login_registration_status = $login_user->get_error_message();
			}
		}
	}
```

请允许我解释一下`login_user()`如何让用户登录 WordPress。

与前面提到的`register_user()`类似，首先我们通过检查`$_POST['login_submit']`是否已经设置来确保用户正在尝试登录。

创建用户输入的登录凭证的关联数组`$creds`。

关联数组然后被传递给 [wp_signon](http://codex.wordpress.org/Function_Reference/wp_signon) 进行认证。

如果凭证在`wp_signon`之前被认为是有效的，用户将被 [wp_redirect](http://codex.wordpress.org/Function_Reference/wp_redirect) 重定向到 WordPress 仪表板，否则属性`$login_registration_status`被设置为登录生成的错误。

后端小部件设置表单是由`form()`方法创建的，它由一个包含小部件标题的字段组成。

```
public function form( $instance ) {
		if ( isset( $instance['title'] ) ) {
			$title = $instance['title'];
		} else {
			$title = __( 'Login / Registration', 'text_domain' );
		}
		?>
		<p>
			<label for="<?php echo $this->get_field_id( 'title' ); ?>"><?php _e( 'Title:' ); ?></label>
			<input class="widefat" id="<?php echo $this->get_field_id( 'title' ); ?>"
			       name="<?php echo $this->get_field_name( 'title' ); ?>" type="text"
			       value="<?php echo esc_attr( $title ); ?>">
		</p>
	<?php
	}
```

![Login and Registration](img/f8126b4d8957584656708363e1a6b3f0.png)

当小部件标题被输入到表单字段中时，`update()`方法将数据清理并保存到数据库中以供重用。

```
public function update( $new_instance, $old_instance ) {
		$instance          = array();
		$instance['title'] = ( ! empty( $new_instance['title'] ) ) ? strip_tags( $new_instance['title'] ) : '';

		return $instance;
	}
```

`widget()`方法在 WordPress 的前端显示选项卡式登录和注册表单。

```
public function widget( $args, $instance ) { ?>
		<script type="text/javascript">
			$('document').ready(function () {
				$('#flip-container').quickFlip();

				$('#flip-navigation li a').each(function () {
					$(this).click(function () {
						$('#flip-navigation li').each(function () {
							$(this).removeClass('selected');
						});
						$(this).parent().addClass('selected');
						var flipid = $(this).attr('id').substr(4);
						$('#flip-container').quickFlipper({}, flipid, 1);

						return false;
					});
				});
			});
		</script>

		<?php
		$title = apply_filters( 'widget_title', $instance['title'] );

		echo $args['before_widget'];
		if ( ! empty( $title ) ) {
			echo $args['before_title'] . $title . $args['after_title'];
		} ?>

		<?php $this->login_user(); ?>

		<?php $this->register_user(); ?>

		<div class="login-reg-error"><?php echo self::$login_registration_status; ?></div>
		<div id="flip-tabs">
			<ul id="flip-navigation">
				<li class="selected"><a href="#" id="tab-0">Login</a></li>
				<li><a href="#" id="tab-1">Register</a></li>
			</ul>
			<div id="flip-container">
				<div>
					<ul class="orange">
						<?php echo self::login_form(); ?>
					</ul>
				</div>
				<div>
					<ul class="green">
						<?php echo self::registration_form(); ?>
					</ul>
				</div>
			</div>
		</div>

		<?php
		echo $args['after_widget'];
	}
```

**代码解释:**JavaScript 代码为小部件添加了跳转和翻转效果。

`login_user()`和`register_user()`分别用于注册用户的登录和新用户的注册。

调用静态方法`login_form()`和`registration_form()`来显示登录和注册表单。

最后，我们关闭小部件类。

```
} // class Tab_Login_Registration
```

小部件类`Tab_Login_Registration`需要使用`widgets_init`钩子注册，这样 WordPress 内部就能识别它。

```
// register Foo_Widget widget
function register_tab_login_registration() {
	register_widget( 'Tab_Login_Registration' );
}

add_action( 'widgets_init', 'register_tab_login_registration' );
```

我们仍然需要包括 jQuery、小部件 CSS 和 QuickFlip 库，以获得小部件工作时的标签和翻转效果。

jQuery 和 Quickflip javascript 以及 CSS 通过 [wp_enqueue_style](https://codex.wordpress.org/Function_Reference/wp_enqueue_style) 和 [wp_enqueue_script](https://codex.wordpress.org/Function_Reference/wp_enqueue_script) 包含/排队到 WordPress 的头部。

```
function plugin_assets() {
	wp_enqueue_style( 'tlrw-styles', plugins_url( 'css/styles.css', __FILE__ ) );
	wp_enqueue_script( 'tlrw-jquery', plugins_url( 'js/jquery.js', __FILE__ ) );
	wp_enqueue_script( 'tlrw-quickflip', plugins_url( 'js/jquery.quickflip.js', __FILE__ ) );
}

add_action( 'wp_enqueue_scripts', 'plugin_assets' );
```

我们已经完成了登录和注册小部件的编码。

下面是小部件的屏幕截图。

![Widget](img/c3fda316c173d54796d61e638a9b3867.png)

[观看小工具](http://fifa-wc.tech4sky.com/)的演示。

## 包裹

为了进一步理解小部件是如何构建的，以及如何在你的 WordPress 站点上实现它，[下载小部件插件](https://github.com/Collizo4sky/tab-login-registration-widget/archive/master.zip)，它包括 jQuery、Quickflip 和小部件样式表文件。

如果您对代码改进有任何问题或建议，请在评论中告诉我。

## 分享这篇文章