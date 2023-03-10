# 用 GDELT 2 配合 PHP 分析世界！

> 原文：<https://www.sitepoint.com/using-gdelt-2-with-php-to-analyze-the-world/>

你对世界政治事件感兴趣吗？你想玩世界上最大的数据库之一吗？如果你对其中任何一个问题的回答是肯定的，继续读下去——这会让你感兴趣的！

这篇文章是对在 PHP 中使用 GDELT 的承诺的跟进。

我将向您展示一个简单的例子，如何通过 PHP 的 BigQuery 使用 GDELT，以及如何在 web 页面上可视化结果。一路上，我会告诉你更多关于 GDELT 的事情。

![Vector illustration of abstract world map](img/f26290f7bc410747190f2487902be90d.png)

## GDELT

GDelt(全球事件、语言和语调数据库)是世界上最大的政治事件公开数据库。它是由 [Kalev Leetaru](https://en.wikipedia.org/wiki/Kalev_Leetaru) ( [个人网站](http://www.kalevleetaru.com/))在 [Philip A. Schrodt](https://en.wikipedia.org/wiki/Philip_Schrodt) 等人 2011 年工作的基础上开发的。这些数据可以通过 zip 文件下载，自 2014 年以来，可以通过谷歌的 BigQuery 网络界面和其 API 以及 [GDELT 分析服务](http://analysis.gdeltproject.org/)进行查询。
[GDELT 项目](http://www.gdeltproject.org/):

> 以 100 多种语言监控世界上几乎每个国家每个角落的广播、印刷和网络新闻，并识别每天每一秒推动我们全球社会的人、地点、组织、计数、主题、来源、情感、引用、图像和事件，为整个世界的计算创建一个免费的开放平台。

## 在线实验

所有 GDELT 数据都可以通过 BigQuery 获得。这个“大数据”数据库有一个 web 界面，允许您在使用自动建议功能的同时查看表结构、预览数据和进行查询。

为了在线试验 GDELT 数据集，你需要有一个 Google 账户。转到 [BigQuery 仪表板](https://bigquery.cloud.google.com/)。

如果你还没有谷歌云项目，系统会提示你创建一个。这是必需的。这个项目将是你的工作环境，所以你最好为它选择一个合适的名字。

您可以通过“编写查询”创建自己的查询。比如这一个:

```
SELECT EventCode, Actor1Name, Actor2Name, SOURCEURL, SqlDate
FROM [gdelt-bq:gdeltv2.events]
WHERE Year = 2016
LIMIT 20 
```

## GDELT 工具和 API

GDELT 允许您从其网站内快速创建可视化。转到[分析页面](http://analysis.gdeltproject.org/)，创建一个选择，一个可视化的链接将会通过邮件发送给您。

GDELT 最近开始开放两个 API，允许您从单个 URL 创建自定义数据馈送。这些提要可以直接输入 CartoDB，以创建一个实时可视化。

*   [GKG·乔森](http://blog.gdeltproject.org/announcing-our-first-api-gkg-geojson/)创建知识图的 feeds([教程](http://blog.gdeltproject.org/tutorial-instant-news-maps-using-cartodb-gkg-api/)
*   [全文搜索 API](http://blog.gdeltproject.org/announcing-the-gdelt-full-text-search-api/) 创建过去 24 小时的新闻提要

您可以使用任何人都可以使用的工具来查询 GDELT 和创建可视化。看看这个最近的例子，[向难民展示一些爱【Kenneth Davis 用来自](https://geovisualist.com/2015/11/22/showing-refugees-some-love/) [GDELT 全球知识图 API](http://blog.gdeltproject.org/announcing-our-first-api-gkg-geojson/) 的数据制作的，并用 [CartoDB](https://carto.com/) 可视化。或者这个，[世界如何看待希拉里·克林顿&唐纳德·特朗普【CuriousGnu 通过下载 CSV 格式的查询结果并将其导入 CartoDB。](https://www.curiousgnu.com/how-the-world-sees-clinton-trump)

## 概念:CAMEO 本体论

为了使用 GDELT，您至少需要了解一些基本概念。这些概念是由 Philip A. Schrodt 创建的，并形成了 CAMEO 本体(用于冲突和调解事件观察)。

*   一个**事件**是两方的政治互动。其事件代码描述了事件的类型，即 1411:“示威或集会要求更换领导人”。
*   一名**演员**是该事件的两名参与者之一。行为者可以是一个国家(“国内”)或其他(“国际”，即一个组织、运动或公司)。参与者代码是一个或多个三个字母缩写的序列。例如，下面的每个三元组进一步指定一个 actor。NGO =非政府组织，NGO hlhrcr(HLH RCR 非政府组织)=非政府组织/卫生/红十字会。
*   事件的**音**是介于-100(极负)和+100(极正)之间的分数。介于-5 和 5 之间的值是最常见的。
*   一个事件的 **Goldstein 等级**是一个介于-10 和+10 之间的分数，反映了该类型事件对一个国家的稳定可能产生的影响。

包含所有事件动词和演员类型代码的完整 CAMEO 代码簿位于[这里](http://data.gdeltproject.org/documentation/CAMEO.Manual.1.1b3.pdf)。

## 为 BigQuery 设置一个帐户

如果您想使用 BigQuery 从应用程序内部访问 GDELT，您将使用 Google 的云平台。我将告诉您如何创建一个 BigQuery 帐户，但是您的情况可能与我在这里描述的有些不同。谷歌不时地改变它的用户界面。

你需要一个谷歌账户。如果你没有，你必须[创建它](https://accounts.google.com/SignUpWithoutGmail)。然后，进入[你的控制台](https://console.cloud.google.com/)，如果你还没有一个项目，你将被要求创建一个项目。

看看控制台。在左上角你会看到一个汉堡菜单(一个有三条水平线的图标),可以进入平台的所有部分。

使用您的项目，转到 [API 库](https://console.cloud.google.com/apis/library)并启用 BigQuery API。

接下来，为您的项目创建一个[服务帐户](https://console.cloud.google.com/iam-admin/serviceaccounts/project),并赋予这个帐户 BigQuery 用户的角色。这允许它运行查询。您可以稍后在 IAM 选项卡上更改权限。对于“成员”，选择您的服务帐户 ID。

您的[服务帐户](https://console.cloud.google.com/iam-admin/iam/project)允许您创建一个密钥(通过下拉菜单)，一个 JSON 文件，您可以下载并保存在一个安全的地方。您的 PHP 代码中需要这个键。

最后，你需要[为你的项目建立一个账单账户](https://console.cloud.google.com/billing)。这可能会让你感到惊讶，因为 GDELT 访问每月高达 1tb 是免费的，但这是必要的，即使 Google 不会向你收取任何费用。

谷歌提供 3 个月的免费试用账户。你可以用它来做实验。如果您真的开始使用您的应用程序，您将需要提供信用卡或银行帐户信息。

## 用 PHP 访问数据

你以前可以通过 [Google APIs PHP 客户端](https://github.com/google/google-api-php-client)访问 BigQuery，但是现在首选的库是[Google Cloud PHP 客户端库](https://googlecloudplatform.github.io/google-cloud-php/#/)。

我们可以用 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 安装它:

```
composer require google/cloud 
```

代码本身非常简单。将项目密钥的路径替换为从 Google Cloud 控制台下载的路径。

```
use Google\Cloud\BigQuery\BigQueryClient;

// setup Composer autoloading
require_once __DIR__ . '/vendor/autoload.php';

$sql = "SELECT theme, COUNT(*) as count
    FROM (
        select SPLIT(V2Themes,';') theme
        from [gdelt-bq:gdeltv2.gkg]
        where DATE>20150302000000 and DATE < 20150304000000 and AllNames like '%Netanyahu%' and TranslationInfo like '%srclc:heb%'
    )
    group by theme
    ORDER BY 2 DESC
    LIMIT 300
";

$bigQuery = new BigQueryClient([
    'keyFilePath' => __DIR__ . '/path/to/your/google/cloud/account/key.json',
]);

// Run a query and inspect the results.
$queryResults = $bigQuery->runQuery($sql);

foreach ($queryResults->rows() as $row) {
    print_r($row);
} 
```

## 探索数据集

我们甚至可以查询元数据。让我们从列出项目的数据集开始。数据集是表的集合。

```
$bigQuery = new BigQueryClient([
    'keyFilePath' => '/path/to/your/google/cloud/account/key.json',
    'projectId' => 'gdelt-bq'
]);

/** @var  Dataset[] $datasets */
$datasets = $bigQuery->datasets();

$names = array();
foreach ($datasets as $dataset) {
    $names[] = $dataset->id();
}

print_r($names); 
```

注意，在查询元数据时，我们必须在客户端配置中提到项目 ID ( `gdelt-bq`)。

这是我们代码的结果:

```
Array
(
    [0] => extra
    [1] => full
    [2] => gdeltv2
    [3] => gdeltv2_ngrams
    [4] => hathitrustbooks
    [5] => internetarchivebooks
    [6] => sample_views
) 
```

## 一点历史

政治事件数据已经保存了几十年。这一领域的一个重要里程碑是在 2010 年左右引入了综合危机预警系统(ICEWS)计划。

Philip A. Schrodt 写的关于全球事件数据采集的一个有趣且有时很有趣的概述是大容量、接近实时的政治事件数据的自动化生产。

新闻故事不断从广泛的来源收集，如 AfricaNews、法新社、美联社在线、英国广播公司监测、基督教科学箴言报、合众国际社和华盛顿邮报。这些数据是从几个新闻故事来源收集来的。这些曾经是手工编码的，但现在是通过各种自然语言处理(NLP)技术来完成的。

GDELT 1 用一个名为 [TABARI](http://eventdata.parusanalytics.com/software.dir/tabari.html) 的 C++库解析新闻故事，并将编码数据输入数据库。TABARI 使用基于模式的浅层解析器解析文章，并执行命名实体识别。它目前涵盖了从 1979 年至今的时期。

Leetaru 和 Schrodt 对 GDELT 的早期介绍描述了新闻来源和编码技术，可以在这里阅读。

2015 年 2 月，GDELT 2.0[发布](http://blog.gdeltproject.org/gdelt-2-0-our-global-world-in-realtime/)。TABARI 被 [PETRARCH](https://github.com/openeventdata/petrarch) 库(用 Python 编写)所取代。现在用的是斯坦福 CoreNLP 解析器，文章都是翻译的。如果你想更多地了解这一变化背后的原因，你可以阅读非常有启发性的 [Philip A. Schrodt 的幻灯片](http://eventdata.parusanalytics.com/presentations.dir/Schrodt.CRS.GDELT.pdf)。GDELT 2 还用一个[全局知识图](http://blog.gdeltproject.org/gdelt-global-knowledge-graph/)扩展了事件数据。

2015 年 9 月，来自互联网档案馆和 Hathi Trust 的数据被[整合到](http://blog.gdeltproject.org/3-5-million-books-1800-2015-gdelt-processes-internet-archive-and-hathitrust-book-archives-and-available-in-google-bigquery/)GDELT big query 数据库中。

## GDELT 数据集

在 GDELT 项目网站的[页面上有数据集的概述。](http://blog.gdeltproject.org/the-datasets-of-gdelt-as-of-february-2016/)

数据集分组如下:

*   原始 GDELT 1 数据集:[满](https://bigquery.cloud.google.com/dataset/gdelt-bq:full)。查看[这篇博客](https://cloudplatform.googleblog.com/2014/05/worlds-largest-event-dataset-now-publicly-available-in-google-bigquery.html)中卡列夫·利塔鲁的例子。
*   GDELT 2 数据集: [gdeltv2](https://bigquery.cloud.google.com/dataset/gdelt-bq:gdeltv2) 和 [gdeltv2_ngrams](https://bigquery.cloud.google.com/dataset/gdelt-bq:gdeltv2_ngrams)
*   Hathi Trust Books 数据集: [hathitrustbooks](https://bigquery.cloud.google.com/dataset/gdelt-bq:hathitrustbooks)
*   互联网存档数据集: [internetarchivebooks](https://bigquery.cloud.google.com/dataset/gdelt-bq:internetarchivebooks) 。[本页](https://cloud.google.com/bigquery/public-data/gdelt-books)显示了一个从互联网档案馆查询图书数据的例子

关于 GDELT 数据集(1 和 2)的表和字段的文档可在 GDELT 项目网站的[文档部分获得。](http://gdeltproject.org/data.html#documentation)

关于互联网档案和 HathiTrust 图书档案的表格和字段的文档可以在[互联网档案+ HathiTrust 页面](http://blog.gdeltproject.org/3-5-million-books-1800-2015-gdelt-processes-internet-archive-and-hathitrust-book-archives-and-available-in-google-bigquery/)上找到。

## 免费吗？

超过 1 TB，谷歌每 TB 收费 5 美元。

当我听说每个月处理的第一个[TB](https://en.wikipedia.org/wiki/Terabyte)的数据是免费的时候，我想:1TB 的数据对任何人来说应该都够了吧！我输入了自己的银行账户信息，并进行了几个简单的查询。

一周后，当新的一个月开始时，我收到了来自谷歌云的消息:

> “我们将很快自动向您的银行账户收费”

我被收了 10.96 欧元！那是怎么发生的？这是我仔细研究定价的时候。

Google Cloud 关于定价的页面其实很清楚。在查询 GDELT 的上下文中，您不需要为加载、复制和导出数据付费。也不需要为元数据操作(例如，列表)付费。你只需为查询付费。准确地说:

> 查询定价是指运行 SQL 命令和用户定义函数的成本。BigQuery 使用一个度量标准对查询收费:处理的字节数。

它不是查询的大小，也不是结果集的大小(正如您可能假设的那样)，而是 BigQuery 在处理数据时抽取的数据的大小。看着我的[账单面板](https://console.cloud.google.com/billing/),我发现谷歌向我收取了

```
BigQuery Analysis: 3541.117 Gibibytes 
```

BigQuery 为我的查询处理了 3541 千兆字节！除以 1024，它[将](http://www.convertunits.com/from/gibibytes/to/terabyte)转换成 345812207 太字节(太字节可能是[更准确的说法是](https://en.wikipedia.org/wiki/Terabyte))。去掉第一个免费 TB，换算成欧元(当时的汇率= 0.892)，我最后得到 10.96 欧元。我计算出上面提到的单个 GDELT 示例查询(带有子查询的那个)的成本是惊人的 2.20 美元！

我希望我没有吓到你。创建不占用太多内存的有用查询是可能的，但是您需要缓存您的结果，并且小心地手工创建您的查询。BigQuery 在这方面帮助了我们。作为对查询的响应，在结果行的旁边，它还为我们提供了“totalBytesProcessed”信息。由此，我们可以用美元计算成本:

```
$results = $bigQuery->runQuery($sql);
$info = $results->info();

$tb = ($info['totalBytesProcessed'] / Constants::BYTES_PER_TEBIBYTE); // 1099511627776
$cost = $tb * Constants::DOLLARS_PER_TEBIBYTE; // 5 
```

## 在 BigQuery 中查询

账单页面的下部信息非常丰富。它告诉我们，BigQuery 每次只需要一条记录就可以加载一整列数据。例如，我跑了:

```
SELECT Actor1Name
FROM [gdelt-bq:gdeltv2.events]
WHERE GLOBALEVENTID = 526870433 
```

在关系数据库中，这将是一个非常快速的查询，它使用 GLOBALEVENTID 上的索引来查找并返回单个记录。在 BigQuery 中，它加载了一整列 3.02GB 的数据，需要几秒钟来运行(！)–big query 不使用索引。每当它需要一列时，就会读取整列。当一个列非常大时，它被划分到多个机器上，这些机器将并行运行来解决您的查询。BigQuery 没有针对小表进行优化，但是它可以在几秒钟内对数 Pb 的数据进行查询。

要了解 BigQuery 架构的细节，你可以阅读[这篇论文](http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/36632.pdf)或[这本关于谷歌 BigQuery 分析的书](https://www.amazon.com/Google-BigQuery-Analytics-Jordan-Tigani/dp/1118824822/)。

## 形象化

当我写这篇文章的时候，我想到了脚本化的 GDELT 方法可能带来的附加值。我认为事实上你已经完全控制了查询和可视化！作为一个例子，我自己并不太有创造力，我决定创建一个过去两天报道的所有打架事件的图表。

GDELT 帮助你[创建奇特的可视化效果](http://analysis.gdeltproject.org/)，但是没有为它们准备的 API。然而，网站确实向我们展示了它们是用什么工具制作的。我们可以用同样的工具自己制作图表。

我选择了[热图](http://analysis.gdeltproject.org/module-event-heatmapper.html)，因为它最能表达我的意图。在输出部分，我发现它使用了 [heatmap.js](https://www.patrick-wied.at/static/heatmapjs/) 在谷歌地图上生成一个覆盖图。我将代码从`heatmap.js`复制到我的项目中，并根据我的需要进行了调整。

使用谷歌地图需要一个 API 密钥。你可以在这里得到一个。

我将 GDELT fights 热图示例[的代码放在了 Github](https://github.com/sitepoint-editors/gdelt-php-examples/blob/master/examples/heatmap.php) 上。

![Global Heatmap GDELT fights](img/9aa5f84a05a1172f6c9ef406a9da512d.png "Heatmap Fights")

该查询选择从现在到两天前添加的所有“fight”事件(190 系列事件代码)的地理坐标，并按照地理坐标对它们进行分组。它只采用根事件，试图从无关事件中过滤出相关事件。我还将 SOURCEURL 添加到我的选择中。我用它在地图上创建了可点击的标记，以更深的缩放级别显示，允许您访问事件的源文章。

```
SELECT MAX(ActionGeo_Lat) as lat, MAX(ActionGeo_Long) as lng, COUNT(*) as count, MAX(SOURCEURL) as source, GROUP_CONCAT(UNIQUE(EventCode)) as codes
FROM [gdelt-bq:gdeltv2.events] 
WHERE SqlDate > {$from} AND SqlDate < {$to}
AND EventCode in ('190', '191','192', '193', '194', '195', '1951', '1952', '196')
AND IsRootEvent = 1
AND ActionGeo_Lat IS NOT NULL AND ActionGeo_Long IS NOT NULL
GROUP BY ActionGeo_Lat, ActionGeo_Long 
```

我发现许多事件仍然与我的想法不太相关，所以要正确地做这件事，我必须对我的查询做更多的调整。此外，许多文章处理相同的事件，我只选择了其中一个在地图上显示。

![Event markers](img/6c22e7c2503ccce40a725bbbb67c499a.png)

至于费用呢？这个示例查询只需要几千兆字节来处理。因为是缓存的，所以一天最多需要执行一次。这样，它保持在 1TB 的限制之下，并且不花我一分钱！

## 提醒一句

小心不要天真地使用 GDELT 提供的数据。

*   仅仅因为某一现象的事件数量随着年份的进展而增加，并不意味着该现象增加。这可能只是意味着更多的数据变得可用，或者更多的来源对这一现象做出反应。
*   这些数据没有经过整理，没有人从无关的数据中挑选出相关的部分。
*   在许多情况下，参与者是未知的。

## 结论

GDELT 无疑提供了丰富的信息。在这篇文章中，我只是触及了它包含的所有想法、项目和工具的表面。GDELT 网站提供了几种无需编码就能创建可视化效果的方法。

如果您喜欢编码，或者您发现可用的工具不足以满足您的需求，您可以遵循上面显示的提示。由于可以通过 BigQuery 访问 GDELT，所以使用简单的 SQL 就可以很容易地提取出您需要的信息。但是要小心！使用 BigQuery 是要花钱的，除非您很好地准备了查询并使用了缓存。

这一次，我仅仅创建了一个简单的时间相关查询的可视化。在实践中，研究人员需要做更多的调整才能得到准确的结果，并以有意义的方式显示出来。

如果你受到这篇文章的启发，想创建一个新的 PHP 应用程序，请在下面的评论部分与我们分享。我们想知道 GDELT 在世界各地是如何使用的。

这篇文章的所有代码都可以在[相应的 Github repo](https://github.com/sitepoint-editors/gdelt-php-examples) 中找到。

## 分享这篇文章