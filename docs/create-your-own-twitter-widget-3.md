# 如何用 PHP 创建自己的 Twitter 小部件，第 3 部分

> 原文：<https://www.sitepoint.com/create-your-own-twitter-widget-3/>

在本系列第 1 部分中，我们研究了 Twitter API，创建了 PHP TwitterStatus 类，并以 JSON 格式导入了最新的 tweets。在[第 2 部分](https://www.sitepoint.com/create-your-own-twitter-widget-2)，我们解析了 Twitter 数据，替换了链接，并为我们的小部件生成了完整的 HTML。在这最后一篇文章中，我们将缓存我们的小部件，并将推文日期转换成更友好的格式——[在这里下载完整的源代码](https://blogs.sitepointstatic.com/examples/tech/twitter/twitterwidget.zip)。

## 缓存的案例

目前，我们的 TwitterStatus 类将在每次调用 Render()方法时获取最近的 tweets。检索和解析这些数据需要几秒钟的时间，并且会产生大量的处理开销。这完全没有必要；我们的网站每秒钟可以接待 10 名访客，但每个月只能发布一条推文。此外，Twitter 将防止应用程序对 API 进行过多不必要的调用。

因此，我们将把生成的 HTML 代码缓存在本地系统的一个文件中。如果该文件的年龄小于$CacheFor seconds(一个公共属性)，那么它将被检索和使用，而不是询问 Twitter API。

因此，我们的 Render()方法将在本地$cache 字符串中生成一个文件名。它被设置为$this->cache(缓存数据的目录)，后跟 Twitter ID、破折号、tweets 的数量和. html 扩展名，例如'/Twitter/cache/sitepointdotcom-10 . html ':

```
 public function Render() {

	// returned HTML string
	$render = '';

	// cached file available?
	$cache = $this->cache . $this->ID . '-' . $this->Count . '.html'; 
```

然后，我们可以计算自文件创建以来已经过期的秒数(假设它存在):

```
 $cacheage = (file_exists($cache) ? time() - filemtime($cache) : -1); 
```

如果缓存文件不存在或者秒数大于$CacheFor，我们需要使用我们在[第 2 部分](https://www.sitepoint.com/create-your-own-twitter-widget-2)中编写的代码重新生成 HTML 小部件缓存文件:

```
 if ($cacheage < 0 || $cacheage > $this->CacheFor) { 
```

如果在$render 字符串中正确生成了 HTML，我们可以将它保存到缓存文件中:

```
 // update cache file
	file_put_contents($cache, $render); 
```

**important:** File permissions

文件权限是您在使用这段代码时最有可能遇到的问题，也是最令人沮丧的问题。您的 web 服务器和/或 PHP 必须有权限读取、写入和修改缓存目录中的文件。在 Linux 和 Mac 系统上，chmod 755 可能就足够了…但并不总是如此。在 Windows 上，通常是向 IIS 用户帐户授予适当的文件夹权限。

在我提供的代码中，缓存失败将简单地从 Twitter 重新获取数据。这在生产环境中并不总是可取的——引发一个错误可能更好。

如果存在有效的缓存文件，则$render 字符串将为空。如果 Twitter API 调用失败，它也将为空；在这种情况下，我们将检索最后一个可用的缓存文件:

```
 // fetch from cache
if ($render == '' && $cacheage > 0) {
	$render = file_get_contents($cache);
} 
```

为了完成我们的 Render()方法，我们将$render 返回给调用代码。然而，我们必须首先解析日期…

```
 return $this->ParseDates($render); 
```

## 日期解析

在[第 2 部分](https://www.sitepoint.com/create-your-own-twitter-widget-2)中，您会注意到 tweet 的创建日期显示为“{ DATE:Thu Dec 23 10:00:00+0000 2010 }”。那么，在生成 HTML 时，我们为什么不把它们转换成格式良好的日期呢？

如果我们只想显示 tweet 的日期和时间，我们可以在缓存阶段解析它，得到类似于“2010 年 12 月 23 日上午 10:10”的结果但是，我们可以选择显示相对日期字符串，如“刚才”、“10 分钟前”、“昨天”、“上周”或“3 个月前”如果这些是在缓存阶段生成的，它们可能在几分钟内就不正确了。

例如，如果一条推文显示为“2 分钟前”，该文本将在缓存文件的生命周期内保持静态。如果我们缓存了 15 分钟，并在 5 分钟后重新访问该页面，它仍然会显示“2 分钟前”，而不是“7 分钟前”因此，我们必须将日期/时间存储在缓存文件中，并在显示小部件时翻译它。

日期由私有的 ParseDates 方法解析，该方法传递给我们的呈现/缓存 HTML。首先，它将当前时间存储为$now，因此我们可以在相对日期计算中使用它:

```
 private function ParseDates($str) {

	// current datetime
	$now = new DateTime(); 
```

正则表达式用于使用 [preg_match_all()](http://uk.php.net/manual/en/function.preg-match-all.php) 将所有日期提取到$m 数组中。然后，我们遍历每个匹配的日期，并将$stime 设置为一个新的 DateTime 对象:

```
 preg_match_all('/{DATE:(.+)}/U', $str, $m);
	for ($i = 0, $il = count($m[0]); $i &lt; $il; $i++) {

		$stime = new DateTime($m[1][$i]); 
```

现在是棘手的部分。如果开发人员选择了“友好的”日期格式，我们将使用 [PHP 的 DateTime diff()函数](http://uk.php.net/manual/en/datetime.diff.php)返回一个 [DateInterval 对象](http://uk.php.net/manual/en/class.dateinterval.php)。

我们现在知道了年(＄y date interval 属性)、月(＄m)、日(＄d)、小时(＄h)、分钟(＄I)和秒(＄s)之间的区别，但是我们的逻辑需要更加模糊一些。例如，如果一条推文是在 2 分 50 秒前发送的，最好显示“3 分钟前”同样，如果是 11 个月零 20 天前，最好显示“去年”

友好日期格式的结果存储为名为$date:

```
 if ($this->DateFormat == 'friendly') {

			// friendly date format
			$ival = $now->diff($stime);

			$yr = $ival->y;
			$mh = $ival->m + ($ival->d > 15);
			if ($mh > 11) $yr = 1;
			$dy = $ival->d + ($ival->h > 15);
			$hr = $ival->h;
			$mn = $ival->i + ($ival->s > 29);

			if ($yr > 0) {
				if ($yr == 1) $date = 'last year';
				else $date = $yr . ' years ago';
			}
			else if ($mh > 0) {
				if ($mh == 1) $date = 'last month';
				else $date = $mh . ' months ago';
			}
			else if ($dy > 0) {
				if ($dy == 1) $date = 'yesterday';
				else if ($dy < 8) $date = $dy . ' days ago';
				else if ($dy < 15) $date = 'last week';
				else $date = round($dy / 7) . ' weeks ago';
			}
			else if ($hr > 0) {
				$hr += ($ival->i > 29);
				$date = $hr . ' hour' . ($hr == 1 ? '' : 's') . ' ago';
			}
			else {
				if ($mn < 3) $date = 'just now';
				else $date = $mn . ' minutes ago';
			}

		} 
```

或者，开发人员可能更喜欢$DateFormat 属性中指定的标准 [PHP date()格式](http://php.net/manual/en/function.date.php):

```
 else {
			// standard PHP date format
			$date = $stime->format($this->DateFormat);
		} 
```

我们现在可以相应地替换编码的日期，并将我们的字符串返回给调用 Render()方法:

```
 // replace date
		$str = str_replace($m[0][$i], $date, $str);

	}

	return $str;
} 
```

![Twitter widget in PHP](img/60435271507ffc497f9088853d516445.png)

## 使用我们的 TwitterStatus 类

我们的 TwitterStatus 类现在已经完成。通过包含()TwitterStatus 代码、实例化 TwitterStatus 对象并回显 Render()方法的结果，可以在任何 PHP 代码中使用它，例如，

```
 <?php
include('twitter/twitterstatus.php');

// show last 10 updates from sitepointdotcom
$t = new TwitterStatus('sitepointdotcom', 10);
echo $t->Render();
?> 
```

少量的 CSS 将把我们的 HTML 转换成一个令人愉快的 Twitter feed 小部件。

我希望这一系列的帖子对你有用。请 [**下载源文件**](https://blogs.sitepointstatic.com/examples/tech/twitter/twitterwidget.zip) ，随心所欲使用。一个链接回到网站点是赞赏的。

## 分享这篇文章