# 如何在 PHP 中创建自己的随机数生成器

> 原文：<https://www.sitepoint.com/php-random-number-generator/>

计算机不能产生随机数。一台以 1 和 0 工作的机器不可能神奇地发明自己的随机数据流。然而，计算机可以实现产生伪随机数的数学算法。它们看起来像随机数。他们感觉像随机分布。但它们是假的。如果运行该算法两次，将生成相同的数字序列。

## 种植随机种子

为了增加明显的随机性，可以向大多数算法传递一个种子——随机序列的初始化数。传递相同的种子两次仍然会生成相同的随机数集，但是您可以根据外部输入因素来设置种子。最简单的选项是当前时间，但它可以是任何时间；最后一次按键、鼠标移动、温度、在 YouTube 上浪费的时间或任何其他因素。

## 随机 PHP 函数

PHP 提供了许多随机数函数。主要的有:

1.  [rand()](http://php.net/manual/en/function.rand.php) 和更高效的 [mt_rand()](http://www.php.net/manual/en/function.mt-rand.php) 函数。两者都返回一个介于零和[getrandmax()](http://www.php.net/manual/en/function.getrandmax.php)/[mt _ getrandmax()](http://www.php.net/manual/en/function.mt-getrandmax.php)之间的随机数。或者，您可以传递最小和最大参数:

    ```
     // random number between 0 and 10 (inclusive)
    echo mt_rand(0, 10); 
    ```

2.  [srand(＄seed)](http://www.php.net/manual/en/function.srand.php)和[mt _ srand(＄seed)](http://www.php.net/manual/en/function.mt-srand.php)设置一个随机数种子。从 PHP 4.2.0 开始，这已经自动完成了。

## PHP 太随意了！

有些情况下，创建伪随机数的可重复列表是有用的。它通常用于安全或验证目的，例如，在传输密码之前对其进行加密，或者为一组数据生成哈希代码。不幸的是，PHP 可能有点太随机了。生成的序列将取决于您的主机平台和 PHP 版本。换句话说，你不能保证相同的‘随机’序列会在两台不同的机器上生成两次，即使使用了相同的种子。

## 滚动你自己的随机类

幸运的是，我们可以编写自己的随机数生成器。你会在网上找到很多算法，但这是最短最快的算法之一。首先，我们初始化我们的类和一个随机种子变量:

```
 class Random {

	// random seed
	private static $RSeed = 0; 
```

接下来，我们有一个 seed()函数来设置新的种子值。为了使算法正确工作，种子应该总是大于零的正数，但不要大到导致数学溢出。seed 函数接受任何值，但将其转换为 1 到 9，999，999 之间的数字:

```
 // set seed
	public static function seed($s = 0) {
		self::$RSeed = abs(intval($s)) % 9999999 + 1;
		self::num();
	} 
```

最后，我们使用 num()函数来生成一个介于$min 和$max 之间的随机数。如果没有设置种子，它会用 PHP 自己的随机数生成器进行初始化:

```
 // generate random number
	public static function num($min = 0, $max = 9999999) {
		if (self::$RSeed == 0) self::seed(mt_rand());
		self::$RSeed = (self::$RSeed * 125) % 2796203;
		return self::$RSeed % ($max - $min + 1) + $min;
	}

} 
```

我们现在可以设置一个种子并输出一系列数字:

```
 // set seed
Random::seed(42);

// echo 10 numbers between 1 and 100
for ($i = 0; $i < 10; $i++) {
	echo Random::num(1, 100) . '<br />';
} 
```

如果您完全复制了这段代码，那么无论您运行的是什么操作系统或 PHP 版本，您都会看到以下值:

```
76
86
14
79
73
2
87
43
62
7

```

不可否认，可重复的*【random】*数字并不是你每天都需要的——你更可能需要更接近真实随机性的东西，PHP 的功能会更好地为你服务。但是有时你会发现这很有用。

## 分享这篇文章