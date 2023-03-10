# PHP 中的随机性——你觉得幸运吗？

> 原文：<https://www.sitepoint.com/randomness-php-feel-lucky/>

![Cryptography Randomness in PHP](img/6095aaa98ebae8bf2f88db3d83dc6922.png "Cryptography Randomness in PHP")

本文分析了与用于加密目的的随机数生成相关的问题。PHP 5 没有提供一个简单的机制来生成加密的强随机数，而 PHP 7 通过引入几个 [CSPRNG](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator) 函数来解决这个问题。

## 什么是 CSPRNG？

引用[维基百科](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator)的话，密码安全伪随机数发生器(CSPRNG)是一种伪随机数发生器(PRNG)，其属性使其适合在密码学中使用。

CSPRNG 主要用于:

*   密钥生成(例如，复杂密钥的生成)
*   为新用户帐户创建随机密码
*   加密系统

保持高安全级别的一个重要方面是高质量的随机性。

## PHP 7 中的 CSPRNG

PHP 7 引入了两个可以用于 CSPRNG 的新函数: [`random_bytes`](http://php.net/manual/en/function.random-bytes.php) 和 [`random_int`](http://php.net/manual/en/function.random-int.php) 。

`random_bytes`函数返回一个`string`并接受一个`int`作为输入，该输入代表要返回的字节长度。

示例:

```
$bytes = random_bytes('10'); var_dump(bin2hex($bytes));  //possible ouput: string(20) "7dfab0af960d359388e6" 
```

`random_int`函数返回给定范围内的整数。
举例:

```
var_dump(random_int(1,  100));  //possible output: 27
```

## 在幕后

上述函数的随机性来源因环境而异:

*   在 Windows 上，将始终使用 [`CryptGenRandom()`](https://msdn.microsoft.com/en-us/library/windows/desktop/aa379942%28v=vs.85%29.aspx) 。
*   在其他平台上， [`arc4random_buf()`](http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man3/arc4random.3) 如果可用的话就会被使用(在 BSD 衍生或带有 libbsd 的系统上为真)。
*   如果以上失败，将使用 Linux [getrandom(2)](http://man7.org/linux/man-pages/man2/getrandom.2.html) 系统调用。
*   如果所有其他方法都失败了，那么/dev/urandom 将被用作最后的退路。
*   如果上述来源都不可用，那么将抛出一个`Error`。

## 简单的测试

一个好的随机数生成系统可以保证生成的正确“质量”。为了检查这种质量，通常要进行一系列的统计测试。无需深入复杂的统计主题，将已知行为与数字生成器的结果进行比较有助于质量评估。

一个简单的测试是骰子游戏。假设用一个骰子掷出 6 的几率是六分之一，如果我同时掷出三个骰子 100 次，那么 0、1、2 和 3 个 6 的期望值大致为:

*   0 六= 57.9 倍
*   1 六= 34.7 倍
*   2 个 6 = 6.9 倍
*   3 个六= 0.5 倍

下面是将骰子滚动 1.000.000 次的代码:

```
$times =  1000000; $result =  [];  for  ($i=0; $i<$times; $i++){ $dieRoll = array(6  =>  0);  //initializes just the six counting to zero $dieRoll[roll()]  +=  1;  //first die $dieRoll[roll()]  +=  1;  //second die $dieRoll[roll()]  +=  1;  //third die $result[$dieRoll[6]]  +=  1;  //counts the sixes  }  function roll(){  return random_int(1,6);  } var_dump($result);
```

用 PHP7 `random_int`和简单的`rand`函数测试上面的代码可能会产生:

| 六 | 预期的 | `random_int` | `rand` |
| --- | --: | --: | --: |
| Zero | Five hundred and seventy-nine thousand | Five hundred and seventy-nine thousand four hundred and thirty | Five hundred and seventy-eight thousand one hundred and seventy-nine |
| one | Three hundred and forty-seven thousand | Three hundred and forty-six thousand nine hundred and twenty-seven | Three hundred and forty-seven thousand six hundred and twenty |
| Two | Sixty-nine thousand | Sixty-eight thousand nine hundred and eighty-five | Sixty-nine thousand five hundred and eighty-six |
| three | Five thousand | Four thousand six hundred and fifty-eight | Four thousand six hundred and fifteen |

为了更好地比较`rand`和`random_int`，我们可以用一个公式将结果绘制在一个图表中，以增加值之间的差异:`php result - expected result / sqrt(expected)`。

生成的图形将是:

![test random graph](img/4523ac81161ca7afceb8eeead91fa48f.png "random test result")
(值越接近零越好)

即使三个六的组合表现不佳，并且测试对于实际应用来说过于简单，我们也可以清楚地看到`random_int`比`rand`表现得更好。
此外，由于所采用的随机数生成器中缺乏可预测、可重复的行为，应用程序的安全性得到了提高。

## PHP 5 呢？

默认情况下，PHP 5 不提供任何强伪随机数生成器。现实中有一些选项像 [`openssl_random_pseudo_bytes()`](http://php.net/manual/en/function.openssl-random-pseudo-bytes.php) 、 [`mcrypt_create_iv()`](http://php.net/manual/en/function.mcrypt-create-iv.php) 或者直接用 [`/dev/random`](https://en.wikipedia.org/wiki//dev/random) 或者 [`/dev/urandom`](https://en.wikipedia.org/wiki//dev/random) 设备配`fread()`。还有像 [RandomLib](https://github.com/ircmaxell/RandomLib) 或者[lib 钠](https://pecl.php.net/package/libsodium)这样的包。

如果你想开始使用一个好的随机数生成器，同时准备好 PHP 7，你可以使用 Paragon Initiative Enterprises[`random_compat`](https://github.com/paragonie/random_compat)库。`random_compat`库允许在 PHP 5.x 项目中使用`random_bytes()`和`random_int()`。

该库可以通过 [Composer](https://getcomposer.org/) 安装:

```
composer require paragonie/random_compat
```

```
require  'vendor/autoload.php'; $string = random_bytes(32); var_dump(bin2hex($string));  // string(64) "8757a27ce421b3b9363b7825104f8bc8cf27c4c3036573e5f0d4a91ad2aaec6f" $int = random_int(0,255); var_dump($int);  // int(81)
```

与 PHP 7 相比，`random_compat`库使用了不同的偏好顺序:

1.  `fread()` `/dev/urandom`如果有的话
2.  `mcrypt_create_iv($bytes, MCRYPT_CREATE_IV)`
3.  `COM('CAPICOM.Utilities.1')->GetRandom()`
4.  `openssl_random_pseudo_bytes()`

关于为什么使用这个命令的更多信息，我建议阅读[文档](https://github.com/paragonie/random_compat/blob/master/ERRATA.md)。

使用该库来生成密码的一个简单方法是:

```
$passwordChar =  '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'; $passwordLength =  8; $max = strlen($passwordChar)  -  1; $password =  '';  for  ($i =  0; $i < $passwordLength;  ++$i)  { $password .= $passwordChar[random_int(0, $max)];  } echo $password;  //possible output: 7rgG8GHu
```

## 结论

您应该始终应用加密安全的伪随机数生成器，而`random_compat` lib 为此提供了一个很好的实现。

如果你想使用一个可靠的随机数据源，正如你在文章中看到的，建议是尽快从`random_int`和`random_bytes`开始。

有问题或意见吗？把它们留在下面！

### 进一步阅读

| 描述 | 环 |
| :-- | :-- |
| 死硬测试 | [https://en.wikipedia.org/wiki/Diehard_tests](https://en.wikipedia.org/wiki/Diehard_tests) |
| 带骰子示例的卡方检验 | [http://bit.ly/1Mrptf5](http://bit.ly/1Mrptf5) |
| 科尔莫戈罗夫-斯米尔诺夫试验 | [https://en.wikipedia.org/wiki/Kolmogorov-Smirnov_test](https://en.wikipedia.org/wiki/Kolmogorov-Smirnov_test) |
| 光谱测试 | [http://random.mat.sbg.ac.at/tests/theory/spectral/](http://random.mat.sbg.ac.at/tests/theory/spectral/) |
| gabinete 测试套件 | [http://cristianopi.altervista.org/RaBiGeTe_MT](http://cristianopi.altervista.org/RaBiGeTe_MT) |
| PHP 中的随机数生成(2011) | [http://blog . ircmaxell . com/2011/07/random-number-generation-in-PHP . html](http://blog.ircmaxell.com/2011/07/random-number-generation-in-php.html) |
| 测试 RNG 第 1 部分和第 2 部分 | [http://ubm . io/1ot 46 VL](http://ubm.io/1Ot46vL)[http://ubm . io/1 vnzh 3n](http://ubm.io/1VNzh3N) |

### 承认

非常感谢以下审稿人对本文的帮助！

*   斯科特·阿奇泽夫斯基

## 分享这篇文章