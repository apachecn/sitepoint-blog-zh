# 为 WordPress 创建一个网址缩写插件

> 原文：<https://www.sitepoint.com/create-a-url-shortener-plugin-for-wordpress/>

URL 缩短是一种技术，在这种技术中，URL 的长度大大缩短，但仍然链接到所需的页面。这是通过在短域名上使用重定向来实现的，它链接到具有较长 URL 的网页。

在本教程中，我将向你展示如何使用 Google 的 URL Shortener API 为 WordPress 创建一个 URL 缩短插件。

## URL 缩短的原因

在我们开始构建 URL 缩短插件之前，让我们看几个例子，看看为什么我们可能需要这个插件:

*   在 Twitter 和其他一些消息服务上，一条消息可以包含的字符数是有限制的。因此，如果你发送长网址，它将占据大部分信息。
*   印刷书籍或展示标牌通常会使用简短的网址，因为它们易于阅读和输入。
*   [二维码](https://www.sitepoint.com/add-qr-codes-wordpress-posts/)有字符限制。很长的网址不适合，因此需要缩短网址。

## 获取 Google URL Shortener API 密钥

要使用 Google URL Shortener API，您需要获得一个 API 密钥。Google 使用这个 API 键来跟踪您的应用程序。

以下是获取 API 密钥的步骤:

*   访问[谷歌开发者控制台](https://console.developers.google.com/)。
*   选择现有项目或创建新项目。
*   在左侧栏中，单击并展开 APIs & auth。
*   接下来，单击 API。在 API 列表中，确保 Google URL Shortener API 的状态为 ON。
*   在左侧边栏中，选择“凭据”。然后生成一个公共访问密钥(如果还没有的话)。这个公共访问密钥就是 API 密钥。

## 插件目录和文件

我们的插件将包含一个目录和一个文件。结构如下:

```
 --url-shortener
      -url-shortener.php 
```

为了使插件可以安装，我们将这段代码放在**url-shortener.php**文件中:

```
<?php

/*
Plugin Name: URL Shortener
Plugin URI: http://www.sitepoint.com
Description: Create's a Shortened URL of every post.
Version: 1.0
Author: Narayan Prusty
*/
```

## 创建插件设置页面

我们需要为我们的插件创建一个设置页面，管理员可以输入 URL Shortener API 键。下面是使用 [WordPress 设置 API](https://www.sitepoint.com/wordpress-options-panel/) 创建设置页面的代码:

```
<?php

function url_shortener_settings_page()
{
    add_settings_section("section", "Enter Key Details", null, "url-shortener");
    add_settings_field("url-shortener-input-field", "API Key", "url_shortener_input_field_display", "url-shortener", "section");  
    register_setting("section", "url-shortener-input-field");
}

function url_shortener_input_field_display()
{
   ?>
        <input type="text" name="url-shortener-input-field" value="<?php echo get_option('url-shortener-input-field'); ?>" /> 
   <?php
}

add_action("admin_init", "url_shortener_settings_page");

function url_shortener_page()
{
  ?>
      <div class="wrap">
         <h1>URL Shortener Setting</h1>

         <form method="post" action="options.php">
            <?php
               settings_fields("section");
               do_settings_sections("url-shortener");
               submit_button(); 
            ?>
         </form>
      </div>
   <?php
}

function menu_item()
{
  add_submenu_page("options-general.php", "URL Shortener", "URL Shortener", "manage_options", "url-shortener", "url_shortener_page"); 
}

add_action("admin_menu", "menu_item");
```

我们将 API 密匙存储为一个名为`url-shortener-input-field`的 WordPress 选项。

设置页面应该是这样的:

![URL Shortner settings page](img/2831ac4962e42c77bce25d79c6995a3c.png)

## Google URL Shortener API 概述

要缩短一个长 URL，你需要发送一个 POST 请求到`https://www.googleapis.com/urlshortener/v1/url` URL，并附上你的 API 密匙和长 URL。

下面是一个请求示例:

```
POST https://www.googleapis.com/urlshortener/v1/url
Content-Type: application/json

{"longUrl": "https://www.sitepoint.com/"}
```

下面是示例响应的样子:

```
{
 "kind": "urlshortener#url",
 "id": "http://goo.gl/fqsT",
 "longUrl": "https://www.sitepoint.com/"
}
```

**注意:**您不能在一天内使用同一个 API 密钥发送超过一百万个请求。

## 在元框中显示缩短的 URL

我们希望在编辑后屏幕的元框中显示缩短的 URL。为了创建元框，我们将使用[元框 API](https://www.sitepoint.com/adding-custom-meta-boxes-to-wordpress/) ，为了缩短 URL，我们将使用 [WordPress HTTP API](https://www.sitepoint.com/the-wordpress-http-api/) 。

下面是在一个元框中显示我们缩短的 URL 的代码:

```
function url_shortener_meta_box_markup($object)
{
	$key = get_permalink($object->ID);

	if(get_option('url-shortener-input-field', '') != "")
	{
		if(get_option($key, "") != "")
		{
			echo get_option($key, "");
			return;
		}

		$url = 'https://www.googleapis.com/urlshortener/v1/url';

		$result = wp_remote_post(
			add_query_arg(
				'key', 
				get_option('url-shortener-input-field'), 
				'https://www.googleapis.com/urlshortener/v1/url'
			), 
			array(
				'body' => json_encode(array('longUrl' => esc_url_raw($key))),
				'headers' => array( 'Content-Type' => 'application/json')
			)
		);

		if(is_wp_error($result)){echo "Error"; return;}

		$result = json_decode($result['body']);
		$shortlink = $result->id;

		update_option($key, $shortlink);

		echo $shortlink;
	}
}

function url_shortener_meta_box()
{
    add_meta_box("url-shortener-meta-box", "Shorten URL", "url_shortener_meta_box_markup", "post", "side", "default", null);
}

add_action("add_meta_boxes", "url_shortener_meta_box");
```

下面是这段代码的工作原理:

*   我们使用`add_meta_box`函数创建了一个元框。
*   我们正在使用`get_permalink()`函数检索文章的长 URL。
*   然后，我们检查我们是否已经在数据库中有一个这个长 URL 的短 URL 作为 WordPress 选项。如果没有，那么我们将使用 HTTP API 检索它，并将其存储为一个 WordPress 选项。否则，我们使用现有的短网址。
*   这个插件与 Google URL shortener web 服务配合得很好，因为它不会每次都请求一个新的 URL，而是在检索到 URL 后存储它。

这是编辑后屏幕中元框的外观:

![Meta box in post edit screen](img/532f86fe4d73d2986b7e0b7ed0666646.png)

## 在前端显示短网址

我们还想在每个帖子下面显示缩短的 URL。下面是实现这一点的代码:

```
function url_shortener_content_filter($content)
{
	if($GLOBALS['post']->post_type != "post")
		return;

	$key = get_permalink($GLOBALS['post']->ID);

	if(get_option('url-shortener-input-field', '') != "")
	{
		if(get_option($key, "") != "")
		{
			$content = $content . "<p><b>Shortern URL: </b>" . get_option($key, "") . "</p>";
			return $content;
		}

		$url = 'https://www.googleapis.com/urlshortener/v1/url';

		$result = wp_remote_post(
			add_query_arg(
				'key', 
				get_option('url-shortener-input-field'), 
				'https://www.googleapis.com/urlshortener/v1/url'
			), 
			array(
				'body' => json_encode(array('longUrl' => esc_url_raw($key))),
				'headers' => array( 'Content-Type' => 'application/json')
			)
		);

		if(is_wp_error($result)){echo "Error"; return;}

		$result = json_decode($result['body']);
		$shortlink = $result->id;

		update_option($key, $shortlink);

		$content = $content . "<p><b>Shortern URL: </b>" . $shortlink . "</p>";
	}	

	return $content;
}

add_filter("the_content", "url_shortener_content_filter");
```

这段代码是这样工作的:

*   我们首先检查以确保 WordPress 正在处理一篇文章。如果是页面或自定义文章类型，那么我们不会显示短 URL。然而，如果你想在每一页都显示它，那么就从函数代码中删除前两行。
*   然后，我们所做的一切都和我们在 meta 框中显示短 URL 时所做的一样。唯一的区别是，我们没有回显它，而是将其连接到帖子内容。

这是它在前端的样子:

![Shortened URL Example](img/c09e839edd4365d458701b88d21fa48d.png)

现在我们已经为 WordPress 构建了一个很棒的 URL shortener 插件！

## WordPress.org 插件目录中流行的网址缩写插件

如果你想查看现有的插件，WordPress.org 插件目录中最受欢迎的两个网址缩短插件是[网址缩短器](https://wordpress.org/plugins/url-shortener/)和 [WP 网址缩短器](https://wordpress.org/plugins/wp-url-shorten/)。

WP URL Shorten 使用 **ref.li** 来缩短 URL。李提供实时统计和其他交通信息的人访问您的网站通过他们的缩短网址。

URL Shortener 插件可以让你在 **Bit.ly** 、 **Su.pr** 、 **YOURLS** 、 **Goo.gl** 和许多其他服务之间进行选择。这个插件还可以生成二维码。

## 结论

在本教程中，我向你展示了如何轻松地建立自己的网址缩短插件。现在，您可以继续进行扩展，添加更多功能，如二维码支持和使用其他 URL 缩短服务。请在下面分享你自己插件的经验。

## 分享这篇文章