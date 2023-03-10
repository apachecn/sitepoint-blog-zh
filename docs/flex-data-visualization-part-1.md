# 使用 Flex 实现数据可视化，第一部分

> 原文：<https://www.sitepoint.com/flex-data-visualization-part-1/>

在一个越来越受数据驱动的世界，很容易发现自己被信息淹没。我们拥有的越多，它就变得越难理解，直到最终它都只是数字。数据可视化是呈现可用信息的艺术:帮助查看者一眼就能辨别趋势、模式和相关事实，而不需要统计学学位。

在这一系列教程中，我们将了解如何使用 Flex 平台来可视化您的数据，并使其对您的受众既清晰又有吸引力。本系列假定读者对 Flex 和 Flex Builder 环境有基本的了解。

在我们开始之前，您应该[下载示例项目](http://sitepoint-examples.s3.amazonaws.com/flexdataviz/SitePoint_DataViz_Tutorial_part1.zip)作为 Flex Builder 归档文件。

本周我们为您准备了另一个[文章测验](https://www.sitepoint.com/quiz/flex3/flex-data-visualization-part-1)—这次是由 Adobe 赞助的——所以一旦您通读了这篇文章，请务必进行自我测试！

## 为什么要可视化？

有效的可视化可以像条形图一样简单。例如，下面的数据集显示了某个网站每月的独特访问量:

**表 1。网站的独立访问量，按月细分**

<colgroup><col> <col></colgroup>
| 月 | 独特的访问 |
| --- | --- |
| 一月 | Twelve thousand two hundred and thirty-one |
| 二月（February 的缩写） | Eleven thousand nine hundred and eighty-nine |
| 瑕疵 | Twelve thousand one hundred and eight |
| 四月 | Thirteen thousand four hundred |
| 五月 | Thirteen thousand six hundred and twenty-six |
| 六月 | Fifteen thousand four hundred and fifty-five |
| 七月 | Twenty thousand seven hundred and forty-two |
| 八月 | Twenty-two thousand three hundred and forty-five |
| 九月 | Twenty-three thousand four hundred and twenty-five |
| 十月 | Twenty thousand one hundred and twenty-three |
| 十一月 | Eighteen thousand three hundred and thirty-two |
| 十二月 | Fourteen thousand two hundred and fifty-five |

这种观点相当枯燥，除非你仔细通读，否则没有什么真正吸引你的。然而，我们可以在图表中表示相同的数据点系列。

**图一。同样的数据，呈现为线形图**

![The same data, presented as a line graph](img/4cd068329f339b66f97fad68551215a0.png)

我们一眼就能看出流量有一个峰值——它在几个月内显著增加，然后逐渐减少。我们立即见证了数据可视化的好处:能够轻松发现趋势。现在，这本身可能没有意义，但如果我们将它与一些其他信息一起分析，如某个事件的新闻报道，或我们网站上发布的功能，我们可以将这两个事件联系在一起，确定我们当时正在做的任何事情都是增加流量的原因。当网站停止运行该功能或删除某项功能时，流量就会下降——所以我们知道这是吸引人们的内容或功能，我们应该做得更多。

即使在这个非常简单的例子中，我们也可以看到可视化的重要性。然而，当您开始查看更复杂的数据集时，这个值会显著增加。

## 提取数据

对于本教程，我们将使用来自 SitePoint 论坛的数据来分析用户帖子中某些技术关键词的使用频率，并尝试确定它们之间的任何关系。论坛已经存在了一段时间，非常受欢迎，所以我们有一个巨大的数据集可以处理。简单地通读它会花费很长时间，给我们提供很少或没有有用的信息，所以我们的首要任务是生成一个有用的数据集。

不过，在深入研究数据之前，最好考虑一下我们想要可视化的是什么。在这种情况下，我决定检查不同技术在论坛中讨论的频率，以及这些技术之间的关系。为了做到这一点，我将查看某些关键词被提及的次数，以及哪些其他关键词倾向于在这些相同的线索中被发现。

我已经编写了一个快速的 PHP 脚本，它从数据库中提取数据，并将其作为 JSON 对象写到一个文件中。生成的 JSON 文件的结构如下所示:

```
{  "keyword":"ajax",  "count":12479,  "links": {    "flash":441,    "php":1900,    "javascript":2304,    "flex":102,    "adobe":61,    "microsoft":123,    "asp":473,    ".net":180,    "dotnet":1,    "css":508,    "xml":1184,    "html":940,    "mysql":369,    "macromedia":18,    "jsp":29,    "ruby":160,    "xhtml":223,    "dhtml":114,    "coldfusion":76,    "air":16,    "postgres":14,    "actionscript":24,    "stylesheet":39,    "cgi":18,    "silverlight":15,    "groovy":2  }}
```

`count`表示关键字(在本例中为“flex”)在论坛中被提及的次数。`link`值是包含该关键字和每个其他关键字的线程数。例如，在上面的数据集中，我们可以看到单词“ajax”被使用了 12，479 次，有 2，304 个线程同时包含单词“ajax”和“javascript”

我将简要回顾一下 PHP 脚本，因为它超出了本文的重点。要认识到的重要一点是，我们正在从数据库中提取一些特定的数据，以传递给我们的 Flex 应用程序。您在这里使用的脚本类型会根据您想要可视化的数据类型而有所不同。

脚本的第一步是定义一个关键字数组，另一个数组保存我们在帖子中找到的频率和链接:

```
// populate an array of keywords to search for, with counts and link arrays$keys =  array('ajax','coldfusion','flash','flex','air','adobe','macromedia','microsoft','php','ruby','groovy','asp','.net','dotnet','actionscript','javascript','dhtml','jsp','cgi','css','stylesheet','silverlight','mysql','postgres','xhtml','xml','html');$keyFreqs = array();foreach ($keys as $key) {  $keyFreqs[$key] = array("count" => 0, "links" => array());}
```

然后，我们连接到数据库，遍历每个线程和线程中的每篇文章，扫描我们的关键字，并在进行过程中添加到我们的`$keyFreqs`数组中。最后，我们展平数组，使其更易于在 Flex 中访问，并将其保存为 JSON 格式的文件:

```
// Connect to the database$link = mysqli_connect('localhost', 'root', 'root');if (!mysqli_select_db($link, 'forum')) {  print 'Unable to select database';}// loop through the threads and their posts$threads = mysqli_query($link, 'SELECT threadid FROM thread ORDER BY dateline LIMIT 100000;');while ($thread = mysqli_fetch_array($threads)) {  $posts = mysqli_query($link, "SELECT postid, pagetext FROM post WHERE threadid=" . $row['threadid']);  while ($post = mysqli_fetch_array($posts)) {    $tempKeys = array();    // loop over the list of keywords    foreach ($keys as $key) {      // search for the key in the post      $pattern = "/ " . $key . "[^a-zA-Z0-9]*/i";      $tempCount = preg_match_all($pattern, $post['pagetext'], $matches);            //if we found one or more matches, add the key to a temporary list      if ($tempCount > 0) {        $tempKeys[] = $key;      }      // increment the global count for that keyword      $keyFreqs[$key]["count"] += $tempCount;    }    // loop over the list of keywords we found a count for     // and increment the link between them    foreach ($tempKeys as $a) {      foreach ($tempKeys as $b) {        if ($a != $b) {          if (isset($keyFreqs[$a]["links"][$b])) {            $keyFreqs[$a]["links"][$b] += 1;          } else {            $keyFreqs[$a]["links"][$b] = 0;          }        }      }    }  }}// Flatten the array and write it out to a file in JSON format$newArray = array();foreach($keyFreqs as $keyword => $value) {  $newArray[] = array("keyword" => $keyword, "count" => $value['count'], "links" => $value["links"]);}$fh = fopen('json_data.txt','w');fwrite($fh,json_encode($newArray));
```

## 分享这篇文章