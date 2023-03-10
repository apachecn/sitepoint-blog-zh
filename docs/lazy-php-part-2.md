# 懒惰 PHP:第 2 部分

> 原文：<https://www.sitepoint.com/lazy-php-part-2/>

继[懒惰 PHP:第 1 部分](https://www.sitepoint.com/blog/)之后，又到了变懒的时候了，还有一些[的懒惰评价](http://c2.com/cgi/wiki?LazyEvaluation)。

**变量函数**
PHP 的一个鲜为人知的特性是它能够将函数名放入变量中，并通过变量的调用函数*，正如手册中的[这里的](http://www.php.net/manual/en/functions.variable-functions.php)所解释的。*

比如使用 [strtolower()](http://www.php.net/strtolower) 函数；

```
 $function = 'strtolower';

$string = 'HELLO WORLD!';

echo $function($string); // displays 'hello world!' 

```

不错，但是有什么意义呢？最近我需要一个内置 [array_change_key_case()](http://www.php.net/array_change_key_case) 函数的本地 PHP 实现，用于低于 4.2.0 的 PHP 版本(这是它可用的时候)。感谢第一个用户在 2004 年 2 月 5 日提交的评论，我甚至不需要动脑子。马上就有了这个；

```
 function array_change_key_case($array, $changeCase = CASE_LOWER) {
	$return = array();
	foreach($array as $key => $value) {
		switch($changeCase) {
			case CASE_LOWER:
				$return[strtolower($key)] = $value;
			break;
			case CASE_UPPER:
			default:
				$return[strtoupper($key)] = $value;
			break;
		}
	}
	return $return;
}

```

[small]注意使用$return 数组是因为 array_change_key_case()的内置实现消除了重复的键。[/小]

但是这里有一个性能问题。对于数组中的每个元素*，必须评估切换条件。对于大型阵列来说，这可能会成为很大的开销。我想要的是评估病情一次，而且只能评估一次。*

这就是一个可变函数可以发挥作用的地方；

```
 function array_change_key_case($array, $changeCase = CASE_LOWER) {
	switch($changeCase) {
		case CASE_LOWER:
			$caseFunc = 'strtolower';
		break;
		case CASE_UPPER:
                default:
			$caseFunc = 'strtoupper';
		break;
	}
	$return = array();
	foreach($array as $key => $value) {
		$return[$caseFunc($key)] = $value;
	}
	return $return;
} 

```

通过将我要使用的函数名(strtolower()或 strtoupper())放在一个变量中，在我开始遍历数组之前，条件只被计算一次。对于小型阵列，性能优势微不足道，但是当您处理大量数据时，您可能会开始注意到差异…

**Lambda Functions**
现在能够在一个变量中只放置一个函数名是很好的，但是当你看着 [create_function()](http://www.php.net/create_function) ，一个用于创建其他函数的 PHP 函数时，生活变得更加有趣！

本质上，它允许您创建一个函数并将其存储在一个变量中，而不必给函数命名。

这里有一个简单的例子。

```
 $lambda = create_function('$str', 'return strtolower($str);');

$string = 'HELLO WORLD!';

$string = $lambda($string);

echo $string; // Displays 'hello world!' 

```

第一行实际上是在对 PHP 说，创建一个类似这样的函数；

```
 function "anonymous"($str) {
    return strtolower($str);
} 

```

…但是*没有*给函数命名(为了举例，我用了“匿名”)。

你也可以在 create_function()中使用引用，例如:

```
 $lambda = create_function('& $str', '$str = strtolower($str);');

$string = 'HELLO WORLD!';

$lambda($string);

echo $string; // Displays 'hello world!' 

```

第一行，如果你把它声明为一个函数，看起来会像这样；

```
 function "anonymous"(& $str) {
    $str = strtolower($str);
} 

```

同样，在处理大量数据时，这很方便。

使用一个与上面类似的假设性示例，假设您需要一个函数来计算您的在线商店(nRonOnline.com)的销售税(VAT)。假设增值税设定为 17%,那么这需要添加到你销售的商品的价格中。

作为一个投机取巧的人，你发现了一个通过篡改一些可疑的数字来获取额外利润的机会。对于客户，您将计算税费，并将*四舍五入到最接近的整数。但是当涉及到向税务人员申报时，你会*四舍五入*含税的数字(**不要在家里尝试这个！**)。*

第一次尝试时，你写了一个函数来解决这个问题；

```
 function calcTax($array, $round = ROUND_DOWN) {
	foreach ( $array as $key => $value ) {
		switch ( $round ) {
			case ROUND_UP:
				$array[$key] = ceil($number * 1.17);
			break;
			case ROUND_DOWN:
			default:
				$array[$key] = floor($number * 1.17);
			break;
		}
	}
	return $array;
} 

```

不过，问题还是在于性能。由于您的商店一直在疯狂销售，您正在处理一些非常大的阵列，这需要很长时间。

你可以使用一个 lambda 函数，并在开始时对条件求值一次，而不是在循环中使用开关条件。

```
 function calcTax($array, $round = ROUND_DOWN) {
    switch ( $round ) {
        case ROUND_UP:
            $func = create_function('$number', 'return ceil($number * 1.17);');
        break;
        case ROUND_DOWN:
        default:
            $func = create_function('$number', 'return floor($number * 1.17);');
        break;
    }
    foreach ( $array as $key => $value ) {
        $array[$key] = $func($value);
    }

    return $array;
} 

```

如果你想了解更多关于 lambda 函数的知识，最好去问离你最近的 [Python 程序员](http://www.python.org)。例如[马克·皮尔格林](http://diveintomark.org/)做得很好[在](http://www.diveintopython.org/power_of_introspection/lambda_functions.html)[深入 Python](http://www.diveintopython.org/) 中解释 lambda 函数。

无论如何，今天足够的懒惰 PHP。

## 分享这篇文章