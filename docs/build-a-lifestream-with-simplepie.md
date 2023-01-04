# 用 SimplePie 构建 Lifestream

> 原文：<https://www.sitepoint.com/build-a-lifestream-with-simplepie/>

如果你和我一样，你的网络生活中的点点滴滴到处都是——照片、评论、推文、博客、喜欢的链接——藏在各种各样的个人资料中。当然，这些资料中有许多提供了 RSS 提要，时机已经成熟。把所有这些都集中在一个页面上不是很好吗？我想是的！

在本教程中，我们将创建一个页面，收集你生活中所有这些小细节的 RSS 源，并在一个地方呈现它们。为此，我们将使用 SimplePie，一个用 PHP 编写的提要解析类。它很强大，是开源的，而且很容易使用。然后，我们将对输出做一些修改，用 HTML 和 CSS 使它看起来更漂亮。如果您发现需要从许多来源(比如新闻页面)聚合 RSS，我们将讨论的技术也很有用。我们开始吧！

## 你需要什么

*   SimplePie 库。
*   能够运行它的 web 服务器 SimplePie 包中有一个兼容性测试，它会告诉你你的服务器是否可以运行。
*   如果你发推文，[使用 Alison Gianotto 的这个功能](http://www.snipe.net/2009/09/php-twitter-clickable-links/)来帮助自动链接这些推文
*   当然，你需要你可信赖的文本编辑器！
*   为了简洁起见，本教程中排除了一些代码。如果你想看到完整的图片，[查看完整的例子](https://www.sitepoint.com/examples/lifestream/lifestream.phps)或[下载。](https://www.sitepoint.com/examples/lifestream/lifestream.phps.zip)

## 我们开始吧

当然，首先我们需要确定我们想要在页面上包含的提要。就像我说的，我在很多地方都有个人资料，但现在我会保持简短和甜蜜——我会抓住我的博客、 [Twitter](http://twitter.com/) 、 [Flickr](http://flickr.com/) 照片，以及 [Last.fm.](http://last.fm/) 收藏的歌曲

您需要收集您想要的提要的 URL，并将它们存放在安全的地方。要做到这一点，请在页面上或浏览器的地址栏中寻找 RSS 图标。将这些 URL 粘贴到文本文件中，供以后参考。

接下来，我们需要检查 SimplePie 是否与我们的托管环境兼容。这个下载中有很多东西——现在，我们可以将`compatibility_test`目录上传到服务器上一个可通过网络访问的位置，并在网络浏览器中查看它。希望会有一系列消息通知您许多依赖项已经启用。如果是这样，是时候上传剩下的库了。(如果您的下载中有一个名为`test`的文件夹，那么排除它是安全的。)

当您浏览 web 服务器时，现在是为 SimplePie 设置一个目录作为缓存的好时机。您应该使这个目录可写。我选择了`cache`。

记下您上传 SimplePie 的位置，以及您的缓存目录。在我的例子中，两者都在我计划放置我的 lifestream 页面的同一个目录中。

## 准备你的饲料

让我们抓住那些饲料！打开一个新的文本编辑器，开始一个新的 PHP 文件，我们称之为`lifestream.php`。这将是最终展示我们生命之流的一页。

首先，让我们包括一个非常重要的组件 SimplePie 库本身:

```
<?php
  require_once('./simplepie/simplepie.inc');
```

现在，我们需要定义几个重要的变量:第一个是定义缓存位置，第二个是指定我们希望缓存提要的时间(以秒为单位)。反复点击提要是不礼貌的，一些提要提供者会因为你过多的提要请求而禁止你。此外，如果你有一个繁忙的网站，保持低资源消耗是明智的。我选择了 30 分钟，或 1800 秒，这是新鲜感和礼貌之间的一个很好的权衡。这些变量如下:

```
$cache = "./cache";
$duration = 1800;
```

接下来，是时候创建一些`SimplePie`对象了。将提要转换成`SimplePie`对象的最简单的方法是[以其简短形式调用它:](http://simplepie.org/wiki/reference/simplepie/start)向它传递提要 URL、缓存目录的位置和缓存持续时间。我所有的对象看起来大致都是这样的:

```
$object = new SimplePie(
 "http://example.com/feed.xml",
  $cache, $duration);
```

你会看到我在传递那些`$cache`和`$duration`变量以避免重复。为你的每一个提要创建对象——我的叫做`$blog`、`$twitter`、`$flickr`和`$lastfm`。

在这一阶段，由于我将包含推文，我将粘贴艾莉森·吉安诺托的`[twitterify](http://www.snipe.net/2009/09/php-twitter-clickable-links/)` [功能。](http://www.snipe.net/2009/09/php-twitter-clickable-links/)该函数只接受一些文本，并寻找 Twitter 中可点击的项目——带有@的用户名、带有#的标签和常规 URL 的引用——并将它们包装在漂亮的超链接中。该函数插入具有`target`属性的超链接；我已经把这些从我的例子中去掉了。

## 创建一个外壳

涨价时间到了！在 PHP 脚本的结尾`?>`下面创建一个 HTML shell。在这个例子中，为了简洁起见，我只显示了`body`元素的内容，当然我还包括了一个`!DOCTYPE`、`head`和其他必需品:

```
<h1>My awesome lifestream</h1>

<div id="blog">
  <h2>My blog</h2>
  <ul>

  </ul>
</div>
<div id="twitter">
  <h2>My tweets</h2>
  <ul>

  </ul>
</div>
<div id="flickr">
  <h2>My pictures</h2>
  <ul>

  </ul>
</div>
<div id="lastfm">
  <h2>My tunes</h2>
  <ul>

  </ul>
</div>
```

## 展示物品

现在我们有了一个文档的外壳，是时候开始向这个页面添加更多的 PHP 来循环遍历每个条目，并回显每个条目的相关部分——标题、描述、附件、链接等等——并在它们周围包装适当的标记。要找出可用的内容，请检查提要的来源，并查看 SimplePie 文档，特别是以`get_`开头的内容。[你可以在这里找到一个列表。](http://simplepie.org/wiki/reference/simplepie_item/start#g)

想到我自己的四个 feeds，每一组项目应该都略有不同。在我的博客文章中，我希望包含标题、描述、日期和超链接。在 Twitter RSS 提要中，标题和描述是相同的，所以我只使用标题。对于 Flickr 项目，我只想显示图片。Last.fm 上的曲目只需要标题、链接和日期。所以，我需要创建四个独立的循环。

这里是一个循环，用来吐出我的博客条目，我把它们放在了`div#blog`的无序列表中。这个相当简单:

```
<?php foreach ($blog->get_items()
  as $item) : ?>
<li>
  <h3>
    <a href="<?php
      echo $item->get_link(); ?>">
      <?php echo $item->get_title(); ?>
    </a>
  </h3>
  <p>
    <?php echo $item->get_description(); ?>
  </p>
  <p class="date">
    <a href="<?php
      echo $item->get_link(); ?>">
      <?php echo $item->get_date(); ?>
    </a>
  </p>
</li>
<?php endforeach; ?>
```

这个循环应该为我的提要中的每个博客条目生成一系列列表项，如下所示:

```
<li>
  <h3>
    <a href="http://example.com/blogentry/">
      Title
    </a>
  </h3>
  <p>Blog entry description text</p>
  <p class="date">
    <a href="http://example.com/blogentry/">
      01 January 2010, 12:00 am
    </a>
  </p>
</li>
```

太容易了！

在检查我的 Twitter feed 时，我注意到每条 tweet 都以我的名字、冒号和空格为前缀。在这种情况下，我想删除该文本。我还想把这些标签、用户名引用和 URL 转换成可点击的超链接。在这个循环中，当我回显 feed 项的`title`时，我会将`$item->get_title()`包装在一个`str_replace`语句中，然后将包装在`twitterify`函数中的*:*

```
<?php foreach ($twitter->get_items()
  as $item) : ?>
<li>
  <p>
    <?php echo twitterify(
      str_replace(
        "raena: ", "", $item->get_title()
      )
    ); ?>
  </p>
  <p class="date">
    <a href="<?php
        echo $item->get_link(); ?>">
      <?php echo $item->get_date(); ?>
    </a>
  </p>
</li>
<?php endforeach; ?>
```

Flickr feeds 的描述对我来说并不理想:它们包括文本“*用户名*发布了一张照片”，由照片页面链接包围的中等大小的图片，以及 Flickr 照片的描述。然而，我只想呈现图像和超链接的最小版本，并使用图像的标题作为`alt`文本。幸运的是，Flickr 以附件的形式提供了图片的 URL，所以我将使用附件。

Flickr 的外壳仍然是中等大小，但我想要可爱的方形缩略图。这就像用`_s.jpg`替换任何对`_m.jpg`的引用一样简单。

```
<?php foreach ($flickr->get_items()
  as $item) : ?>
  <li class="item">
    <a href="<?php echo $item->get_link(); ?>">
    <?php foreach ($item->get_enclosures()
      as $enclosure) : ?>
      <img src="<?php echo str_replace(
        "_m.jpg","_s.jpg",
          $enclosure->get_link()
      ); ?>"
        alt="<?php
          echo $item->get_title(); ?>" />
      <?php endforeach; ?>
    </a>
  </li>
<?php endforeach; ?>
```

最后，我只需要 Last.fm 中我喜欢的曲目列表中的标题和日期:

```
<?php foreach ($lastfm->get_items()
  as $item) : ?>
  <li class="item">
  <p>
    <a href="<?php
      echo $item->get_link(); ?>">
      <?php echo $item->get_title(); ?>
    </a>
  </p>
  <p class="date">
    <?php echo $item->get_date(); ?>
  </p>
   </li>
 <?php endforeach; ?>
```

您将在代码下载中找到我的完整示例`lifestream.phps`，或者再一次，[在这里查看完整示例。](https://www.sitepoint.com/examples/lifestream/lifestream.phps)如果你打算把它作为你自己工作的基础，记得把占位符提要 URL 改成你自己的！

当你完成了对你的项目的微调，是时候把它上传到网络服务器，并在你的浏览器中查看。如果一切顺利，您应该会看到一系列无序列表，每个列表都包含来自这些 RSS 提要的不同内容。

现在剩下的就是设计页面风格，并添加你认为必要的任何其他有趣的东西。在这个例子中，你会发现一组非常简单的样式:随意使用它们作为你自己的基础。

## 还有更多…

当然，除了我们今天看到的这个非常简单的例子，SimplePie 还有更多的内容。深入 wiki 看看有什么可能——有对附件显示的大量支持，将多个 feeds 合并成一个巨大流的能力，以及向项目添加社交媒体共享按钮的简单方法。[你会在维基上找到一些 SimplePie 驱动的网站的好例子。](http://simplepie.org/wiki/ideas/start)尽情享受！

## 分享这篇文章