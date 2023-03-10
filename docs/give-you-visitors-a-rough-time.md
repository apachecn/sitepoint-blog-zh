# 让你的访客不好过

> 原文：<https://www.sitepoint.com/give-you-visitors-a-rough-time/>

在网上冲浪时，我看到了劳伦斯·威尔莫特关于人们如何真正知道时间的项目页面。

当被问及时间时，我们通常不需要军事上准确的回答，所以不会说“现在是九点五十三分二十二秒”。我们通常会告知大概的时间。即“快十点了”或“刚过三点半”。然而在网络上，时间通常显示为“上午 12:24:13”，或者类似的时间——对访问者来说不太友好。

这给了我一个想法:在网站上做类似的事情怎么样。即显示你所说的时间。所以，事不宜迟，我给你:“RoughTime”一段 PHP 代码来表达时间——大概。

```
 < ?php
$hour = (int) date('g');
$minute = (int) date('i');

switch($minute){
	case ($minute >=0 && $minute <5):
		$roughTime = numberToWord($hour).'-ish';
	break;
	case ($minute >=5 && $minute <14):
		$roughTime = 'just gone '.numberToWord($hour);
	break;
	case ($minute >=15 && $minute <20):
		$roughTime = 'quarter past '.numberToWord($hour);
	break;
	case ($minute >=20 && $minute <25):
		$roughTime = 'nearly half '.numberToWord($hour);
	break;
	case ($minute >=25 && $minute <35):
		$roughTime = 'half '.numberToWord($hour);
	break;
	case ($minute >=35 && $minute <40):
		$roughTime = 'just gone half '.numberToWord($hour);
	break;
	case ($minute >=40 && $minute <50):
		$roughTime = 'quarter to '.numberToWord($hour + 1);
	break;
	case ($minute >=50):
		$roughTime =  'nearly '.numberToWord($hour +1);
	break;
}

function numberToWord($number){
	switch($number){
		case 1:
			$word = 'one';break;
		case 2:
			$word = 'two';break;
		case 3:
			$word = 'three';break;
		case 4:
			$word = 'four';break;
		case 5:
			$word = 'five';break;
		case 6:
			$word = 'six';break;
		case 7:
			$word = 'seven';break;
		case 8:
			$word = 'eight';break;
		case 9:
			$word = 'nine';break;
		case 10:
			$word = 'ten';break;
		case 11:
			$word = 'eleven';break;
		case 13:
			$word = 'one';break;
		default:
			$word = 'twelve';break;
	}
	return $word;
}

echo 'Its '.$roughTime;
?> 

```

代码故意处于非常原始的状态，因此您可以对其进行调整以适应环境。

它是如何工作的？
首先，它获取当前时间的分钟和小时作为整数。接下来，它根据过去小时的分钟数选择适当的消息，最后将小时数设置为一个单词。

“传统”显示时间方式的有用替代方式？继续给你的网站访问者制造麻烦，你永远不知道——他们可能会喜欢。

## 分享这篇文章