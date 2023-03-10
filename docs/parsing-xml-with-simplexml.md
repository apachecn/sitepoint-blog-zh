# 用 SimpleXML 解析 XML

> 原文：<https://www.sitepoint.com/parsing-xml-with-simplexml/>

解析 XML 本质上意味着浏览 XML 文档并返回相关数据。越来越多的 web 服务返回 JSON 格式的数据，但仍有大量返回 XML，所以如果您真的想使用所有可用的 API，您需要掌握 XML 解析。

使用 PHP 5.0 中引入的 SimpleXML 扩展，处理 XML 非常容易。在这篇文章中，我将向您展示如何。

## 基本用法

让我们以下面的例子作为`languages.xml`开始:

```
<?xml version="1.0" encoding="utf-8"?>
<languages>
 <lang name="C">
  <appeared>1972</appeared>
  <creator>Dennis Ritchie</creator>
 </lang>
 <lang name="PHP">
  <appeared>1995</appeared>
  <creator>Rasmus Lerdorf</creator>
 </lang>
 <lang name="Java">
  <appeared>1995</appeared>
  <creator>James Gosling</creator>
 </lang>
</languages>
```

上面的 XML 文档对一系列编程语言进行了编码，给出了每种语言的两个细节:实现年份和创建者姓名。

第一步是使用`simplexml_load_file()`或`simplexml_load_string()`加载 XML。如您所料，前者将把 XML 文件加载到一个文件中，而后者将从给定的字符串中加载 XML。

```
<?php
$languages = simplexml_load_file("languages.xml");
```

这两个函数将整个 DOM 树读入内存，并返回它的一个`SimpleXMLElement`对象表示。在上面的例子中，对象被存储到$ `languages`变量中。然后，如果您愿意，您可以使用`var_dump()`或`print_r()`来获取返回对象的详细信息。

```
SimpleXMLElement Object
(
    [lang] => Array
        (
            [0] => SimpleXMLElement Object
                (
                    [@attributes] => Array
                        (
                            [name] => C
                        )
                    [appeared] => 1972
                    [creator] => Dennis Ritchie
                )
            [1] => SimpleXMLElement Object
                (
                    [@attributes] => Array
                        (
                            [name] => PHP
                        )
                    [appeared] => 1995
                    [creator] => Rasmus Lerdorf
                )
            [2] => SimpleXMLElement Object
                (
                    [@attributes] => Array
                        (
                            [name] => Java
                        )
                    [appeared] => 1995
                    [creator] => James Gosling
                )
        )
)
```

XML 包含一个根`language`元素，它包装了三个`lang`元素，这就是为什么`SimpleXMLElement`有公共属性`lang`，它是三个`SimpleXMLElements`的数组。数组的每个元素对应于 XML 文档中的一个`lang`元素。

您可以使用`->`操作符以通常的方式访问对象的属性。例如，`$languages->lang[0]`会给你一个对应于第一个`lang`元素的`SimpleXMLElement`对象。这个对象有两个公共属性:`appeared`和`creator`。

```
<?php
$languages->lang[0]->appeared;
$languages->lang[0]->creator;
```

使用标准的循环方法，例如`foreach`，可以非常容易地遍历语言列表并显示它们的细节。

```
<?php
foreach ($languages->lang as $lang) {
    printf(
        "<p>%s appeared in %d and was created by %s.</p>",
        $lang["name"],
        $lang->appeared,
        $lang->creator
    );
}
```

注意，我访问了`lang`元素的 name 属性来检索语言的名称。您可以使用这样的数组表示法访问表示为`SimpleXMLElement`对象的元素的任何属性。

## 处理名称空间

在处理来自不同 web 服务的 XML 时，您会经常遇到命名空间元素。让我们修改我们的`languages.xml`例子来反映名称空间的用法:

```
<?xml version="1.0" encoding="utf-8"?>
<languages
 xmlns:dc="http://purl.org/dc/elements/1.1/">
 <lang name="C">
  <appeared>1972</appeared>
  <dc:creator>Dennis Ritchie</dc:creator>
 </lang>
 <lang name="PHP">
  <appeared>1995</appeared>
  <dc:creator>Rasmus Lerdorf</dc:creator>
 </lang>
 <lang name="Java">
  <appeared>1995</appeared>
  <dc:creator>James Gosling</dc:creator>
 </lang>
</languages>
```

现在，`creator`元素被放在名称空间`dc`下，该名称空间指向 http://purl.org/dc/elements/1.1/的*。如果你试图用我们以前的技术来打印一种语言的创造者，那是行不通的。为了像这样读取命名空间的元素，你需要使用下面的方法之一。*

 *第一种方法是在访问命名空间元素时，直接在代码中使用命名空间 URI。以下示例演示了如何:

```
<?php
$dc = $languages->lang[1]- >children("http://purl.org/dc/elements/1.1/");
echo $dc->creator;
```

`children()`方法接受一个名称空间，并返回以它为前缀的元素的子元素。它接受两个参数；第一个是 XML 名称空间，第二个是可选的布尔值，默认为 false。如果传递 true，名称空间将被视为前缀，而不是实际的名称空间 URI。

第二种方法是从文档中读取命名空间 URI，并在访问命名空间元素时使用它。这实际上是一种更干净的访问元素的方式，因为您不必硬编码 URI。

```
<?php
$namespaces = $languages->getNamespaces(true);
$dc = $languages->lang[1]->children($namespaces["dc"]);

echo $dc->creator;
```

`getNamespaces()`方法返回一个名称空间前缀及其相关 URIs 的数组。它接受一个可选参数，默认为 false。如果设置为 true，那么该方法将返回父节点和子节点中使用的名称空间。否则，它只查找父节点中使用的名称空间。

现在，您可以像这样遍历语言列表:

```
<?php
$languages = simplexml_load_file("languages.xml");
$ns = $languages->getNamespaces(true);

foreach($languages->lang as $lang) {
    $dc = $lang->children($ns["dc"]);
    printf(
        "<p>%s appeared in %d and was created by %s.</p>",
        $lang["name"],
        $lang->appeared,
        $dc->creator
    );
}
```

## 一个实际例子——解析 YouTube 视频源

让我们来看一个例子，它从 YouTube 频道中检索 RSS 提要，并显示所有视频的链接。为此，我们需要调用以下 URL:

*http://gdata.youtube.com/feeds/api/users//uploads*

URL 以 XML 格式返回给定频道的最新视频列表。我们将解析 XML 并获得每个视频的以下信息:

*   视频 URL
*   极小的
*   标题

我们将从检索和加载 XML 开始:

```
<?php
$channel = "channelName";
$url = "http://gdata.youtube.com/feeds/api/users/".$channel."/uploads";
$xml = file_get_contents($url);

$feed = simplexml_load_string($xml);
$ns=$feed->getNameSpaces(true);
```

如果您看一下 XML 提要，您会发现有几个`entity`元素，每个元素都存储了来自该频道的特定视频的详细信息。但是我们只关心缩略图、视频 URL 和标题。这三个元素是`group`的子元素，T1 是`entry`的子元素:

```
<entry>
   …
   <media:group>
      …
      <media:player url="video url"/>
      <media:thumbnail url="video url" height="height" width="width"/>
      <media:title type="plain">Title…</media:title>
      …
   </media:group>
   …
</entry>
```

我们简单地遍历所有的`entry`元素，对于每一个元素，我们都可以提取相关的信息。注意，`player`、`thumbnail`和`title`都在`media`名称空间下。因此，我们需要像前面的例子一样继续。我们从文档中获取名称空间，并在访问元素时使用名称空间。

```
<?php
foreach ($feed->entry as $entry) {
	$group=$entry->children($ns["media"]);
	$group=$group->group;
	$thumbnail_attrs=$group->thumbnail[1]->attributes();
	$image=$thumbnail_attrs["url"];
	$player=$group->player->attributes();
	$link=$player["url"];
	$title=$group->title;
	printf('<p><a href="%s"><img src="%s" alt="%s"></a></p>',
	        $player, $image, $title);
}
```

## 结论

现在您已经知道如何使用 SimpleXML 解析 XML 数据，您可以通过解析来自各种 API 的不同 XML 提要来提高自己的技能。但是需要考虑的重要一点是，SimpleXML 将整个 DOM 读入内存，所以如果您正在解析大型数据集，那么您可能会面临内存问题。在这些情况下，建议使用 SimpleXML 之外的东西，最好是基于事件的解析器，比如 XML 解析器。要了解关于 SimpleXML 的更多信息，请查看它的文档。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start PHP](https://learnable.com/books/jump-start-php?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章*