# 如何用 PHP 创建自己的 Twitter 小部件，第 2 部分

> 原文：<https://www.sitepoint.com/create-your-own-twitter-widget-2/>

在本系列第 1 部分中，我们研究了 Twitter API，创建了 PHP TwitterStatus 类，并以 JSON 格式导入了最新的 tweets。今天，我们将把数据填充到 HTML 模板中— [在这里下载完整的源代码](https://blogs.sitepointstatic.com/examples/tech/twitter/twitterwidget.zip)。

## 模板解析

我们已经将两个 HTML 模板定义为公共属性:

*   $WidgetTemplate 是小部件的外部 HTML。{TWEETS}代码表示$TweetTemplate 将出现在哪里
*   $TweetTemplate 是单个 tweet 的 HTML。

任一模板都可以包括来自 Twitter 提要的{命名值}，例如，

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

我们的代码必须提取所有{named-values}，查看 Twitter 数据中是否存在值，并相应地替换它。让我们创建一个私有函数来处理这个问题:

```
 private function ParseStatus($data, $template) { 
```

ParseStatus 接受两个值:

*   $data 是 Twitter JSON 的一部分，用于个人推文。
*   $template 是一个 HTML 模板(将传递$WidgetTemplate 或$TweetTemplate)。

第一步是使用正则表达式定位每个{命名值}:

```
 preg_match_all('/{(.+)}/U', $template, $m); 
```

PHP 的 [preg_match_all()](http://uk.php.net/manual/en/function.preg-match-all.php) 返回一个包含两个元素的数组。第一个是包含所有匹配的“{named-value}”字符串的数组。第二个是另一个数组，包含正则表达式的(。+)括号，即不带花引号的“命名值”。

我们可以遍历每个值，并确定它是否在 Twitter 数据中。注意，它可能位于“用户”部分(一个子关联数组)…

```
 for ($i = 0, $il = count($m[0]); $i < $il; $i++) {

	$name = $m[1][$i];

	// Twitter value found?
	$d = false;
	if (isset($data[$name])) {
		$d = $data[$name];
	}
	else if (isset($data['user'][$name])) {
		$d = $data['user'][$name];
	} 
```

在这段代码的末尾$d 要么是 false，要么是一个匹配的 tweet 值。我们现在可以在一个 [str_replace()](http://uk.php.net/manual/en/function.str-replace.php) 中使用它，但是我们需要首先处理两种特殊情况:

*   “文本”是 tweet 字符串，可能包含 URL、@ ids 或#hashtags。我们需要相应地将它们转换成，所以我们将截取数据并将其传递给一个名为 ParseTwitterLinks()的新函数。
*   “created_on”是推文发送的日期/时间。日期必须以不同的方式处理，原因将在下一篇文章中阐明。目前，我们只想表明我们有一个日期，所以我们将把这个值放在一个{DATE:…}字符串中。

```
 // replace data
		if ($d) {

			switch ($name) {

				// parse status links
				case 'text':
					if ($this->ParseLinks) {
						$d = $this->ParseTwitterLinks($d);
					}
					break;

				// tweet date
				case 'created_at':
					$d = "{DATE:$d}";
					break;

			}

			$template = str_replace($m[0][$i], $d, $template);

		}

	}
	// end of loop

	return $template;

}
// end of ParseStatus 
```

## 链接解析

我们现在需要一个私有的 ParseTwitterLinks()函数来翻译 tweets 中的链接(“文本”值)。传递了一个字符串:

```
 private function ParseTwitterLinks($str) { 
```

我们现在需要多一点正则表达式的魔力。首先，我们将寻找 URL——我们不关心它们是否格式良好，只要它们以“http”开头并包含有效字符:

```
 $str = preg_replace('/(https{0,1}://[w-./#?&=]*)/', '<a href="$1">$1</a>', $str); 
```

接下来，我们将检查@id 链接，并将其替换为一个适当的 Twitter 链接:

```
 $str = preg_replace('/@(w+)/', '@<a href="http://twitter.com/$1" class="at">$1</a>', $str); 
```

最后，我们将#hashtags 替换为 Twitter 搜索链接:

```
 $str = preg_replace('/s#(w+)/', ' <a href="http://twitter.com/#!/search?q=%23$1" class="hashtag">#$1</a>', $str); 
```

## 呈现我们的 HTML 小部件

我们现在已经有了将 Twitter feed 转换成 HTML 小部件所需的所有代码。这是在一个名为 Render()的新公共方法中完成的。首先，我们获取提要数据，并检查是否返回了结果:

```
 $json = $this->FetchFeed();
if ($json) { 
```

接下来，我们将为小部件本身定义两个字符串以及所有状态更新的集合:

```
 $widget = '';
	$status = ''; 
```

然后，我们可以在一个循环中检查每条推文:

```
 // examine all tweets
	for ($t = 0, $tl = count($json); $t < $tl; $t++) { 
```

如果这是第一条 tweet，我们将把值替换到$WidgetTemplate 中。这仅需要一次，因为外部小部件代码可能显示来自“用户”数组的信息，该数组随每条 tweet 一起提供(检查 [SitePoint feed](http://twitter.com/statuses/user_timeline/sitepointdotcom.xml?count=10) ):

```
 // parse widget template
		if ($t == 0) {
			$widget .= $this->ParseStatus($json[$t], $this->WidgetTemplate);
		} 
```

然后我们可以用每个 tweet 中的值替换$TweetTemplate 中的值:

```
 // parse tweet
		$status .= $this->ParseStatus($json[$t], $this->TweetTemplate); 
```

最后，我们结束循环并创建一个新的$render 字符串。它包含外部的$widget HTML，但是{TWEETS}被替换为$status HTML:

```
 }

	// parse Twitter links
	$render = str_replace('{TWEETS}', $status, $widget);
} 
```

假设我们使用上面的$WidgetTemplate 和$TweetTemplate 代码，我们得到的 HTML 应该类似于:

```
 <div>
<img src="http://a3.twimg.com/profile_images/76953980/Picture_29_normal.png" />
<strong>SitePoint</strong>
<ul>
<li>
Tweet 9:
Visit <a href="http://www.sitepoint.com">http://www.sitepoint.com</a>
{DATE:Thu Dec 23 10:10:18 +0000 2010}
</li>
<li>
Tweet 8:
Hello @<a href="http://twitter.com/craigbuckler" class="at">craigbuckler</a>
{DATE:Thu Dec 23 10:05:01 +0000 2010}
</li>
<li>
Tweet 7:
We love  <a href="http://twitter.com/#!/search?q=%23javascript" class="hashtag">#javascript</a>
{DATE:Thu Dec 23 10:00:00 +0000 2010}
</li>
</ul>
</div> 
```

这接近我们想要的，但我们仍然需要缓存结果 HTML，将日期转换为更友好的格式，并添加少量 CSS。[我们将在上一篇文章中讨论这个问题……](https://www.sitepoint.com/create-your-own-twitter-widget-3/)

## 分享这篇文章