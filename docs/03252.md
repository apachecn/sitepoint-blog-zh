# 用作曲家建议碳-日期和时间的正确方式

> 原文：<https://www.sitepoint.com/suggesting-carbon-with-composer-date-and-time-the-right-way/>

Carbon 是一个 PHP 中用于日期和时间操作的小库。它依赖并扩展了核心的 [DateTime](http://php.net/manual/en/class.datetime.php) 类，增加了有用的方法来获得更加清晰的体验。

在本文中，我们将看看一些基本的使用示例，然后在一个真实的项目中使用它。

![Date and time image](img/9cbdf37e78a818498d87fd8a8a6f5d6c.png)

## 介绍

Carbon 只是一个被设计用于日期时间的*而不是*的类。由于扩展了 DateTime，Carbon 的用户可以使用所有的 DateTime 方法。此外，它实现了一个`__toString`方法，允许用户用它代替日期和时间的字符串表示。

它可以很容易地与 Composer 一起安装:

```
composer require nesbot/carbon 
```

让我们来看一些例子，如他们的[优秀文档](http://carbon.nesbot.com/docs)中所展示的。

## 示例用途

开始使用 Carbon 最简单的方法是将一个人类可读的日期字符串和一个可选的时区一起传递给它的构造函数——如果省略了时区，将使用当前 PHP 安装设置的时区。

```
$carbon = new Carbon('first day of next week'); 
```

它也可以从字符串、时间戳、甚至日期时间的其他实例甚至 Carbon 实例化。为了高效克隆，可以使用`copy()`方法复制实例。

从那里，我们可以访问助手检查器和获取器:

```
$carbon->isWeekend();
$carbon->isFuture();
$carbon->isLeapYear();

$carbon->year;
$carbon->month;

$carbon->daysInMonth;
$carbon->weekOfYear; 
```

该包还公开了快速创建新实例的静态方法:

```
echo Carbon::now()->addYear()->diffForHumans();    // in 1 year 
```

甚至可以检查生日，我们可以从文档中的这个例子中看到:

```
$born = Carbon::createFromDate(1987, 4, 23);
$noCake = Carbon::createFromDate(2014, 9, 26);
$yesCake = Carbon::createFromDate(2014, 4, 23);
$overTheHill = Carbon::now()->subYears(50);
var_dump($born->isBirthday($noCake));              // bool(false)
var_dump($born->isBirthday($yesCake));             // bool(true)
var_dump($overTheHill->isBirthday());              // bool(true) -> default compare it to today! 
```

### 本地化

还支持本地化，因此输出可以是安装在驱动 PHP 应用程序的机器上的任何语言。请注意，您**的确**需要安装必要的语言环境来实现这一点——请参考您的操作系统文档，了解如何操作的详细信息。

要本地化日期和时间字符串，可以使用标准的 PHP 函数`setlocale`:

```
setlocale(LC_TIME, 'German');
echo $dt->formatLocalized('%A %d %B %Y');          // Mittwoch 21 Mai 1975 
```

为了本地化输出人类可读时间差的`diffForHumans`方法，该类提供了自己的`setLocale`方法:

```
Carbon::setLocale('de');
echo Carbon::now()->addYear()->diffForHumans();    // in 1 Jahr 
```

### 间隔

还提供了一个 CarbonInterval 类，它是对 [DateInterval](http://php.net/manual/en/class.dateinterval.php) 的扩展。自我描述，它保存区间值，就像基类一样，但是在上面添加了 helper 方法。根据示例:

```
echo CarbonInterval::year();                           // 1 year
echo CarbonInterval::months(3);                        // 3 months
echo CarbonInterval::days(3)->seconds(32);             // 3 days 32 seconds
echo CarbonInterval::weeks(3);                         // 3 weeks
echo CarbonInterval::days(23);                         // 3 weeks 2 days
echo CarbonInterval::create(2, 0, 5, 1, 1, 2, 7);      // 2 years 5 weeks 1 day 1 hour 2 minutes 7 seconds 
```

请注意，碳作为一个整体得到了非常好的记录——有关方法和使用示例的完整参考，请参见[他们的文档](http://carbon.nesbot.com/docs)。

## 履行

在这一节中，我们将升级 [Diffbot PHP 客户端](https://github.com/Swader/diffbot-php-client)来可选地支持 Carbon。计划如下:如果用户安装了库，那么[文章实体](http://diffbot-php-client-docs.readthedocs.org/en/latest/api-article.html#article-entity-class)和[文章实体](http://diffbot-php-client-docs.readthedocs.org/en/latest/api-discussion.html#discussion-post-class)将从它们的`getDate`和`getEstimatedDate`方法返回 Carbon 实例，而不是日期字符串。否则，它们会像往常一样返回字符串。

如果您想继续，请在[克隆这个版本](https://github.com/Swader/diffbot-php-client/releases/tag/1.1)的客户端。

### 作曲家建议

第一步是将库添加到`composer.json`中的`suggests`列表。`suggests`列表采用与`require`块相同的格式，但是我们没有版本约束，而是给出了完整的字符串消息，说明为什么建议使用这个包。

```
"suggest": {
    "nesbot/carbon": "Turns the date and estimatedDate return values of Article and Post entity into Carbon entities."
  }, 
```

我们可以通过运行`composer validate`来确保我们得到了正确的语法:

```
vagrant@homestead:~/Code/diffbot-php-client$ composer validate
./composer.json is valid 
```

当用户安装 Diffbot PHP 客户端时，他们会看到安装 Carbon 的建议。

### 试验

接下来，是时候更新测试来适应这种情况了。

在`tests/Entities/ArticleTest.php`中，我们像这样修改`dateProvider`和`testDate`函数:

```
 public function dateProvider()
    {
        return [
            [
                'Articles/diffbot-sitepoint-basic.json',
                "Sun, 27 Jul 2014 00:00:00 GMT",
                2014
            ],
            [
                'Articles/diffbot-sitepoint-extended.json',
                "Sun, 27 Jul 2014 00:00:00 GMT",
                2014
            ],
            [
                'Articles/apple-watch-verge-basic.json',
                "Wed, 08 Apr 2015 00:00:00 GMT",
                2015
            ],
            [
                'Articles/apple-watch-verge-extended.json',
                "Wed, 08 Apr 2015 00:00:00 GMT",
                2015
            ]
        ];
    }

    /**
     * @param $file
     * @param $articles
     * @dataProvider dateProvider
     */
    public function testDate($file, $articles, $year)
    {
        $articles = (is_array($articles)) ? $articles : [$articles];
        /** @var Article $entity */
        foreach ($this->ei($file) as $i => $entity) {
            $this->assertEquals($articles[$i], $entity->getDate());
            if (class_exists('\Carbon\Carbon')) {
                $this->assertEquals($year, $entity->getDate()->year);
            }
        }
    } 
```

这里发生了什么？

首先，我们将年份值添加到提供程序中，作为第三个参数传递给`testDate`。然后，在迭代和断言期间，我们首先检查该类是否被加载/存在，如果是，我们测试它的一个 getter(`->year`)。

我们必须在测试之前检查该类是否存在，因为 Carbon 在 Diffbot SDK 中是可选的——这只是一个建议，所以如果它不存在，我们不能失败。

我们对`ArticleTest`类底部的`estimatedDateProvider`和`estimatedDate`重复这个过程:

```
 public function estimatedDateProvider()
    {
        return [
            ['Articles/15-11-07/diffbot-sitepoint-basic.json', 'Sun, 27 Jul 2014 00:00:00 GMT', 2014],
        ];
    }

    /**
     * @dataProvider estimatedDateProvider
     * @param $file
     * @param $value1
     */
    public function testEstimatedDate($file, $value1, $value2)
    {
        $value1 = (is_array($value1)) ? $value1 : [$value1];
        /** @var Article $entity */
        foreach ($this->ei($file) as $i => $entity) {
            $this->assertEquals($value1[$i], $entity->getEstimatedDate());
            if (class_exists('\Carbon\Carbon')) {
                $this->assertEquals($value2, $entity->getDate()->year);
            }
        }
    } 
```

接下来，我们来更新一下`PostTest`类。那个只有`getDate`方法，但是涉及到更多的输入，因为一个[讨论](http://diffbot-php-client-docs.readthedocs.org/en/latest/api-discussion.html)几乎总是返回多个帖子。

```
 {
        return [
            [
                'Discussions/15-05-01/sp_discourse_php7_recap.json',
                [
                    "Wed, 29 Apr 2015 16:00:00 GMT",
                    "Thu, 30 Apr 2015 01:13:00 GMT",
                    "Thu, 30 Apr 2015 05:55:00 GMT",
                    "Thu, 30 Apr 2015 06:57:00 GMT",
                    "Thu, 30 Apr 2015 07:51:00 GMT",
                    "Thu, 30 Apr 2015 09:29:00 GMT",
                    "Thu, 30 Apr 2015 10:26:00 GMT",
                    "Thu, 30 Apr 2015 10:40:00 GMT",
                    "Thu, 30 Apr 2015 11:06:00 GMT",
                    "Thu, 30 Apr 2015 11:29:00 GMT",
                    "Thu, 30 Apr 2015 14:33:00 GMT",
                    "Thu, 30 Apr 2015 15:48:00 GMT",
                    "Thu, 30 Apr 2015 16:17:00 GMT",
                    "Thu, 30 Apr 2015 16:51:00 GMT",
                    "Thu, 30 Apr 2015 17:02:00 GMT",
                    "Fri, 01 May 2015 08:00:00 GMT",
                ],
                [
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                    2015,
                ]
            ]
        ];
    }

    /**
     * @param $file
     * @param $posts
     * @dataProvider dateProvider
     */
    public function testDate($file, $posts, $years)
    {
        /** @var Discussion $entity */
        foreach ($this->ei($file) as $entity) {
            /** @var Post $post */
            foreach ($entity->getPosts() as $i => $post) {
                $this->assertEquals($posts[$i], $post->getDate());
                if (class_exists('\Carbon\Carbon')) {
                    $this->assertEquals($years[$i], $post->getDate()->year);
                }

            }
        }
    } 
```

如果我们现在尝试运行这些测试，它们将会成功。如果我们在项目中加入碳元素:

```
composer require nesbot/carbon --dev 
```

…测试将会失败。

### 履行

现在是时候改变实际的实体了。

Diffbot 以下列格式返回日期:`Sun, 27 Jul 2014 00:00:00 GMT`。

为了保持向后兼容性，我们需要将 Carbon 设置为在作为字符串使用时产生相同的输出(通过`__toString`)，这样每个直接使用日期值作为输出的人仍然可以这样做。这是通过静态的`Carbon::setToStringFormat($format);`方法完成的。

将以下内容添加到`src/Entity/Article.php`中的构造函数将会实现这一点:

```
if (class_exists('\Carbon\Carbon')) {
    $format = 'D, d M o H:i:s e';
    \Carbon\Carbon::setToStringFormat($format);
} 
```

必须将相同的内容添加到`src/Entity/Post.php`文件中，尽管该文件仍然没有覆盖基类的构造函数，并且需要首先这样做。`Post`的构造方法的最终版本是:

```
public function __construct(array $data)
{
    if (class_exists('\Carbon\Carbon')) {
        $format = 'D, d M o H:i:s e';
        \Carbon\Carbon::setToStringFormat($format);
    }

    parent::__construct($data);
} 
```

现在我们已经有了活性炭，并默认为我们想要的格式，是时候升级我们的吸气剂了。

在`Article`和`Post`中，`getDate`方法现在应该是这样的:

```
public function getDate()
{
    return (class_exists('\Carbon\Carbon')) ?
        new \Carbon\Carbon($this->data['date'], 'GMT') :
        $this->data['date'];
} 
```

如果 Carbon 存在，则根据文章的日期在 GMT 时区创建一个新实例(时区 Diffbot 总是返回)，否则，像以前一样返回日期字符串。

最后，我们需要更改`Article`中的`getEstimatedDate`:

```
public function getEstimatedDate()
{
    $date = $this->getOrDefault('estimatedDate', $this->getDate());

    return (class_exists('\Carbon\Carbon')) ?
        new \Carbon\Carbon($date, 'GMT') :
        $date;
} 
```

同样的，如果`estimatedDate`不能确定，只有这个先默认为`getDate`。

运行测试现在应该显示一切都通过了:

```
vagrant@homestead:~/Code/diffbot-php-client$ phpunit
PHPUnit 5.0.8 by Sebastian Bergmann and contributors.

Runtime:       PHP 5.6.10-1+deb.sury.org~trusty+1 with Xdebug 2.3.2
Configuration: /home/vagrant/Code/diffbot-php-client/phpunit.xml.dist

...............................................................  63 / 352 ( 17%)
............................................................... 126 / 352 ( 35%)
............S.................................................. 189 / 352 ( 53%)
............................................................... 252 / 352 ( 71%)
............................................................... 315 / 352 ( 89%)
.....................................                           352 / 352 (100%)

Time: 49.39 seconds, Memory: 21.00Mb 
```

成功！我们现在可以提交、推送和发布新版本了！

## 结论

我们研究了 Carbon，它是 DateTime 的一个扩展，为核心类添加了有用的方法，使用起来更加舒适。我们看到了在项目中实现它是多么容易，以及它如何通过不同的内部字符串格式来取代纯字符串输出和时间戳。

你在你的项目中使用碳吗？你喜欢或不喜欢它的什么？请在下面留下你的想法和评论，如果你喜欢这篇文章，别忘了点击那个大拇指按钮！

## 分享这篇文章