# 如何用 PHP 创建自己的 Twitter 小部件，第 1 部分

> 原文：<https://www.sitepoint.com/create-your-own-twitter-widget-1/>

Twitter 上的状态窗口小部件十分便宜，为什么要创建自己的呢？因为你可以！您自己的小部件将比任何现成的解决方案更具可定制性，您将成为同行羡慕的对象。一些读者也向我们索要关于这个主题的文章，这是对 PHP、REST APIs、JSON、正则表达式和面向对象编程的很好的介绍。

![Twitter widget in PHP](img/60435271507ffc497f9088853d516445.png)

## 挑战

这是一个基于 [**源代码的 3 部分教程，你可以在这里下载**](https://blogs.sitepointstatic.com/examples/tech/twitter/twitterwidget.zip) 。我们的目标是:

*   查询 Twitter API，并为单个用户获取任意数量的状态更新。
*   将数据应用到一个可配置的 HTML 模板，并将 URL、@id 和#hashtags 转换为适当的链接。
*   将日期格式化为更友好的格式，例如十分钟前、昨天、两周前等等。
*   缓存小部件 HTML，以便在每次页面加载时不需要获取过程。
*   让生成的小部件可以在所有浏览器中工作——是的，包括 IE6！

## Twitter API

Twitter 提供了一个 REST API。以下 URL 获取特定用户发布的最后 N 条推文:

http://twitter.com/statuses/user_timeline/**用户标识**。**格式**？计数= **N**

其中:

*   **user_id** 是 Twitter 用户名
*   **格式**可以是 json、xml、rss 或者 atom
*   **N** 是返回的状态更新的数量

因此，以下 URL 以 XML 格式返回 Sitepoint 的最近 10 次更新:

[http://Twitter . com/status/user _ timeline/sitepointdotcom . XML？计数=10](http://twitter.com/statuses/user_timeline/sitepointdotcom.xml?count=10)

### JSON vs XML？

XML 的未来是一个热门的讨论话题，尤其是在 Twitter 决定从他们的流 API 中放弃这种格式，转而支持 JSON 之后。我不会在这里讨论这个问题，但是我的初衷是使用 XML 提要，并通过 XSLT 将其转换成 HTML。这变得有点太复杂了——主要是因为 tweet 中的链接必须使用正则表达式来解析。使用 XSLT 2.0 这是可能的，但不幸的是，PHP 的 libxslt 只支持 XSLT 1.0。

我因此选择 JSON 这是一种不太冗长的格式，可以被 PHP 的 [json_decode()](http://php.net/manual/en/function.json-decode.php) 函数解码。

## TwitterStatus 类

小部件功能封装在一个名为 TwitterStatus 的类中(请参考[下载](https://blogs.sitepointstatic.com/examples/tech/twitter/twitterwidget.zip)中的 twitter/twitterstatus.php 文件)。七个公共财产和一个私人财产的定义是:

```
 class TwitterStatus
{

	public $ID;				// twitter user name
	public $Count;			// tweets to fetch
	public $WidgetTemplate;	// widget template
	public $TweetTemplate;	// template for each tweet
	public $ParseLinks;		// parse links in Twitter status
	public $DateFormat;		// PHP or "friendly" dates
	public $CacheFor;		// number of seconds to cache feed

	private $cache;			// location of cache files 
```

**note:** Public and Private?

如果您是面向对象编程的新手，那么可以从类外部访问公共属性(变量)或方法(函数)，也就是说，我们可以从任何代码中设置和检查$ID 值，不管它位于何处。

私有属性或方法具有局部范围，只能在类本身内部使用。可以在 TwitterStatus 类中设置和检查$cache 属性，但是在其他地方看不到它。

PHP 还提供了受保护的属性和方法。我们在这里没有使用它们，但是受保护的成员在类内部以及对于继承类和父类是可见的。

我们现在需要一个构造函数，它在创建新的 TwitterStatus 对象时运行。我们的构造函数只是将每个属性设置为默认值:

```
 // constructor
public function __construct($id = null, $count = 0) {

	// constants
	$this->cache = __DIR__ . '/cache/';	// cache location
	$this->CacheFor = 900;				// cache feed for 15 minutes

	$this->ID = $id;
	$this->Count = $count;
	$this->ParseLinks = true;
	$this->DateFormat = 'friendly';

	// default widget template
	$this->WidgetTemplate =
		'<div class="twitterstatus">' .
		'<h2><a href="http://twitter.com/{screen_name}"><img src="{profile_image_url}" width="24" height="24" alt="{name}" />{name}</a></h2>' .
		'<ul>{TWEETS}</ul>' .
		'</div>';

	// default tweet template
	$this->TweetTemplate =
		'<li>{text} <em>{created_at}</em></li>';

} 
```

唯一的私有属性是$ cache——我们将在这个目录中存储小部件的缓存版本，这样我们就不会在每次页面加载时询问 Twitter API。它被设置为 twitterstatus.php 所在目录中名为“cache”的目录，需要设置适当的读/写权限。

当 TwitterStatus 对象被实例化时，可以设置$ID 和$Count 属性，

```
 $t = new TwitterStatus('sitepointdotcom', 10); 
```

或者我们可以分别更改属性，例如，

```
 $t = new TwitterStatus();
$t->ID = 'sitepointdotcom';
$t->Count = 10; 
```

如果$ParseLinks 设置为 true，我们的代码将把 tweet 中的链接转换为 HTML 锚标签。

$DateFormat 可以设置为一个 [PHP date()格式](http://php.net/manual/en/function.date.php)，例如

```
 $t->DateFormat = 'g:ia j F Y'; // 1:15pm 27 January 2011 
```

或者，可以将$DateFormat 设置为“friendly”(默认字符串)，以表明我们需要友好的日期(十分钟前、昨天、上周等)。)

最后，我们设置了两个 HTML 模板:

*   $WidgetTemplate 是小部件的外部 HTML。{TWEETS}代码指示一个或多个 TweetTemplate 块将出现在哪里。
*   $TweetTemplate 是单个 tweet 的 HTML。

任一属性可以包括来自 Twitter 订阅源的 *{named-values}* ，例如{text}、{source}、{name}、{location}、{profile_image_url}、{ statuses _ count }等。查看提要的 [XML 版本来定位命名值是最容易的。](http://twitter.com/statuses/user_timeline/sitepointdotcom.xml?count=10)

因此，开发者可以指定他们自己的窗口小部件 HTML，例如，

```
 $t->WidgetTemplate =
	'<div>' .
	'<img src="{profile_image_url}" />' .
	'<strong>{name}</strong>'
	'<ul>{TWEETS}</ul>' .
	'</div>';

$t->TweetTemplate =
	'<li>Tweet {statuses_count}: {text} {created_at}</li>'; 
```

## 获取 Twitter Feed

我们现在可以使用 [PHP 的客户端 URL (cURL)库](http://uk2.php.net/manual/en/book.curl.php)实现一个私有方法来获取 Twitter 状态数据:

```
 private function FetchFeed() {

	$r = '';
	if ($this->ID != '' && $this->Count > 0) {
		// fetch feed
		$c = curl_init();
		curl_setopt_array($c, array(
			CURLOPT_URL => 'http://twitter.com/statuses/user_timeline/' . $this->ID . '.json?count=' . $this->Count,
			CURLOPT_HEADER => false,
			CURLOPT_TIMEOUT => 10,
			CURLOPT_RETURNTRANSFER => true
		));
		$r = curl_exec($c);
		curl_close($c);
	}

	// return JSON as array
	return (!!$r ? json_decode($r, true) : false);
} 
```

设置了许多[卷曲选项](http://uk2.php.net/manual/en/function.curl-setopt.php)，但最重要的是超时。我们允许 Twitter 有 10 秒钟的响应时间，如果超过这个时间，PHP 将放弃，函数将返回 false。

如果一切正常，那么使用 [json_decode()](http://php.net/manual/en/function.json-decode.php) 解析结果，返回一个与 json 数据匹配的关联数组。

我们今天走了很多路，所以是休息的时候了。在下一篇文章的[中，我们将研究将 Twitter 数据转换成 HTML 代码的代码。](https://www.sitepoint.com/create-your-own-twitter-widget-2/)

## 分享这篇文章