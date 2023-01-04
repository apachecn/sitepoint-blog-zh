# PHP MapReduce

> 原文：<https://www.sitepoint.com/php-mapreduce/>

所以，在我最初[担心](https://www.sitepoint.com/oh-dear/)[这个](http://shiflett.org/archive/269)的影响之后，我终于明白了——谷歌试图告诉我们——我们已经有了一个[巨大的集群](http://en.wikipedia.org/wiki/MapReduce)在我们的支配之下！

所以花了一个晚上一起黑掉了[PHP MapReduce](http://sourceforge.net/projects/phpmapreduce)—*主*节点，你在你的服务器上运行，使用[这个搜索](http://google.com/codesearch?hl=en&lr=&q=lang%3Aphp+%28include%7Crequire%29%5Cs*%28%5C%28%7C%5Cs%29.*%5C%24_%28GET%7CPOST%7CCOOKIE%7CREQUEST%29&btnG=Search)来定位受害者……呃……工人参与集群。然后编写一些代码，如:

```
 <?php
require_once 'mapreduce.php';

$veryLargeFile = '/tmp/bigfile';
$map = 'http://masternode.com/mapreduce/wordcounter.phps';
$reduce = 'http://masternode.com/mapreduce/adder.phps';

# Massively distributed computing, here we come...
$result = MapReduce($veryLargeFile, $map, $reduce); 

```

目前，它仅限于在 workers 上执行 PHP，所以这是一个规模相当有限的集群。但是正在努力扩展它，以便你的 map 和 reduce 函数被自动翻译成 MySQL 存储过程，允许[这个搜索](http://google.com/codesearch?hl=en&lr=&q=lang%3Aphp+query%5C%28.*%5C%24_%28GET%7CPOST%7CCOOKIE%7CREQUEST%29.*%5C%29&btnG=Search)显著扩展集群(感谢[伊利亚](http://ilia.ws/archives/133-Google-Code-Search-Hackers-best-friend.html#extended))。在 [adodb](http://adodb.sourceforge.net/) 的帮助下，我认为让这个 db 独立应该是可能的。

但是真正有趣的是考虑到 T2 的搜索。现在这很难实现，但是邀请*浏览器*加入集群也是可能的，这极大地提高了你的处理能力。工作流类似于 master =>worker server =>worker browser =>(通过 AJAX 返回)= > work server = > master。

我们已经进入了真正的分布式计算时代。想想我们可以用它做些什么，比如有史以来最大的博客垃圾过滤器！

## 这是个玩笑 btw！

…而且可能是个坏消息。现在不是四月，但无论如何。我不是在做这件事。我永远也不会。

我认为这可能是一个好主意，谷歌允许人们将搜索限制在一个单一的领域，这样人们至少可以在自己的网站上看到他们的内容，并根据需要进行清理。

## 分享这篇文章