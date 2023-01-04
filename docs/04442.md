# OAuth，Twitter，WordPress HTTP API 和你

> 原文：<https://www.sitepoint.com/oauth-twitter-wordpress-http-api/>

在之前的教程中，我们深入研究了 [WordPress HTTP API](https://www.sitepoint.com/the-wordpress-http-api/) 。我们甚至构建了以下插件来展示它的真实用法:[域名 whois 和社交数据 widget](https://www.sitepoint.com/building-a-domain-whois-and-social-data-wordpress-widget/)；验证码保护插件为 WordPress [登录](https://www.sitepoint.com/integrating-a-captcha-with-the-wordpress-login-form/)，[注册](https://www.sitepoint.com/integrating-a-captcha-with-the-wordpress-registration-form/) & [评论](https://www.sitepoint.com/integrating-a-captcha-with-the-wordpress-comment-form/)；以及[用于停止一次性电子邮件地址注册的插件](https://www.sitepoint.com/stop-the-use-of-disposable-email-addresses-in-wordpress/)。

在本教程中，我们将了解到 [OAuth](http://oauth.net/) 的世界，Twitter 如何使用它来授权对其 API 的 HTTP 请求，最后，构建一个由 [WordPress HTTP API](https://www.sitepoint.com/the-wordpress-http-api/) 支持的 PHP 类，插件可以在使用 Twitter 时利用这个类。

## OAuth 简介

OAuth 是一种身份验证协议，它提供了一种简单、更安全、更可靠的方式来发布受保护的数据并与之交互。它允许用户在不共享密码的情况下代表他们批准应用程序。

如果你代表你的用户存储受保护的数据，他们不应该在网上散布他们的密码来获取这些数据。相反，您可以使用 OAuth 让您的用户访问他们的数据，同时保护他们的帐户凭证。

## 编写 PHP 类

我们将在编写`PHP class`代码时解释如何使用 OAuth 认证向 Twitter 发出 HTTP 请求。

首先，去 Twitter 的[应用管理中心](https://apps.twitter.com/)；创建一个应用程序来获取您的密钥和访问令牌。

在[hostoople.com](http://www.hostoople.com/creating-twitter-applications-get-keys-access-token/)可以找到创建 Twitter 应用程序和获取 API 密匙的分步指南

创建 PHP 类并包含存储各种参数的属性。这些概述如下。

```
class Twitter_API_WordPress {

	/** @var string OAuth access token */
	private $oauth_access_token;

	/** @var string OAuth access token secrete */
	private $oauth_access_token_secret;

	/** @var string Consumer key */
	private $consumer_key;

	/** @var string consumer secret */
	private $consumer_secret;

	/** @var array POST parameters */
	private $post_fields;

	/** @var string GET parameters */
	private $get_field;

	/** @var array OAuth credentials */
	private $oauth_details;

	/** @var string Twitter's request URL */
	private $request_url;

	/** @var string Request method or HTTP verb */
	private $request_method;
```

构造函数将接受 Twitter 的应用程序消费者(或 API)密钥和秘密的数组，以及访问令牌和访问令牌秘密，并将它们保存到各自的属性中。

```
/** Class constructor */
	public function __construct( $settings ) {

		if ( ! isset( $settings['oauth_access_token'] )
		     || ! isset( $settings['oauth_access_token_secret'] )
		     || ! isset( $settings['consumer_key'] )
		     || ! isset( $settings['consumer_secret'] )
		) {
			return new WP_Error( 'twitter_param_incomplete', 'Make sure you are passing in the correct parameters' );
		}

		$this->oauth_access_token        = $settings['oauth_access_token'];
		$this->oauth_access_token_secret = $settings['oauth_access_token_secret'];
		$this->consumer_key              = $settings['consumer_key'];
		$this->consumer_secret           = $settings['consumer_secret'];
	}
```

接下来是接受 HTTP 请求的 GET 或 POST 参数的方法。

```
/**
	 * Store the POST parameters
	 *
	 * @param array $array array of POST parameters
	 *
	 * @return $this
	 */
	public function set_post_fields( array $array ) {
		$this->post_fields = $array;

		return $this;
	}

	/**
	 * Store the GET parameters
	 *
	 * @param $string
	 *
	 * @return $this
	 */
	public function set_get_field( $string ) {
		$this->getfield = $string;

		return $this;
	}
```

私有方法`_build_signature_base_string()`接受以下参数来创建签名基本字符串:请求 URL、请求方法或 HTTP 动词和 OAuth 凭证(消费者密钥和秘密；访问令牌和密码；以及 GET 参数(如果是 GET 请求的话)。

```
/**
	 * Create a signature base string from list of arguments
	 *
	 * @param string $request_url request url or endpoint
	 * @param string $method HTTP verb
	 * @param array $oauth_params Twitter's OAuth parameters
	 *
	 * @return string
	 */
	private function _build_signature_base_string( $request_url, $method, $oauth_params ) {
		// save the parameters as key value pair bounded together with '&'
		$string_params = array();

		ksort( $oauth_params );

		foreach ( $oauth_params as $key => $value ) {
			// convert oauth parameters to key-value pair
			$string_params[] = "$key=$value";
		}

		return "$method&" . rawurlencode( $request_url ) . '&' . rawurlencode( implode( '&', $string_params ) );
	}
```

`_generate_oauth_signature()`私有方法接受创建的签名基本字符串来生成 OAuth 签名。

```
private function _generate_oauth_signature( $data ) {

	// encode consumer and token secret keys and subsequently combine them using & to a query component
	$hash_hmac_key = rawurlencode( $this->consumer_secret ) . '&' . rawurlencode( $this->oauth_access_token_secret );

	$oauth_signature = base64_encode( hash_hmac( 'sha1', $data, $hash_hmac_key, true ) );

	return $oauth_signature;
}
```

`build_oauth()`创建一个包含以下数据的数组，并将其保存到`oauth_details`属性中，稍后`authorization_header()`将使用该属性来生成授权头。

*   **oauth _ consumer _ key**–Twitter 应用消费者密钥。
*   **oauth _ nonce**–一个随机字符串，由客户端唯一生成，允许服务器验证请求以前从未被提出过，通常使用`time()`或`mt_rand()`创建。
*   **oauth _ signature _ method**–常用的签名方法*“HMAC-SHA1”*
*   **oauth _ token**–应用 OAuth 令牌。
*   **oauth _ timestamp**–当前时间戳由`time()`创建
*   oauth _ version–Twitter 使用 1.0 版本
*   **OAuth _ signature**–OAuth 签名由`_generate_oauth_signature()`生成

请求方法或 HTTP 动词也被保存到`request_method`属性中。

```
/**
	 * Build, generate and include the OAuth signature to the OAuth credentials
	 *
	 * @param string $request_url Twitter endpoint to send the request to
	 * @param string $request_method Request HTTP verb eg GET or POST
	 *
	 * @return $this
	 */
	public function build_oauth( $request_url, $request_method ) {
		if ( ! in_array( strtolower( $request_method ), array( 'post', 'get' ) ) ) {
			return new WP_Error( 'invalid_request', 'Request method must be either POST or GET' );
		}

		$oauth_credentials = array(
			'oauth_consumer_key'     => $this->consumer_key,
			'oauth_nonce'            => time(),
			'oauth_signature_method' => 'HMAC-SHA1',
			'oauth_token'            => $this->oauth_access_token,
			'oauth_timestamp'        => time(),
			'oauth_version'          => '1.0'
		);

		if ( ! is_null( $this->get_field ) ) {
			// remove question mark(?) from the query string
			$get_fields = str_replace( '?', '', explode( '&', $this->get_field ) );

			foreach ( $get_fields as $field ) {
				// split and add the GET key-value pair to the post array.
				// GET query are always added to the signature base string
				$split                          = explode( '=', $field );
				$oauth_credentials[ $split[0] ] = $split[1];
			}
		}

		// convert the oauth credentials (including the GET QUERY if it is used) array to query string.
		$signature = $this->_build_signature_base_string( $request_url, $request_method, $oauth_credentials );

		$oauth_credentials['oauth_signature'] = $this->_generate_oauth_signature( $signature );

		// save the request url for use by WordPress HTTP API
		$this->request_url = $request_url;

		// save the OAuth Details
		$this->oauth_details = $oauth_credentials;

		$this->request_method = $request_method;

		return $this;
	}
```

这是我们谈到的`authorization_header()`方法的代码。

```
/**
	 * Generate the authorization HTTP header
	 * @return string
	 */
	public function authorization_header() {
		$header = 'OAuth ';

		$oauth_params = array();
		foreach ( $this->oauth_details as $key => $value ) {
			$oauth_params[] = "$key=\"" . rawurlencode( $value ) . '"';
		}

		$header .= implode( ', ', $oauth_params );

		return $header;
	}
```

根据请求方法，`process_request()`将使用`wp_remote_get()`或`wp_remote_post()`发送 GET 或 POST 请求，随后使用`wp_remote_retrieve_body()`返回响应。

```
/**
	 * Process and return the JSON result.
	 *
	 * @return string
	 */
	public function process_request() {

		$header = $this->authorization_header();

		$args = array(
			'headers'   => array( 'Authorization' => $header ),
			'timeout'   => 45,
			'sslverify' => false
		);

		if ( ! is_null( $this->post_fields ) ) {
			$args['body'] = $this->post_fields;

			$response = wp_remote_post( $this->request_url, $args );

			return wp_remote_retrieve_body( $response );
		}

		else {

			// add the GET parameter to the Twitter request url or endpoint
			$url = $this->request_url . $this->get_field;

			$response = wp_remote_get( $url, $args );

			return wp_remote_retrieve_body( $response );

		}

	}
```

为了更好地理解 WordPress HTTP API 和它是如何工作的，请看这个教程。

最后，我们结束课程。

```
} // Twitter_API_WordPress
```

**请注意:**在`set_post_fields()`、`set_get_field()`、`build_oauth()`中，为了支持方法链接，每个方法中都返回了对象`$this`。

示例:

```
$SomeObject->getObjectOne()->getObjectTwo()
```

为了更好的理解，请看下面的类用法。

## 如何使用该类

这个类必须在 WordPress 插件的上下文中使用。它不能作为一个独立的类工作，因为它需要 WordPress HTTP API 来工作。

要获得您最近的推文列表或集合，请遵循以下指南。
注意:[https://api.twitter.com/1.1/statuses/user_timeline.json](https://dev.twitter.com/rest/reference/get/statuses/user_timeline)是检索最近 tweet 数据的资源 URL。

首先，创建一个访问键和令牌的数组。

```
$settings = array(
	'oauth_access_token' => "211978035-tcdnwn5GlzeY9tKiMqTvkSLNPAgcwO5ABtqwgx18",
	'oauth_access_token_secret' => "rS0CMxoVNmcUYG5nWi2OhY8bJdFnK3p4W99KSyJ5BU7Iv",
	'consumer_key' => "qFt8kyGlietTJoduRItBAU2oJ",
	'consumer_secret' => "YWlaR5amBQWlwB62Uah8hjNoCnYYme7tMcrUDg0Z9SKaFvh4eC"
);
```

设置请求 URL 和方法，其中`w3guy`是您的 Twitter 用户名。

```
$url            = 'https://api.twitter.com/1.1/statuses/user_timeline.json';
$getfield       = '?screen_name=w3guy';
$request_method = 'GET';
```

最后，像这样处理请求。

```
$twitter_instance = new Twitter_API_WordPress( $settings );

$result = $twitter_instance
	->set_get_field( $getfield )
	->build_oauth( $url, $request_method )
	->process_request();
```

如果一切顺利，变量`$result`将被你最近的 tweets 的 JSON 数据填充。

例如，对于帖子请求，假设您想要更新您的个人资料描述。

```
$settings = array(
	'oauth_access_token'        => "211978035-tcdnwn5GlzeY9tKiMqTvkSLNPAgcwO5ABtqwgx18",
	'oauth_access_token_secret' => "rS0CMxoVNmcUYG5nWi2OhY8bJdFnK3p4W99KSyJ5BU7Iv",
	'consumer_key'              => "qFt8kyGlietTJoduRItBAU2oJ",
	'consumer_secret'           => "YWlaR5amBQWlwB62Uah8hjNoCnYYme7tMcrUDg0Z9SKaFvh4eC"
);

/** POST fields required by the URL above. See relevant docs as above **/
$url = 'https://api.twitter.com/1.1/account/update_profile.json';

$postField = array(
	'description' => 'Web Developer, Writer, Geek'
);

$request_method = 'POST';

$instance = new Twitter_API_WordPress( $settings );

$update = $instance
	->set_post_fields( $postField )
	->build_oauth( $url, $request_method )
	->process_request();
```

## 信贷和资源

这个类的结构和代码受到了 James Mallison 的 PHP Twitter 客户端的启发。

要了解关于 Twitter API 和 OAuth 的更多信息，请参阅下面的参考资料。

*   [OAuth 认证解密](http://nouncer.com/oauth/authentication.html)
*   【Twitter oAuth 如何在场景环境中工作
*   [Twitter 的文档](https://dev.twitter.com/oauth)
*   [创建 PHP OAuth 服务器](https://www.sitepoint.com/creating-a-php-oauth-server/)
*   IETF RFC

## 结论

在本文中，我们了解了 OAuth 和如何使用由 T2 WordPress HTTP API 支持的 HTTP 客户端类来使用 Twitter。如前所述，这个类应该在 WordPress 插件中使用，因为它使用了 WordPress HTTP API，它只在 WordPress 被加载时才出现或被实例化。这个 PHP 类可以在构建中派上用场，例如，[最近的 tweets 小部件](http://code.tutsplus.com/tutorials/create-a-twitter-widget-with-the-latest-twitter-api--cms-21535)。

该代码可在 [GitHub](https://github.com/Collizo4sky/Twitter-WordPress-HTTP-Client) 上获得。随意派生甚至提交拉取请求。

一定要订阅 [WordPress 频道](https://www.sitepoint.com/wordpress/)来了解我即将推出的教程。

快乐编码。

## 分享这篇文章