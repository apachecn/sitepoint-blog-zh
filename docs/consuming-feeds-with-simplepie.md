# 使用 SimplePie 消费提要

> 原文：<https://www.sitepoint.com/consuming-feeds-with-simplepie/>

如果你像我一样是一个狂热的 feed 消费者，看到谷歌最近宣布 Reader 即将寿终正寝，你可能会有点难过。Reader 简单易用，但是你没有理由不能在自己开发的项目中拥有同样的功能。PHP 库 [SimplePie](http://simplepie.org/ "SimplePie: Super-fast, easy-to-use, RSS and Atom feed parsing in PHP") 允许快速简单的 feed 消费和显示。以下是你如何开始使用自己的阅读器的方法。

SimplePie 可以通过 Compose r 安装。Composer 下载了库并将 autoloader 文件包含在 PHP 脚本中后，您就可以开始编写自己的阅读器了。

```
{
    "require": {
        "simplepie/simplepie": "dev-master"
   }
}
```

## 基本功能

要使用 SimplePie，首先需要选择一个想要操作的 RSS 或 Atom 提要，并获取它的 URL。我将使用《纽约时报》作为我的例子——以下是你应该开始做的事情:

```
<?php
require_once 'autoloader.php';

$url = 'http://rss.nytimes.com/services/xml/rss/nyt/HomePage.xml';
$feed = new SimplePie();
$feed->set_feed_url($url);
$feed->init();
```

您可以在上面的代码中看到纽约时报提要的 URL，以及我们基于 SimplePie 的阅读器的开始。在最新版本的 SimplePie (v1.3)中，与以前的版本不同，构造函数不接受任何参数，我们使用`set_feed_url()`方法告诉它从哪里获取提要数据。一旦设置好 URL，我们调用`init()`，阅读器就准备好了。你不会看到任何只有上述内容的东西；我们仍然需要出去获取信息。

SimplePie 提供了许多类和方法来从 RSS 提要的任何给定部分提取信息。有了上面的开头，我们再补充下面的:

```
<?php
echo '<h1>' . $feed->get_title() . '</h1>';
echo '<p>' . $feed->get_description() . '</p>';
```

方法`get_title()`和`get_description()`完全按照它们所说的去做；他们返回整个提要的标题和描述。您可以获得提要作者(在某些情况下，但不是纽约时报提要)、提要贡献者、提要链接、版权，当然还有类似方法的项目。

为了实际显示由提要发布的任何故事，您需要使用`get_item()`或`get_items()`函数获得至少一个条目。现在，让我们只获取一个项目并从中获取一些信息。

```
<?php
$item = $feed->get_item(0);
echo '<article>';
echo '<header>';
echo '<p>Title: <a href="' . $item->get_link() . '">' . $item->get_title() . '</a></p>';
echo '<p>Author: ' . $item->get_author()->get_name() . '</p>';
echo '<p>Date: ' . $item->get_date('Y-m-d H:i:s') . '</p>';
echo '<p>Description: ' . $item->get_description() . '</p>';
echo '</header>';
echo $item->get_content(true);
echo '</article>';
```

这看起来肯定不太好，但是它向您展示了从提要条目中获取信息所必需的基础知识。那么，让我们回顾一下这其中的一些含义。

`get_item()`方法从提要中检索单个项目。提供的整数参数是提要条目编号，和数组一样，零是提要中第一个条目的索引。

`get_link()`方法返回一个指向提要条目本身的 URL，允许您打开任何正在处理的提要的文章/视频等。`get_title`和`get_description()`方法看起来应该很熟悉，因为它们与之前直接应用于提要本身时是一样的。

作者这一行看起来有点滑稽，但我相信你已经发现了——`get_author()`方法返回一个`Author`对象，我们在该对象上调用`get_name()`方法来检索文章作者。

`get_date()`方法采用任何标准的 PHP 日期格式字符串来显示日期。

最后，`get_content()`方法接受一个布尔参数，该参数表示要么只返回摘要信息(true ),要么尝试返回摘要信息，但如果没有提供摘要信息，则返回描述(false)。

## 选择项目

我们已经看到了基本内容，并显示了提要标题、描述以及发布到提要的单个条目的相关信息。这当然很好，但是如果不得不不断刷新页面来查看是否有新的条目，那就太令人沮丧了，更令人沮丧的是我们看不到任何过去的条目！

SimplePie 很容易做到这一点，我已经简单介绍了我们的选项:`get_item()`和`get_items()`。这两个函数用于以两种不同的方式获取我们的提要条目及其内容。

我们演示的第一个选项采用一个整数来指定我们想要获取的提要条目“数组”中的哪个条目。在一个循环中与方法`get_item_quantity()`结合使用允许我们显示提要中的所有项目或项目的子集。

```
<?php
$itemQty = $feed->get_item_quantity();
for ($i = 0; $i < $itemQty; $i++) {
...
}
```

如果您希望在显示提要条目时使用分页，那么这个解决方案无论如何都不是很好。为此，我们可以使用方法`get_items()`。它接受两个整数:偏移量和项数。这允许我们通过为两者传递零来获得所有的条目，或者，真正方便的是，允许我们在所有可用的提要条目中的任何地方获得小的子集。

```
<?php
foreach ($feed->get_items(3, 3) as $item) {
...
}
```

这种用法是显示三个项目的第二页。

## 贮藏

随着所有这些数据的流动，每次加载页面时处理整个 feed 会很费力，但是不要担心……simple pie 已经解决了这个问题。有几个内置的缓存提要数据的选项，因此您不必每次都提取整个提要。SimplePie 使用条件 GET 来确定自上次检索以来提要是否已经更新。为了存储内容，您的缓存存储选项是文件系统、MySQL、Memcache，或者——如果您愿意的话——您可以编写自己的处理程序。

最容易实现的是缓存到文件。方法如下:

```
<?php
$feed = new SimplePie();
$feed->set_feed_url($url);
$feed->enable_cache();
$feed->init();
```

就是这样！新添加的行打开 SimplePie 的缓存并将数据写出到文件中。您需要确保您的文档根目录包含一个名为`cache`的可写目录，并且一切就绪。如果您喜欢指定不同的位置，也很容易。只需使用方法`set_cache_location()`并向其传递一个字符串，该字符串指向您想要写入缓存文件的位置。同样的方法也可以用在用 MySQL 和 Memcache 进行缓存的情况下。

## 结论

这些是使用 SimplePie 库的最基本的原则。您已经学习了如何设置和初始化提要，从提要中获取一个或多个条目，并解析这些条目中包含的信息以进行显示。

当然，SimplePie 的功能并不止于此。它还让您能够一次从多个 feeds 中提取项目和解析信息——允许在您的指尖显示混合信息，所有这些都由您控制。深入他们的 [API 文档](http://simplepie.org/api/ "SimplePie 1.3 API Documentation")；这是一个很好的资源，会很好地指导你。

我期待看到下一个最好的谷歌阅读器替代品，由 SimplePie 提供支持，由你编写！您可以在 GitHub 上找到本文附带的一个简短示例来帮助您入门。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章