# 整合验证码和 WordPress 登录表单

> 原文：<https://www.sitepoint.com/integrating-a-captcha-with-the-wordpress-login-form/>

在之前的教程中，我们深入研究了 [WordPress HTTP API](https://www.sitepoint.com/the-wordpress-http-api/) ，我承诺向你展示如何在 WordPress 插件中通过`HTTP API`使用 API。

在构建[域名 WHOIS 和社交数据 WordPress Widget](https://www.sitepoint.com/building-a-domain-whois-and-social-data-wordpress-widget/) 的过程中，我们已经看到了一个使用 HTTP API 的插件示例，在今天的教程中，我们将使用 Google 的 reCAPTCHA 编写一个插件，将验证码与 WordPress 登录系统集成在一起。当然，`HTTP API`将用于向 reCAPTCHA 发送 POST 请求，以验证用户对验证码挑战的回答。

下面是一个默认的 WordPress 登录表单的截图，在激活本教程中将要开发的插件时，这个表单受到验证码的保护。

![CAPTCHA WordPress](img/db1a57a525ef098f5d3328354c7788f7.png)

## 插件开发

> 在我们开始编写插件之前，前往 [reCAPTCHA](https://www.google.com/recaptcha/admin#createsite) ，注册你的域名并获取你的`public`和`private` API 密钥。

首先，包括插件头。

```
<?php

/*
Plugin Name: WP Login Form with reCAPTCHA
Plugin URI: https://www.sitepoint.com
Description: Add Google's reCAPTCHA to WordPress Login 
Version: 1.0
Author: Agbonghama Collins
Author URI: http://w3guy.com
License: GPL2
*/
```

创建一个带有两个属性的 PHP 类，这两个属性将存储 reCAPTCHA 的私钥和公钥。

```
class reCAPTCHA_Login_Form {

    /** @type string private key|public key */
    private $public_key, $private_key;
```

当用 [OOP](https://learnable.com/courses/object-oriented-php-2734) 的方式编写 WordPress 插件时，所有的动作和过滤钩子都应该在构造函数(`__construct`)中。
我们插件的魔法构造方法将由两个动作挂钩组成，将验证码添加到登录表单并验证验证码响应。

```
/** class constructor */
    public function __construct() {
        $this->public_key  = '6Le6d-USAAAAAFuYXiezgJh6rDaQFPKFEi84yfMc';
        $this->private_key = '6Le6d-USAAAAAKvV-30YdZbdl4DVmg_geKyUxF6b';

        // adds the captcha to the login form
        add_action( 'login_form', array( $this, 'captcha_display' ) );

        // authenticate the captcha answer
        add_action( 'wp_authenticate_user', array( $this, 'validate_captcha_field' ), 10, 2 );
    }
```

**代码解释:**首先，我的 reCAPTCHA 公钥和私钥被保存到它们的类属性中。

通过 [login_form](https://codex.wordpress.org/Plugin_API/Action_Reference/login_form) 动作，将输出 reCAPTCHA 挑战的`captcha_display()`方法被添加到 WordPress 登录中。

通过 [wp_authenticate_user](https://codex.wordpress.org/Plugin_API/Filter_Reference/wp_authenticate_user) 动作将确保验证码字段不为空并且答案正确的`validate_captcha_field()`方法包含在登录验证系统中。

下面是我们谈到的`captcha_display()`和`validate_captcha_field()`方法的代码。

```
/** Output the reCAPTCHA form field. */
    public function captcha_display() {
        ?>
        <script type="text/javascript"
                src="http://www.google.com/recaptcha/api/challenge?k=<?=$this->public_key;?>">
        </script>
        <noscript>
            <iframe src="http://www.google.com/recaptcha/api/noscript?k=<?=$this->public_key;?>"
                    height="300" width="300" frameborder="0"></iframe>
            <br>
            <textarea name="recaptcha_challenge_field" rows="3" cols="40">
            </textarea>
            <input type="hidden" name="recaptcha_response_field"
                   value="manual_challenge">
        </noscript>

    <?php
    }
```

```
/**
     * Verify the captcha answer
     *
     * @param $user string login username
     * @param $password string login password
     *
     * @return WP_Error|WP_user
     */
    public function validate_captcha_field($user, $password) {

        if ( ! isset( $_POST['recaptcha_response_field'] ) || empty( $_POST['recaptcha_response_field'] ) ) {
            return new WP_Error( 'empty_captcha', 'CAPTCHA should not be empty');
        }

        if( isset( $_POST['recaptcha_response_field'] ) && $this->recaptcha_response() == 'false' ) {
            return new WP_Error( 'invalid_captcha', 'CAPTCHA response was incorrect');
        }

        return $user;
    }
```

仔细查看`validate_captcha_field()`确切地说是第二个`if`条件语句，调用`recaptcha_response()`来检查验证码答案是否正确(即，如果验证码响应错误，则返回 false)。

我们来看看`recaptcha_response()`的代码和解释。

```
/**
     * Get the reCAPTCHA API response.
     *
     * @return string
     */
    public function recaptcha_response() {

        // reCAPTCHA challenge post data
        $challenge = isset($_POST['recaptcha_challenge_field']) ? esc_attr($_POST['recaptcha_challenge_field']) : '';

        // reCAPTCHA response post data
        $response  = isset($_POST['recaptcha_response_field']) ? esc_attr($_POST['recaptcha_response_field']) : '';

        $remote_ip = $_SERVER["REMOTE_ADDR"];

        $post_body = array(
            'privatekey' => $this->private_key,
            'remoteip'   => $remote_ip,
            'challenge'  => $challenge,
            'response'   => $response
        );

        return $this->recaptcha_post_request( $post_body );

    }
```

**代码解释:**为了验证用户提供的验证码答案是否正确，需要向端点`http://www.google.com/recaptcha/api/verify`发送一个 POST 请求，该请求包含以下参数或主体。

*   你的私人钥匙
*   `remoteip`解开验证码的用户的 IP 地址。
*   `challenge`通过表单发送的***recaptcha _ challenge _ field***的值。
*   `response`***的值 recaptcha _ response _ field***通过表单发送。

首先，表单发送的挑战和响应 POST 数据被捕获并分别保存到`$challenge`和`$response`。

用户的 IP 地址被`$_SERVER["REMOTE_ADDR"]`捕获并保存到`$remote_ip`。

要用 [HTTP API](https://www.sitepoint.com/the-wordpress-http-api/) 发送 POST 请求，参数或主体应该是如下的数组形式:

```
$post_body = array(
            'privatekey' => $this->private_key,
            'remoteip'   => $remote_ip,
            'challenge'  => $challenge,
            'response'   => $response
        );
```

POST 参数作为参数传递给`recaptcha_post_request()`，它将请求和参数一起发送给`https://www.google.com/recaptcha/api/verify`，并返回 API 响应。

如果验证码答案正确，则`recaptcha_post_request()`返回`true`，否则返回`false`。

下面是`recaptcha_post_request()`的代码

```
/**
     * Send HTTP POST request and return the response.
     *
     * @param $post_body array HTTP POST body
     *
     * @return bool
     */
    public function recaptcha_post_request( $post_body ) {

        $args = array( 'body' => $post_body );

        // make a POST request to the Google reCaptcha Server
        $request = wp_remote_post( 'https://www.google.com/recaptcha/api/verify', $args );

        // get the request response body
        $response_body = wp_remote_retrieve_body( $request );

        /**
         * explode the response body and use the request_status
         * @see https://developers.google.com/recaptcha/docs/verify
         */
        $answers = explode( "\n", $response_body );

        $request_status = trim( $answers[0] );

        return $request_status;
    }
```

**代码解释:**创建一个 POST 体`$post_body`保存到 key `body`的数组`$args`。

`wp_remote_post`向`$request`发送 POST 请求和响应 save。

响应体由`wp_remote_retrieve_body`检索并保存到`$response_body`。

如果验证码测试通过，reCAPTCHA API 返回:

```
true
success
```

否则，将返回以下错误

```
false
incorrect-captcha-sol
```

让`recaptcha_post_request`方法返回一个布尔值，即成功时返回`true`，失败时返回`false`；响应`$response_body`是[分解](http://php.net/manual/en/function.explode.php)并且带有索引`0`的数组数据[被修剪](http://php.net/manual/en/function.trim.php)以从字符串的开头和结尾移除任何冗余的空白。

最后，我们关闭插件类。

```
} // reCAPTCHA_Login_Form
```

我们完成了插件类的编码。为了让这个类工作，我们需要像这样实例化它:

```
new reCAPTCHA_Login_Form();
```

## 包裹

如果你想在你的 WordPress 站点上使用插件或者深入研究代码，[下载插件](https://github.com/Collizo4sky/WP-Login-Form-with-reCAPTCHA/archive/master.zip)。

这是演示如何在插件中使用 WordPress HTTP API 的系列文章中的第二篇。

一定要关注 WordPress 频道的[类似教程。](https://www.sitepoint.com/wordpress/)

编码快乐！

## 分享这篇文章