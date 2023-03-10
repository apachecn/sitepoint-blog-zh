# 用不到 100 行代码构建微软的什么狗人工智能

> 原文：<https://www.sitepoint.com/building-microsofts-what-dog-ai-in-under-100-lines-of-code/>

最近，微软发布了一款利用人工智能检测狗的品种的应用程序。当我在我的小猎犬身上测试时，虽然…

![The app identifies a beagle as a Saluki](img/872e8b98c51e3604b024fc90a0132b3b.png)

嗯，不完全是，app。不完全是。

在我非 SitePoint 的时间里，我还为 [Diffbot](https://www.sitepoint.com/blog/) 工作——你[可能在过去几周听说过](http://techcrunch.com/2016/02/11/diffbot-aims-to-build-the-intel-of-data-for-artificial-intelligence/?ncid=rss)的初创公司——他们也涉足人工智能。为了测试他们的比较，在本教程中，我们将使用 Diffbot 的技术重新创建微软的应用程序，看看它是否在识别我们扔向它的可爱野兽方面做得更好！

我们将构建一个非常原始的单文件“应用程序”,用于上传图片并在表单下输出关于品种的信息。

## 先决条件

如果你想跟进，请在[Diffbot.com](http://diffbot.com)注册一个 14 天的免费令牌，如果你还没有那里的账户的话。

为了安装客户端，我们使用下面的`composer.json`文件:

```
{
    "require": {
        "swader/diffbot-php-client": "^2",
        "php-http/guzzle6-adapter": "^1.0"
    },
    "minimum-stability": "dev",
    "prefer-stable": true,
    "require-dev": {
        "symfony/var-dumper": "^3.0"
    }
} 
```

然后，我们运行`composer install`。

因为[普利](https://www.sitepoint.com/can-puliphp-re-revolutionize-php-package-development/)包的一部分仍处于测试阶段，而且它现在是 [PHP HTTP](http://php-http.readthedocs.org) 项目的一个依赖项，所以有了*最小稳定性*标志。*prefere stable*指令是为了确保使用最高稳定版本的包(如果有的话)。我们还需要一个 HTTP 客户端，在这种情况下，我选择了 Guzzle6，尽管 Diffbot PHP 客户端通过[HTTP plug](https://www.sitepoint.com/breaking-free-from-guzzle5-with-php-http-and-httplug/)支持任何现代的 HTTP 客户端，所以请随意使用您自己喜欢的。

一旦安装了这些项目，我们就可以创建一个`index.php`文件，它将包含我们应用程序的所有逻辑。但是首先，自举:

```
<?php

require 'vendor/autoload.php';

$token = 'my_token'; 
```

## 上传

让我们在我们的`index.php`文件的 PHP 内容之上构建一个原始的上传表单。

```
<form action="/" method="post" enctype="multipart/form-data">
    <h2>Please either paste in a link to the image, or upload the image directly.</h2>
    <h3>URL</h3>
    <input type="text" name="url" id="url" placeholder="Image URL">
    <h3>Upload</h3>
    <input type="file" name="file" id="file">
    <input type="submit" value="Analyze">
</form>

<?php

... 
```

这里我们只关注 PHP 端，所以我们将省略 CSS。我向你的眼睛道歉。

![Ugly form](img/d12d4c3edcee24afcc9d6ede3adf29a7.png)

我们将使用 Imgur 来托管图像，这样我们就不必为了调用 Diffbot 而托管应用程序(即使我们的应用程序不是公开的，图像也将是公开的，从而节省我们的托管成本)。让我们首先通过[这个链接](https://api.imgur.com/oauth2/addclient)在 Imgur 上注册一个应用程序:

![Imgur registration](img/9e1c03bce8770784ab1ebee530a608ce.png)

这将产生一个客户端 ID 和一个密码，尽管我们将只使用客户端 ID(匿名上传)，所以我们应该将它添加到我们的文件中:

```
$token = 'my_token';
$imgur_client = 'client'; 
```

## 分析图像

那么，分析将如何进行呢？

如文档中的[所述，Diffbot 的](http://diffbot-php-client-docs.readthedocs.org/en/latest/api-image.html)[图像 API](https://www.diffbot.com/dev/docs/image/) 可以接受一个 URL，然后扫描页面中的图像。对所有找到的图像进行额外的分析，并返回一些关于它们的数据。

我们需要的数据是附加到图像条目的`tags` Diffbot。`tags`是一个 JSON 对象的数组，每个对象包含一个标签 label，以及一个到相关资源的[http://dbpedia.org](http://dbpedia.org)的链接。在本教程中我们不需要这些链接，但是我们将在后面的文章中研究它们。`tags`数组的形式如下:

```
"tags": [
        {
          "id": 4368,
          "label": "Beagle",
          "uri": "http://dbpedia.org/resource/Beagle"
        },
        {
          "id": 2370241,
          "label": "Treeing Walker Coonhound",
          "uri": "http://dbpedia.org/resource/Treeing_Walker_Coonhound"
        }
      ] 
```

如您所见，每个标签都有上述值。如果只有一个标签，那么只有一个对象存在。默认情况下，Diffbot 为每个条目返回多达 5 个标签*——因此每个图像可以有多达 5 个标签，并且它们不必直接相关(例如，提交跑鞋的图像可能会同时返回标签*耐克*和标签*鞋*)。*

我们将使用这些标签作为狗品种的猜测。一旦请求通过并在响应中返回标签，我们将在图像下方打印建议的标签。

## 处理提交

为了处理表单，我们将在令牌声明下面添加一些基本逻辑。：

```
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    if (isset($_FILES['file']['tmp_name']) && !empty($_FILES['file']['tmp_name'])) {
        $filename = $_FILES['file']['tmp_name'];

        $c = new Client();
        $response = $c->request('POST', 'https://api.imgur.com/3/image.json', [
            'headers' => [
                'authorization' => 'Client-ID ' . $imgur_client
            ],
            'form_params' => [
                'image' => base64_encode(fread(fopen($filename, "r"),
                    filesize($filename)))
            ]
        ]);

        $body = json_decode($response->getBody()->getContents(), true);
        $url = $body['data']['link'];
        if (empty($url)) {
            echo "<h2>Upload failed</h2>";
            die($body['data']['error']);
        }
    }

    if (!isset($url) && isset($_POST['url'])) {
        $url = $_POST['url'];
    }

    if (!isset($url) || empty($url)) {
        die("That's not gonna work.");
    }

    $d = new Swader\Diffbot\Diffbot($token);
    /** @var Image $imageDetails */
    $imageDetails = $d->createImageAPI($url)->call();
    $tags = $imageDetails->getTags();

    echo "<img width='500' src='{$url}'>";

    switch (count($tags)) {
        case 0:
            echo "<h4>We couldn't figure out the breed :(</h4>";
            break;
        case 1:
            echo "<h4>The breed is probably " . labelSearchLink($tags[0]['label']) . "</h4>";
            echo iframeSearch($tags[0]['label']);
            break;
        default:
            echo "<h4>The breed could be any of the following:</h4>";
            echo "<ul>";
            foreach ($tags as $tag) {
                echo "<li>" . labelSearchLink($tag['label']) . "</li>";
            }
            echo "</ul>";
            echo iframeSearch($tags[0]['label']);
            break;
    }
} 
```

我们首先检查是否选择了一个文件进行上传。如果是这样，它优先于基于链接的提交。图像被上传到 Imgur，然后 Imgur 返回的 URL 被传递给 Diffbot。如果只提供了一个 URL，它将被直接使用。

我们直接使用 Guzzle 作为 HTTP 客户端，因为我们已经安装了它，所以 Diffbot PHP 客户端可以使用它进行 API 调用。

在图像数据返回后，我们从图像对象中获取标签，并将它们输出到屏幕上，同时显示该品种的 Bing 搜索结果的链接，以及显示这些结果的 iframe。

构建搜索链接和 iframe HTML 元素的函数如下:

```
function labelSearchLink($label) {
    return '<a href="http://www.bing.cimg/search?q='.urlencode($label).'&qs=AS&pq=treein&sc=8-6&sp=1&cvid=92698E3A769C4AFE8C6CA1B1F80FC66D&FORM=QBLH" target="_blank">'.$label.'</a>';
}

function iframeSearch($label) {
    return '<iframe width="100%" height="400" src="http://www.bing.cimg/search?q='.urlencode($label).'&qs=AS&pq=treein&sc=8-6&sp=1&cvid=92698E3A769C4AFE8C6CA1B1F80FC66D&FORM=QBLH" />';
} 
```

![Beagle slightly misidentified](img/ebaaaa8fc006fd940fef60f25c3c5f2e.png)

再次，请原谅代码和网页的设计-因为这只是一个快速原型，CSS 和框架会分散注意力。

## 测试和比较

从上面的图片中我们可以看到，Diffbot 也错误地识别了猎犬——但不像微软那样严重。在这种情况下，我的小猎犬看起来更像一只爬树的猎浣熊犬，而不是一只典型的小猎犬。

让我们看更多的例子。

![Diffbot fails, MS succeeds](img/52327bf10b98bd9067d51a8d625feaa8.png)

啊，诅咒！微软赢得了这一轮——diff bot 认为它有更好的机会在巴吉度猎犬和爬树猎浣熊犬之间进行猜测，但两者都错过了。再来一杯怎么样？

![Bingo!](img/0892cadd717419f8d142c3267c5d4adf.png)

答对了。两者都是正确的，尽管 Diffbot 再次建议步行机作为替代方案，以确保安全。好吧，这个有点太明显了——来个难的怎么样？

![Derps](img/8ab7c282ac72de5a494429ef96b088a6.png)

有趣的是，这张图片似乎让两个人都想起了威尔士柯基犬！

但是，如果图像中有不止一只狗呢？

![Whoops, Diffbot got it very wrong](img/a422636a7ee0cfa26d5562b40c7e466d.png)

可爱，Diffbot，但没有雪茄-做得好，微软！

好吧，最后一个。

![Sleeping beagle](img/a3cf2421bed1aff086b088ef368d181e.png)

两方面都做得很好。很明显，“狗探测 AI”已经被刷爆了！诚然，Diffbot 确实有一个小优势，即它也能够检测图像中的人脸、文本、品牌、其他动物类型等，但它们的“狗识别”是脚趾对脚趾的。

## 结论

在本教程中，我们看到了利用现代人工智能的力量来识别狗的品种是多么容易，至少在一定程度上是准确的。虽然这两个引擎都有很大的改进空间，但我们给它们提供的内容越多，它们就会变得越好。

这是对强大的远程机器学习算法易用性的演示，也是对我们即将探索的更复杂主题的介绍——敬请关注！

## 分享这篇文章