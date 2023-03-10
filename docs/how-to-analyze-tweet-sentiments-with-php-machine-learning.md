# 如何用 PHP 机器学习分析推文情绪

> 原文：<https://www.sitepoint.com/how-to-analyze-tweet-sentiments-with-php-machine-learning/>

*这篇文章由[沃恩·安切塔](https://www.sitepoint.com/author/wancheta)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

最近，似乎每个人和他们众所周知的祖母都在谈论机器学习。你的社交媒体 feeds 被关于 ML、Python、TensorFlow、Spark、Scala、Go 等等的帖子淹没；如果你和我一样，你可能会想，PHP 怎么样？

是的，机器学习和 PHP 呢？幸运的是，有人足够疯狂，不仅提出了这个问题，而且还开发了一个通用的机器学习库，我们可以在下一个项目中使用。在这篇文章中，我们将看一看[PHP-ML](https://github.com/php-ai/php-ml)——PHP 的机器学习库——我们将编写一个情感分析类，以后我们可以在自己的聊天或推特机器人中重用它。该职位的主要目标是:

*   探索机器学习和情感分析的一般概念
*   回顾 PHP-ML 的能力和缺点
*   定义我们将要解决的问题
*   证明尝试用 PHP 进行机器学习并不是一个完全疯狂的目标(可选)

![A robot elephpant](img/4a28fb874fd0d92e4e1441e5bc1c3dad.png)

## 什么是机器学习？

机器学习是人工智能的一个子集，专注于赋予“计算机无需显式编程就能学习的能力”。这是通过使用可以从特定数据集“学习”的通用算法来实现的。

例如，机器学习的一个常见用法是分类。分类算法用于将数据归入不同的组或类别。分类应用的一些例子是:

*   垃圾邮件过滤器
*   市场分割
*   欺诈检测

机器学习是一个涵盖不同任务的许多通用算法的总称，根据它们如何学习，有两种主要的算法类型——监督学习和非监督学习。

### 监督学习

在监督学习中，我们使用输入对象(向量)和期望输出值形式的标记数据来训练我们的算法；该算法分析训练数据并产生所谓的推断函数，我们可以将该函数应用于新的、未标记的数据集。

在这篇文章的剩余部分，我们将关注监督学习，因为它更容易看到和验证这种关系；请记住，这两种算法同样重要和有趣；有人可能会说，无监督的更有用，因为它排除了标记数据的要求。

### 无监督学习

另一方面，这种类型的学习从一开始就处理未标记的数据。我们不知道数据集的期望输出值，我们让算法从数据集进行推断；当进行探索性数据分析以发现数据中隐藏的模式时，无监督学习尤其方便。

## PHP-ML

见见 PHP-ML，一个声称是 PHP 中机器学习的新方法的库。该库实现算法、神经网络和工具来进行数据预处理、交叉验证和特征提取。

我将是第一个承认 PHP 对于机器学习来说是一个不寻常的选择，因为这种语言的优势不太适合机器学习应用。也就是说，并不是每个机器学习应用程序都需要处理数 Pb 的数据和进行大量计算——对于简单的应用程序，我们应该能够使用 PHP 和 PHP-ML。

我现在能看到的这个库的最佳用例是分类器的实现，可以是垃圾邮件过滤器，甚至是情感分析。我们将定义一个分类问题，并逐步构建一个解决方案，看看如何在我们的项目中使用 PHP-ML。

## 问题是

为了举例说明实现 PHP-ML 和向我们的应用程序添加一些机器学习的过程，我想找到一个有趣的问题来解决，以及比构建一个 tweet 情感分析类更好的展示分类器的方法。

构建成功的机器学习项目所需的关键要求之一是一个像样的起始数据集。数据集是至关重要的，因为它们将允许我们根据已经分类的例子来训练我们的分类器。由于最近媒体上关于航空公司的声音很大，有什么数据集比顾客给航空公司的推文更好呢？

幸运的是，多亏了 [Kaggle.io](https://kaggle.io) ，我们已经可以获得一组推文。使用[这个链接](https://www.kaggle.com/crowdflower/twitter-airline-sentiment)，可以从他们的网站上下载 Twitter 美国航空公司情绪数据库

## 解决方案

让我们先来看看我们将要处理的数据集。原始数据集包含以下各列:

*   推特 _id
*   航空公司 _ 情绪
*   航空公司 _ 情绪 _ 信心
*   消极原因
*   消极原因 _ 自信
*   航空公司
*   航空公司 _ 情绪 _ 黄金
*   名字
*   消极原因 _ 黄金
*   转发次数
*   文本
*   推特 _ 坐标
*   推特 _ 已创建
*   推特 _ 位置
*   用户时区

如下例所示(侧边可滚动表格):

| 推特 _id | 航空公司 _ 情绪 | 航空公司 _ 情绪 _ 信心 | 消极原因 | 消极原因 _ 自信 | 航空公司 | 航空公司 _ 情绪 _ 黄金 | 名字 | 消极原因 _ 黄金 | 转发次数 | 文本 | 推特 _ 坐标 | 推特 _ 已创建 | 推特 _ 位置 | 用户时区 |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 570306133677760513 | 中立的 | One |  |  | 维珍美国 |  | 卡登 |  | Zero | @ virgin America @ dhepburn 说的话。 |  | 2015-02-24 11:35:52 -0800 |  | 东部时间(美国和加拿大) |  |
| 570301130888122368 | 积极的 | 0.3486 |  | Zero | 维珍美国 |  | jnardino |  | Zero | @VirginAmerica 加上你在体验中加入了商业广告……俗气。 |  | 2015-02-24 11:15:59 -0800 |  | 太平洋时间(美国和加拿大) |  |
| 570301083672813571 | 中立的 | 0.6837 |  |  | 维珍美国 |  | 伊冯纳林 |  | Zero | @VirginAmerica 我今天没有…一定是说我需要再走一趟！ |  | 2015-02-24 11:15:48 -0800 | 让我们玩吧 | 中部时间(美国和加拿大) |  |
| 570301031407624196 | 否定的；消极的；负面的；负的 | One | 糟糕的飞行 | 0.7033 | 维珍美国 |  | jnardino |  | Zero | @VirginAmerica 当着客人的面大吹特吹令人讨厌的“娱乐节目”真是咄咄逼人&他们几乎没有办法 |  | 2015-02-24 11:15:36 -0800 |  | 太平洋时间(美国和加拿大) |  |
| 570300817074462722 | 否定的；消极的；负面的；负的 | One | 看不出来 | One | 维珍美国 |  | jnardino |  | Zero | @VirginAmerica 这真的是一件非常糟糕的事情 |  | 2015-02-24 11:14:45 -0800 |  | 太平洋时间(美国和加拿大) |  |
| 570300767074181121 | 否定的；消极的；负面的；负的 | One | 看不出来 | 0.6842 | 维珍美国 |  | jnardino |  | Zero | “@VirginAmerica 真的会花 30 美元买一个没有播放这个的座位。 |  |  |  |  |  |
| 这真的是乘坐 VA 唯一不好的地方” |  | 2015-02-24 11:14:33 -0800 |  | 太平洋时间(美国和加拿大) |  |  |  |  |  |  |  |  |  |  |  |
| 570300616901320704 | 积极的 | 0.6745 |  | Zero | 维珍美国 |  | cjmcginnis |  | Zero | “@VirginAmerica 是的 | 几乎每次我坐飞机去 VX，这只“耳朵虫”都不会消失:)” |  | 2015-02-24 11:13:57 -0800 | 加州旧金山 | 太平洋时间(美国和加拿大) |
| 570300248553349120 | 中立的 | Zero point six three four |  |  | 维珍美国 |  | 飞行员 |  | Zero | “@VirginAmerica 真的错过了一个无帽男恶搞的黄金机会 | 那里。https://t.co/mWpG7grEZP" |  | 2015-02-24 11:12:29 -0800 | 洛杉矶 | 太平洋时间(美国和加拿大) |

该文件包含 14，640 条推文，因此这是一个不错的数据集，可供我们使用。现在，根据我们现有的列数，我们拥有的数据比我们的例子所需要的要多得多；出于实用目的，我们只关心以下各列:

*   文本
*   航空公司 _ 情绪

其中`text`将成为我们的特色，`airline_sentiment`将成为我们的目标。其余的列可以丢弃，因为我们的练习不会用到它们。让我们从创建项目开始，并使用以下文件初始化 composer:

```
{
    "name": "amacgregor/phpml-exercise",
    "description": "Example implementation of a Tweet sentiment analysis with PHP-ML",
    "type": "project",
    "require": {
        "php-ai/php-ml": "^0.4.1"
    },
    "license": "Apache License 2.0",
    "authors": [
        {
            "name": "Allan MacGregor",
            "email": "amacgregor@allanmacgregor.com"
        }
    ],
    "autoload": {
        "psr-4": {"PhpmlExercise\\": "src/"}
    },
    "minimum-stability": "dev"
} 
```

```
composer install 
```

如果你需要作曲家的介绍，请看这里的。

为了确保我们设置正确，让我们创建一个快速脚本来加载我们的`Tweets.csv`数据文件，并确保它包含我们需要的数据。将下面的代码作为`reviewDataset.php`复制到我们项目的根目录中:

```
<?php
namespace PhpmlExercise;

require __DIR__ . '/vendor/autoload.php';

use Phpml\Dataset\CsvDataset;

$dataset = new CsvDataset('datasets/raw/Tweets.csv',1);

foreach ($dataset->getSamples() as $sample) {
    print_r($sample);
} 
```

现在，用`php reviewDataset.php`运行脚本，让我们来看看输出:

```
Array( [0] => 569587371693355008 )
Array( [0] => 569587242672398336 )
Array( [0] => 569587188687634433 )
Array( [0] => 569587140490866689 ) 
```

现在看起来没什么用了吧？让我们看一下`CsvDataset`类，以便更好地了解内部发生了什么:

```
<?php 

    public function __construct(string $filepath, int $features, bool $headingRow = true)
    {
        if (!file_exists($filepath)) {
            throw FileException::missingFile(basename($filepath));
        }

        if (false === $handle = fopen($filepath, 'rb')) {
            throw FileException::cantOpenFile(basename($filepath));
        }

        if ($headingRow) {
            $data = fgetcsv($handle, 1000, ',');
            $this->columnNames = array_slice($data, 0, $features);
        } else {
            $this->columnNames = range(0, $features - 1);
        }

        while (($data = fgetcsv($handle, 1000, ',')) !== false) {
            $this->samples[] = array_slice($data, 0, $features);
            $this->targets[] = $data[$features];
        }
        fclose($handle);
    } 
```

`CsvDataset`构造函数有 3 个参数:

*   源 CSV 的文件路径
*   一个整数，指定我们的文件中的特征数量
*   指示第一行是否为标题的布尔值

如果我们仔细观察，我们可以看到该类将 CSV 文件映射到两个内部数组中:samples 和 targets。*样本*包含文件提供的所有特征，*目标*包含已知值(负值、正值或中间值)。

基于以上内容，我们可以看到我们的 CSV 文件需要遵循的格式如下:

```
| feature_1 | feature_2 | feature_n | target | 
```

我们需要生成一个干净的数据集，只包含我们继续工作所需的列。让我们称这个脚本为`generateCleanDataset.php`:

```
<?php
namespace PhpmlExercise;

require __DIR__ . '/vendor/autoload.php';

use Phpml\Exception\FileException;

$sourceFilepath         = __DIR__ . '/datasets/raw/Tweets.csv';
$destinationFilepath    = __DIR__ . '/datasets/clean_tweets.csv';

$rows =[];

$rows = getRows($sourceFilepath, $rows);
writeRows($destinationFilepath, $rows);

/**
 * @param $filepath
 * @param $rows
 * @return array
 */
function getRows($filepath, $rows)
{
    $handle = checkFilePermissions($filepath);

    while (($data = fgetcsv($handle, 1000, ',')) !== false) {
        $rows[] = [$data[10], $data[1]];
    }
    fclose($handle);
    return $rows;
}

/**
 * @param $filepath
 * @param string $mode
 * @return bool|resource
 * @throws FileException
 */
function checkFilePermissions($filepath, $mode = 'rb')
{
    if (!file_exists($filepath)) {
        throw FileException::missingFile(basename($filepath));
    }

    if (false === $handle = fopen($filepath, $mode)) {
        throw FileException::cantOpenFile(basename($filepath));
    }
    return $handle;
}

/**
 * @param $filepath
 * @param $rows
 * @internal param $list
 */
function writeRows($filepath, $rows)
{
    $handle = checkFilePermissions($filepath, 'wb');

    foreach ($rows as $row) {
        fputcsv($handle, $row);
    }

    fclose($handle);
} 
```

没有什么太复杂的，只是足够做这项工作。我们用`phpgenerateCleanDataset.php`来执行吧。

现在，让我们继续将我们的`reviewDataset.php`脚本指向干净的数据集:

```
Array
(
    [0] => @AmericanAir That will be the third time I have been called by 800-433-7300 an hung on before anyone speaks. What do I do now???
)
Array
(
    [0] => @AmericanAir How clueless is AA. Been waiting to hear for 2.5 weeks about a refund from a Cancelled Flightled flight &amp; been on hold now for 1hr 49min
) 
```

嘭！这是我们可以利用的数据！到目前为止，我们一直在创建简单的脚本来操作数据。接下来，我们将开始在`src/classification/SentimentAnalysis.php`下创建一个新类。

```
<?php
namespace PhpmlExercise\Classification;

/**
 * Class SentimentAnalysis
 * @package PhpmlExercise\Classification
 */
class SentimentAnalysis { 
    public function train() {}
    public function predict() {}
} 
```

我们的情感分析类需要两个函数:

*   一个**训练函数**，它将接受我们的数据集训练样本和标签以及一些可选参数。
*   一个**预测函数**，它将获取一个未标记的数据集，并根据训练数据分配一组标签。

在项目的根目录下创建一个名为`classifyTweets.php`的脚本。我们将使用他的脚本来实例化和测试我们的情感分析类。以下是我们将使用的模板:

```
<?php

namespace PhpmlExercise;
use PhpmlExercise\Classification\SentimentAnalysis;

require __DIR__ . '/vendor/autoload.php';

// Step 1: Load the Dataset

// Step 2: Prepare the Dataset

// Step 3: Generate the training/testing Dataset

// Step 4: Train the classifier 

// Step 5: Test the classifier accuracy 
```

### 步骤 1:加载数据集

在前面的例子中，我们已经有了将 CSV 加载到 dataset 对象的基本代码。我们将使用相同的代码进行一些调整:

```
<?php
...
use Phpml\Dataset\CsvDataset;
...
$dataset = new CsvDataset('datasets/clean_tweets.csv',1);

$samples = [];
foreach ($dataset->getSamples() as $sample) {
    $samples[] = $sample[0];
} 
```

这将生成一个仅包含特征的平面数组，在本例中是 tweet 文本，我们将使用它来训练我们的分类器。

### 步骤 2:准备数据集

现在，拥有原始文本并将其传递给分类器是没有用的，也是不准确的，因为每条推文本质上都是不同的。幸运的是，在尝试应用分类或机器学习算法时，有一些处理文本的方法。对于本例，我们将利用以下两个类:

*   **令牌计数矢量器**:这将把一组文本样本转换成一个令牌计数矢量。本质上，我们 tweet 中的每个单词都成为一个唯一的数字，并记录一个单词在特定文本样本中的出现次数。
*   **Tf-idf Transformer** :词频(term frequency)的简称——逆文档频率，是一种数字统计，旨在反映一个词对集合或语料库中的文档有多重要。

让我们从我们的文本矢量器开始:

```
<?php
...
use Phpml\FeatureExtraction\TokenCountVectorizer;
use Phpml\Tokenization\WordTokenizer;

...
$vectorizer = new TokenCountVectorizer(new WordTokenizer());

$vectorizer->fit($samples);
$vectorizer->transform($samples); 
```

接下来，应用 Tf-idf 变压器:

```
<?php
...

use Phpml\FeatureExtraction\TfIdfTransformer;
...
$tfIdfTransformer = new TfIdfTransformer();

$tfIdfTransformer->fit($samples);
$tfIdfTransformer->transform($samples); 
```

我们的样本数组现在的格式很容易被我们的分类器理解。我们还没有完成，我们需要给每个样本贴上相应的情绪标签。

### 步骤 3:生成训练数据集

幸运的是，PHP-ML 已经满足了这一需求，代码非常简单:

```
<?php
...
use Phpml\Dataset\ArrayDataset;
...
$dataset = new ArrayDataset($samples, $dataset->getTargets()); 
```

我们可以继续使用这个数据集来训练我们的分类器。然而，我们缺少一个用于验证的测试数据集，因此我们将稍微“欺骗”一下，将原始数据集一分为二:一个训练数据集和一个更小的数据集，后者将用于测试我们模型的准确性。

```
<?php
...
use Phpml\CrossValidation\StratifiedRandomSplit;
...
$randomSplit = new StratifiedRandomSplit($dataset, 0.1);

$trainingSamples = $randomSplit->getTrainSamples();
$trainingLabels     = $randomSplit->getTrainLabels();

$testSamples = $randomSplit->getTestSamples();
$testLabels      = $randomSplit->getTestLabels(); 
```

这种方法被称为交叉验证。该术语来自统计学，可定义如下:

> 交叉验证，有时称为旋转估计，是一种模型验证技术，用于评估统计分析的结果如何推广到独立的数据集。它主要用于以预测为目标的环境中，人们希望估计预测模型在实践中的准确程度。—[Wikipedia.com](https://en.wikipedia.org/wiki/Cross-validation_(statistics))

### 步骤 4:训练分类器

最后，我们准备回过头来实现我们的`SentimentAnalysis`类。如果你到现在还没有注意到，机器学习的很大一部分是关于收集和处理数据的；机器学习模型的实际实现往往要简单得多。

为了实现我们的情感分析类，我们有三种分类算法可用:

*   支持向量分类
*   KNearestNeighbors
*   朴素贝叶斯

在本练习中，我们将使用所有分类器中最简单的一个，NaiveBayes 分类器，所以让我们继续更新我们的类来实现 train 方法:

```
<?php

namespace PhpmlExercise\Classification;
use Phpml\Classification\NaiveBayes;

class SentimentAnalysis
{
    protected $classifier;

    public function __construct()
    {
        $this->classifier = new NaiveBayes();
    }
    public function train($samples, $labels)
    {
        $this->classifier->train($samples, $labels);
    }
} 
```

正如你所看到的，我们让 PHP-ML 为我们做所有繁重的工作。我们只是为我们的项目创建一个不错的小抽象。但是我们如何知道我们的分类器是否真的在训练和工作呢？是时候使用我们的`testSamples`和`testLabels`了。

### 步骤 5:测试分类器的准确性

在我们继续测试我们的分类器之前，我们必须实现预测方法:

```
<?php
...
class SentimentAnalysis
{
...
    public function predict($samples)
    {
        return $this->classifier->predict($samples);
    }
} 
```

再说一次，PHP-ML 帮了我们一个大忙，帮我们完成了所有繁重的工作。让我们相应地更新我们的`classifyTweets`类:

```
<?php
...
$predictedLabels = $classifier->predict($testSamples); 
```

最后，我们需要一种方法来测试我们训练好的模型的准确性；幸运的是 PHP-ML 也涵盖了这一点，他们有几个度量类。在我们的例子中，我们感兴趣的是模型的准确性。让我们看一下代码:

```
<?php
...
use Phpml\Metric\Accuracy;
...
echo 'Accuracy: '.Accuracy::score($testLabels, $predictedLabels); 
```

我们应该看到以下内容:

```
Accuracy: 0.73651877133106% 
```

## 结论

这篇文章有点冗长，所以让我们回顾一下目前为止我们所学的内容:

*   从一开始就拥有一个好的数据集对于实现机器学习算法至关重要。
*   监督学习和非监督学习的区别。
*   交叉验证在机器学习中的意义和用途。
*   矢量化和转换对于为机器学习准备文本数据集至关重要。
*   如何使用 PHP-ML 的 NaiveBayes 分类器实现 Twitter 情感分析？

这篇文章也是对 PHP-ML 库的介绍，希望能给你一个好主意，让你知道这个库能做什么，以及如何把它嵌入到你自己的项目中。

最后，这篇文章绝不是全面的，有很多东西需要学习、改进和实验；以下是一些帮助你开始进一步改进的想法:

*   用支持向量分类算法替换 NaiveBayes 算法。
*   如果您尝试对整个数据集(14，000 行)运行，您可能会注意到这个过程是多么占用内存。尝试实现模型持久性，这样就不必在每次运行时都进行训练。
*   将数据集生成移动到它自己的帮助器类中。

我希望这篇文章对你有用。如果你有一些关于 PHP-ML 的应用想法或者任何问题，不要犹豫，把它们放到评论区吧！

## 分享这篇文章