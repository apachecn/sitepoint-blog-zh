# Sourcehunt 九月 Hacktoberfest 版

> 原文：<https://www.sitepoint.com/sourcehunt-september-hacktoberfest-edition/>

又到了一年中的这个时候——数字海洋的黑客啤酒节开始了！

这是一个为期一个月的开源努力，鼓励人们为各种开源项目做出贡献(不是他们自己的！).任何打开 4 个拉请求的人(甚至文档修复也算！)在月底得到一件 t 恤，象征着他们在开源社区的参与。

当然，开源不仅仅是一个月的时间，但是对于那些通常不会花时间为其他开发者的项目做贡献的人来说，这不仅仅是一个好的开始。

如果你想参与，为什么不从我们这个月收集的列表中挑选一些项目呢？如果这些不能引起你的兴趣，为什么不访问 [sourcehunt PHP 标签](https://www.sitepoint.com/php/)看看你是否能找到更有趣的东西呢？

让我们开始吧！

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

* * *

## [kodus/mail](https://github.com/kodus/mail) [3 ★]

*就在*我们发布[用 PHP 对抗招聘者垃圾邮件](https://www.sitepoint.com/fighting-recruiter-spam-with-php-proof-of-concept/)的时候，使用 Swiftmailer 发送回复，Kodus 被提交到 Sourcehunt。

Kodus/mail 是一个全新的 alpha 级包，旨在使发送 UTF-8 电子邮件更加简单，同时跳过 Swiftmailer 带来的所有遗留问题(旧的自动加载、命名约定等)。).正如作者所说，目标是*“为了简单起见，从现代 PHP 和有限范围的特性开始。”*

这是你参与一个非常有前途的项目的机会！

* * *

## [voku/Arrayy](https://github.com/voku/Arrayy) [20 ★]

Arrayy 是一个非常强大和通用的面向对象的数组操作接口。正如 README 简介中所说:

```
Arrayy::create(['Array', 'Array'])->unique()->append('y')->implode() // Arrayy 
```

这只是 Arrayy 提供的无数可能的操作链中的一个。其他用法包括:

##### createFromString(string $ str):Arrayy(不可变)

通过字符串创建一个新的 Arrayy 对象。

```
$arrayy = A::createFromString(' foo, bar '); // Arrayy['foo', 'bar'] 
```

##### append(混合$value) : Arrayy(可变)

向当前数组追加一个值。

别名:" Arrayy->add()"

```
a(['fòô' => 'bàř'])->append('foo'); // Arrayy['fòô' => 'bàř', 0 => 'foo'] 
```

##### contains 不区分大小写(string $value) : boolean

检查当前数组中是否有(不区分大小写的)字符串。

```
a(['E', 'é'])->containsCaseInsensitive('É'); // true 
```

##### each(Closure $closure) : Arrayy(不可变)

迭代当前数组并修改数组的值。

```
$result = A::create();
$closure = function ($value) {
  return ':' . $value . ':';
};
a(['foo', 'bar' => 'bis'])->each($closure); // Arrayy[':foo:', 'bar' => ':bis:'] 
```

##### find(Closure $closure):混合

找到数组中通过真值测试的第一项，否则返回 false。

```
$search = 'foo';
$closure = function ($value, $key) use ($search) {
  return $value === $search;
};
a(['foo', 'bar', 'lall'])->find($closure); // 'foo' 
```

##### randomWeighted(array $array，int|null $take) : Arrayy(不可变)

从数组中获取一个随机值，能够扭曲结果。

```
a([0 => 3, 1 => 4])->randomWeighted([1 => 4]); // e.g.: Arrayy[4] (has a 66% chance of returning 4) 
```

##### search value(mixed $ index):Arrayy(不可变)

通过$index 搜索当前数组的值。

```
a(['fòô' => 'bàř'])->searchValue('fòô'); // Arrayy[0 => 'bàř'] 
```

##### toJson() : string

将当前数组转换为 JSON。

```
a(['bar', array('foo')])->toJson(); // '["bar",{"1":"foo"}]' 
```

…还有更多。该项目已经推出了一段时间，但是尽管已经过充分的测试和良好的文档记录，仍然没有得到广泛的采用，所以让我们希望 Sourcehunt 能够有所帮助！

* * *

## [voku/portable-utf8](https://github.com/voku/portable-utf8)【76】

portable-utf8 由制作上述 Arrayy 包的同一个人制作，它是 PHP 中 UTF-8 的一个实现，没有任何扩展先决条件。换句话说，无论你的服务器是否安装了 UTF-8 友好扩展，UTF-8 都将在你的服务器上启用。如果缺少扩展，将使用 Symfony 的 polyfills。

我将让“为什么”部分告诉您为什么会存在这种情况:

> PHP 5 和更早的版本没有本地 Unicode 支持。为了弥合这一差距，有几种扩展，如“mbstring”、“iconv”和“intl”。
> 
> “mbstring”和其他的问题是，大多数时候你不能确保在服务器上存在一个特定的。如果您依赖其中的一个，您的应用程序就不再具有可移植性。对于必须在具有不同配置的不同服务器上运行的开源应用程序来说，这个问题变得更加严重。

重要的是要注意，如果你已经在你的应用中使用了一些 UTF 8 特定的功能，转换到这个包就不那么简单了，因为它不是一个现成的替代品。由于这个包的性质，同样值得记住的是，它将成为一个硬依赖。尽管如此，考虑到收益，这似乎是值得的。

这个包没有未解决的问题或拉请求，也没有定义 TODOs，但是它的测试覆盖率和审查者分数可能需要一些工作，所以如果你想通过优化测试级别和感知代码质量来做出贡献，现在是你的机会了。

* * *

## PHP-integrator/atom-base[168★]

PHP-integrator 是一个 Atom 包，它有助于代码林挺——即从 PhpStorm 到 Atom 添加了许多有用的代码完成和代码提示功能。

它目前依赖于 Atom，因为它是一个 Atom 包，但是它的 PHP 代码可以很容易地提取并应用到另一个带有钩子的编辑器中，这使得集成成为可能。

它可以在 Atom 端使用更多的特性，但也可以使用一两个分支将它变成一个编辑器无关的包，可以很容易地应用于任何其他 ide。另一个想法——为什么不让 Docker 开箱即用，帮助人们避免在他们的主机操作系统上安装 PHP？潜进去！

* * *

## [弗洛里安夫/交换器](https://github.com/florianv/exchanger)【7★】

这个包是一个“货币兑换框架”，意思是它被用来开发*货币兑换包*。把它看作是帮助你开发自己的可互换货币交换包的标准。

这个“框架”的一个有趣的实现是同一作者的流行的 [Swap](https://github.com/florianv/swap) 包。

虽然两个软件包都很成熟，但 Exchanger 还需要更多的爱。

顺便说一句，如果你有兴趣写关于 Exchanger 的文章并展示该框架的威力，请联系我们——我们正在寻找！

* * *

## [rinvex/country](https://github.com/rinvex/country) [383 ★]

正如描述所说:

> Rinvex Country 是一个简单的轻量级软件包，用于灵活地检索国家详细信息。一大堆数据，包括全球所有 250 个国家的名称、假名、资本、iso 代码、拨号代码、地理数据、货币、旗帜、表情符号和其他属性，触手可及

示例:

```
use Rinvex\Country\Models\Country;

// Find a country by it's ISO 3166-1 alpha-2
$egypt = (new Country)->find('EG');

// Find a country by one of it's attributes
$usa = (new Country)->findBy('capital', 'Washington D.C.');

// Find all countries
$countries = (new Country)->findAll();

// Retrieve only `name`, `demonym`, and `currency` attributes of "Japan":
$japan = (new Country)->find('JP', ['name', 'demonym', 'currency']);

// Utilize Laravel Collections to get an array of all country names, with their 'iso_3166_1_alpha2' as the array keys
$allCountries = (new Country)->findAll()->pluck('name.common', 'iso_3166_1_alpha2'); 
```

作为一个目前正在开发两个独立地理感知应用的人，我肯定会喜欢这个包——它让通用国家信息变得更加容易。

由于其多用途的文档和优秀的代码质量分数，该库已经获得了一些不错的采用，但仍然可以使用更多的用户和更多的贡献者。你能帮忙吗？还有一些[问题](https://github.com/rinvex/country/issues)你现在就可以解决！

* * *

## [脚本融合/映射器](https://github.com/ScriptFUSION/Mapper) [7 ★]

老实说，我不知道这是什么，也不知道我会用它做什么，但 Ocramius 称赞我，所以我把它列入了清单。

如果你觉得你可以解释这是什么总新手，请与我们联系，我们会支付你的职位。

* * *

## [luff ICC/laravel-blog](https://github.com/lufficc/laravel-blog)[164★]

一个 Laravel 5.3 博客系统！从反馈来看，它相当不错，但肯定可以使用更多的功能。为什么不插话帮忙呢？

如果有，请告诉我们——我们希望得到全面的分析！

* * *

## [推论/PSR 6-型材缓存池/](https://github.com/Corollarium/PSR6-ProfileCachePool/)【9】

你使用 PSR 6 缓存吗？如果是这样，您可能会对这个分析器感兴趣，它监视您的缓存实现获得了多少命中和未命中。除非您注意到一些差异并想要调试，否则它并不真正有用，但是如果您需要它，它就在这里！

为什么不通过添加更多的报告格式(动画图)来为软件包做出贡献呢？)或者其他统计数据来跟踪？

* * *

九月份到此为止——一如既往，请将您的链接和 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 标签一起发给我们！现在去黑掉 Hacktober 吧！让我们知道你做了什么和快乐编码！

## 分享这篇文章