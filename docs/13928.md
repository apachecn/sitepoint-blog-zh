# SimpleXML 和名称空间

> 原文：<https://www.sitepoint.com/simplexml-and-namespaces/>

在 SitePoint 最近出版的书 [<cite>中有很多关于</cite>](https://www.sitepoint.com/books/xml1/) [SimpleXML](http://www.php.net/manual/en/ref.simplexml.php) 的内容，这是 PHP5 用于访问 XML 文档内容的新 API，但有一点没有涉及，那就是如何将 SimpleXML 用于利用了 [XML 名称空间](https://www.w3.org/TR/xml-names11/)的文档。

以这个文档为例——一个简化的 RSS 1.0 提要:

```
 <?xml version="1.0" encoding="utf-8"?>
<rdf:RDF xmlns:rdf="https://www.w3.org/1999/02/22-rdf-syntax-ns#"
     xmlns:dc="http://purl.org/dc/elements/1.1/">
  <channel rdf:about="https://www.sitepoint.com/">
    <title>SitePoint.com</title>
    <link>https://www.sitepoint.com/</link>
    <description>SitePoint is the natural place to go to grow your online business.</description>
    <items>
      <rdf:Seq>
        <rdf:li rdf:resource="https://www.sitepoint.com/article/take-command-ajax" />
      </rdf:Seq>
    </items>
  </channel>
  <item rdf:about="https://www.sitepoint.com/article/take-command-ajax">
    <title>Take Command with AJAX</title>
    <link>https://www.sitepoint.com/article/take-command-ajax</link>
    <description>Want to get a bang out of your AJAX artillery?</description>
    <dc:date>2005-10-14T04:00:00Z</dc:date>
  </item>
</rdf:RDF> 
```

在 PHP5 中，您可能会想到使用 SimpleXML 的 API 来获取提要中每个`item`的`date`:

```
 $feed = simplexml_load_file('https://www.sitepoint.com/recent.rdf');
foreach ($feed->item as $item) {
  echo $item->date;
} 
```

但这是行不通的，因为`date`元素有一个名称空间前缀(`<*dc:*date>`)，所以不能通过通常的方式访问。

下面是解决方案。首先，检查名称空间的 URI 是什么。在这种情况下，`dc:`前缀映射到 URI `http://purl.org/dc/elements/1.1/`:

```
<rdf:RDF xmlns:rdf="https://www.w3.org/1999/02/22-rdf-syntax-ns#"
     xmlns:dc="http://purl.org/dc/elements/1.1/">
```

然后使用 SimpleXML 对象的 [`children`方法](http://www.php.net/manual/en/function.simplexml-element-children.php)，将它传递给 URI:

```
 $feed = simplexml_load_file('https://www.sitepoint.com/recent.rdf');
foreach ($feed->item as $item) {
  $ns_dc = $item->children('http://purl.org/dc/elements/1.1/');
  echo $ns_dc->date;
} 
```

当您将名称空间 URI 传递给`children`方法时，您将获得属于该名称空间的子元素的 SimpleXML 集合。您可以像处理任何 SimpleXML 集合一样处理该集合。

您可以以同样的方式使用 [`attributes`方法](http://www.php.net/manual/en/function.simplexml-element-attributes.php)来获得带有名称空间前缀的属性。

## 分享这篇文章