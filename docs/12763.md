# 将 JSON 用于独立于语言的配置文件

> 原文：<https://www.sitepoint.com/using-json-for-language-independent-configuration-files/>

服务器端框架中 <abbr title="JavaScript Object Notation">JSON</abbr> 解析的日益增长的可用性提升了 JavaScript 在客户端编程之外的用途，为通用数据交换格式提供了基本语法。好吧。

但是它的一个不太明显的优点是能够为 Ajax 开发提供独立于语言的配置文件。

简而言之，一个 JavaScript object-literal 可以被解析为 <abbr title="JavaScript Object Notation">JSON</abbr> with(比如) <abbr title="Hypertext Pre-Processor">PHP</abbr> 来创建一个相应的关联数组。

考虑一个简单的配置对象，如下所示:

```
const config = {
	'lang' : 'en', 
	'host' : 'sitepoint.com'
	};
```

我们可以将它包含在常规的`<script>`中，并用 JavaScript 访问它的属性:

```
alert(config['lang']);    //outputs "en"
```

一切都好。但是我们也可以将它导入到 <abbr title="Hypertext Pre-Processor">PHP</abbr> 中，并像这样解析它:

```
$datastring = file_get_contents('config.js');

$regexes = array(
	array("p"=>"/[w]*(//).*$/m", "r"=>""),    //remove comments
	array("p"=>"/'/m", "r"=>""")                //replace single-quotes with double-quotes
	);	

foreach($regexes as $regex)
{
	$datastring = preg_replace($regex['p'], $regex['r'], $datastring); 
}

preg_match("/config[ ]?=[ ]?{([^;]+)\;/", $datastring, $matches);

$config = json_decode('{' . $matches[1], true);
```

然后我们在一个 <abbr title="Hypertext Pre-Processor">PHP</abbr> 关联数组中有相同的数据:

```
echo $config['lang'];    //outputs "en"
```

## PHP 中 JSON 解析的可用性

本地函数 [json_encode](http://www.php.net/json_encode) 和 [json_decode](http://www.php.net/json_decode) 直到版本 5.2 才被添加到 <abbr title="Hypertext Pre-Processor">PHP</abbr> 中。如果你使用的是早期版本，你需要自己实现它们，为此我推荐 Michal Migurski 的 [JSON 服务类](http://pear.php.net/pepr/pepr-proposal-show.php?id=198)。唯一的缺点是它只支持转换为对象，不支持转换为关联数组(由本地函数中的`true`标志触发)。

然而，您可以通过递归对象到数组的转换来解决这个问题。这里有一个小片段可以做到这一点；这不是我写的，但我恐怕也记不起在哪里找到的:

```
function object_to_array($data) 
{
	if(is_array($data) || is_object($data))
	{
		$result = array(); 
		foreach($data as $key => $value)
		{ 
			$result[$key] = object_to_array($value); 
		}
		return $result;
	}
	return $data;
}
```

然后，您将能够像这样进行原始转换:

```
$config = object_to_array(json_decode('{' . $matches[1]));
```

## 结论

这样做的好处是显而易见的——应用程序的客户端和服务器端层都可以从单个托管源获取配置数据。

## 分享这篇文章