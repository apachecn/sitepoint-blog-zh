# PHP 有一个性别扩展

> 原文：<https://www.sitepoint.com/theres-a-gender-extension-for-php/>

不像我们的“主流”付费课程，我喜欢探索这门语言的怪异和被遗忘的领域。

最近，我冒险进入了 PHP 手册的一个部分，它列出了用于帮助人类语言和字符编码的扩展。我从来没有把它们作为一个整体来看——例如，在处理 [gettext](https://www.sitepoint.com/easy-multi-language-twig-apps-with-gettext/) 时，我总是直接登陆它，忽略其他的。好吧，在其他这些中，有一个引起了我的注意——尤其是在这个充满争议的时代——[性别延伸](http://php.net/manual/en/book.gender.php)。

![Pink and green elephant symbolizing gender roles](img/0f33d79711c2f4370ace4af42398e73f.png)

简而言之，这个扩展试图猜测名字的性别。正如它的介绍所说:

> Gender PHP 扩展是 Joerg Michael 最初编写的`gender.c`程序的一个移植。主要目的是找出名字的性别。目前的数据库包含来自 54 个国家的 40000 个名字。

除了作者被称为*乔治·迈克*之外，这一点很有趣。事实上，这种扩展有许多方面相当令人困惑。

虽然它的[最后一个稳定版本](http://pecl.php.net/package/gender)是在 2015 年，但该扩展使用了名称空间，这清楚地表明它不是某种早已丢失的过去的残余——相对而言，最近才做出努力使它符合现代编码标准。甚至示例代码也使用名称空间:

```
<?php

namespace Gender;

$gender = new Gender;

$name = "Milene";
$country = Gender::FRANCE;

$result = $gender->get($name, $country);
$data = $gender->country($country);

switch($result) {
    case Gender::IS_FEMALE:
        printf("The name %s is female in %s\n", $name, $data['country']);
    break;

    case Gender::IS_MOSTLY_FEMALE:
        printf("The name %s is mostly female in %s\n", $name, $data['country']);
    break;

    case Gender::IS_MALE:
        printf("The name %s is male in %s\n", $name, $data['country']);
    break;

    case Gender::IS_MOSTLY_MALE:
        printf("The name %s is mostly male in %s\n", $name, $data['country']);
    break;

    case Gender::IS_UNISEX_NAME:
        printf("The name %s is unisex in %s\n", $name, $data['country']);
    break;

    case Gender::IS_A_COUPLE:
        printf("The name %s is both male and female in %s\n", $name, $data['country']);
    break;

    case Gender::NAME_NOT_FOUND:
        printf("The name %s was not found for %s\n", $name, $data['country']);
    break;

    case Gender::ERROR_IN_NAME:
        echo "There is an error in the given name!\n";
    break;

    default:
        echo "An error occurred!\n";
    break;

} 
```

既然我们这里有这个代码，让我们来看看它。

这里有一些非常容易混淆的常量名——一个名字怎么会包含错误呢？男女通用和情侣名字有什么区别？深入挖掘，我们看到一些更奇怪的常数。

例如，该类将国家的短名称作为常量(例如，`BRITAIN`)，引用包含国家的国际代码(`UK`)和完整的国家名称(`GREAT BRITAIN`)的数组。

```
$gender = new Gender\Gender;
var_dump($gender->country(Gender\Gender::BRITAIN));

array(2) {
  'country_short' =>
  string(2) "UK"
  'country' =>
  string(13) "Great Britain"
} 
```

只是，`UK`不是这里的国际代码，而是`GB`。为什么他们选择这条路线，而不是依靠现有的地理名称包，甚至只是一个[准确的常数列表](https://countrycode.org/)，这是任何人的猜测。

一旦使用，该类就使用`get`方法返回姓名的性别，前提是我们已经给定了姓名和国家(可选——如果省略，则搜索所有国家)。但是国家必须是类的常量(所以你需要记住它或者在把它添加到 UI 时使用*它们的值*，因为它不会匹配任何*标准*国家代码列表)并且它还*返回一个整数*——类中定义的另一个常量，就像这样:

```
const integer IS_FEMALE = 70 ;
const integer IS_MOSTLY_FEMALE = 102 ;
const integer IS_MALE = 77 ;
const integer IS_MOSTLY_MALE = 109 ;
const integer IS_UNISEX_NAME = 63 ;
const integer IS_A_COUPLE = 67 ;
const integer NAME_NOT_FOUND = 32 ;
const integer ERROR_IN_NAME = 69 ; 
```

这些价值观都是莫名其妙的。

另一种方法`isNick`，检查一个名字是否是另一个名字的昵称或别名。这在鲍勃对罗伯特或迪克对理查德的情况下是有道理的，但它真的能超越这些可预测的英国价值观吗？这个方法让人加倍困惑，因为它说它在签名中返回一个数组，而描述说它是一个布尔值。

![Wrong description of method return type](img/8ae19fe94e585cb6d5a0bdea7013e343.png)

最后，`similarNames`方法将返回一个与提供的名称相似的名称数组，给出名称和国家(如果省略 country，则比较所有国家的名称)。这包括别名吗？相似的依据是什么？尽管是异性，马里奥和玛丽亚相似吗？还是马里奥只是和马雷克差不多？马里奥与马雷克*和*有任何相似之处吗？没有信息。

我必须自己去发现，所以我安装并测试了它。

## 装置

我在一个隔离的环境中通过预装了 PECL 的 Homestead Improved 进行了测试。

```
sudo pecl install gender
echo "extension=gender.so" | sudo tee /etc/php/7.1/mods-available/gender.ini
sudo phpenmod gender
pear run-scripts pecl/gender 
```

最后一个命令将询问字典放在哪里。我认为这是为了扩展它。我选择了`.`，如在“当前文件夹”中。让我们先用上面的示例内容制作一个简单的`index.php`文件并进行测试。

![Milene is female in France](img/c34671d7de3ea83c7ee05b284644045a.png)

果然有效。好了，我们把国家改成`$country = Gender::CROATIA;`。

![Milene is female in Croatia](img/c3aa78b64854cd5c5e28b2ad3f37024e.png)

好吧，当然，这不是一个常见的名字，也不是那种格式，但它最类似于米莲娜，这是一个女性的名字。让我们通过`similar.php`来看看米莲娜有什么相似之处:

```
<?php
namespace Gender;

$gender = new Gender;
$similar = $gender->similarNames("Milena", Gender::CROATIA);

var_dump($similar); 
```

![Milena has no similar names?](img/9fc13b4b9dc7bc71cffb754cb76bfe89.png)

不是我所期待的。让我们看看原件，米兰妮。

![Milene has odd similarities](img/da2c7f3cc01331c54039b38af9b07abf.png)

所以米莲娜被列为和米兰妮相似的名字，但是米兰妮和米莲娜不相似吗？此外，其中两个似乎有一些编码问题？克罗地亚字母表甚至没有字母“y”，我们肯定没有这两个相似的名字，不管问号下面藏着什么。

好吧，我们试试别的。让我们看看鲍勃是否是`alias.php`中罗伯特的别名:

```
<?php
namespace Gender;
$gender = new Gender;
var_dump($gender->isNick('Bob', 'Robert', Gender::USA)); 
```

![Bob is an alias of Robert](img/984cc2524ba90337eb8e92a137f9a675.png)

事实上，这似乎是真的。不过，这是唾手可得的果实。让我们看一个本地的。

```
var_dump($gender->isNick('Tea', 'Dorotea', Gender::CROATIA)); 
```

![Tea is not an alias of Dorotea](img/9427de6564aedeaaa81d73d8d760e0e2.png)

得了吧。

马里奥/玛丽亚/马雷克的问题从一开始呢？让我们依次看看它们的相似之处。

![Mario is similar to himself and a misencoded version of himself](img/8ee6ef892fbc3d6aaed556600d57aa51.png)
![Maria is similar to herself and a misencoded version of herself](img/324e6061c9ec302b620dfd461c97ccf6.png)


不太好。

多试几次。为了使测试更容易，让我们将`index.php`中的`$name`和`$country`行改为:

```
$name = $argv[1];
$country = constant(Gender::class.'::'.strtoupper($argv[2])); 
```

现在，我们可以在不编辑文件的情况下从 CLI 进行测试。

最后几次尝试。我有一个来自突尼斯的女性朋友，叫马内尔。我认为她的名字在世界上大多数地方都是男性的，因为它以辅音结尾。让我们测试一下她的名字和其他名字。

![Cannot find Tunisia](img/93dd4c1cdf07821d30515527b38983df.png)

没有突尼斯？也许手册中没有记录，让我们输出所有定义的常量并进行检查。

```
// constants.php
<?php

$oClass = new ReflectionClass(Gender\Gender::class);
var_dump($oClass->getConstants()); 
```

![Missing countries in list](img/c2ffbd7e5f2e431072ce38e270670f89.png)

不，看起来那些医生是对的。在这一点上，我停止使用这个工具。

* * *

这是一个简单的类，并且绝对不需要*作为扩展，这使得整个情况变得更加有趣。没有人会经常调用它来关心一个扩展相对于一个包的性能提升，一个包可以由非 sudo 用户安装，人们可以更容易地为它做出贡献。*

这个扩展既不准确也不完整，可能是一个简单的类，它是如何出现在 PHP 手册中的还不清楚，但它表明在我们提高 PHP 的声誉之前，在 PHP 核心(我把手册作为“核心”)中还有许多清理工作要做。在这 9 年里(9！)自从这个端口的开发开始以来，甚至不是所有的国家都被添加到内部列表中，但是有人决定这个扩展应该在手册中。

你有更多关于这个扩展的信息吗？你明白这有什么意义吗？你在手册或 PHP 中还发现了哪些奇怪的扩展或内置特性？

## 分享这篇文章