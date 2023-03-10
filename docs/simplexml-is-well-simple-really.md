# SimpleXML 非常简单

> 原文：<https://www.sitepoint.com/simplexml-is-well-simple-really/>

我之前已经提到过 SimpleXML，但是需要指出的是，T2·斯特林的 T3 在这方面做得非常出色。如果你觉得 [XML 太难](http://www.tbray.org/ongoing/When/200x/2003/03/16/XML-Prog)，SimpleXML 就是答案。

Zend 在这里有一篇新文章，对 DOM 和 SimpleXML 进行了很好的比较。

SimpleXML 不仅简单，Sterling 还为 XML 名称空间提出了一个简洁的解决方案(这是 PHP4 实际上没有直接支持的),并且还添加了 XPath 支持，允许您关注 XML 文档的某个部分并对其进行“SimpleXML ”(例如，参见[这里的](http://www.php.net/manual/en/function.simplexml-element-xpath.php))。

另一个很酷的特性是 [simplexml_import_dom()](http://www.php.net/manual/en/function.simplexml-import-dom.php) 函数，它允许您获取任何 [DOM 节点](http://www.php.net/domxml)并获得它的 simplexml 表示。这为您提供了 DOM(作为 XPath 的替代)遍历 XML 文档的能力，但是 SimpleXML 却很容易找到您想要的内容。类似于…

 `load('somefile.xml');`

 `$ nodeList = $ doc-> get _ elements _ by _ tagname(' sometag ')；

foreach($ nodelist as $ node){
$ simple _ node = SimpleXML _ import _ DOM($ node)；

//在这里做简单的事情` 

`}
?>`

顺便说一句，我需要纠正的一点是，当谈到 [XMLReader](https://www.sitepoint.com/blog/) 时，我说 PHP 没有针对 XML 的“光标风格”API 这实际上是不正确的，因为您可以将 XPath 语句应用于 DOM 扩展和现在的 SimpleXML，以及已经加载的文档。

总之，我的猜测是，PHP5 可能不会“出售”新的对象模型，而是很酷的新扩展，比如 SimpleXML、 [Tidy HTML](http://www.php.net/tidy) 、 [SQLite](http://www.php.net/sqlite) 、 [SOAP](http://www.php.net/soap) 等等。行动比物体更响亮…

## 分享这篇文章