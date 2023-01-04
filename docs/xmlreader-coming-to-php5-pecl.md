# XMLReader 即将登陆 PHP5 / PECL

> 原文：<https://www.sitepoint.com/xmlreader-coming-to-php5-pecl/>

Via [Christian](http://blog.bitflux.ch/p1687.html) ，看起来基于[libxml 2](http://xmlsoft.org/)(PHP5 背后的 XML 库)，PHP 5 将会有一个快速的 [XML Pull](http://www.xmlpull.org/) 实现。

最终这应该会通过 http://cvs.php.net/cvs.php/pecl/xmlreader/的 PECL 发布(我猜，在不久的将来，你不能指望你的共享主机支持这个)，但是现在源代码在网上。

本质上，这意味着一种比 SAX 更容易的解析 XML 的方式。 [Philip 的](https://www.sitepoint.com/blog/)文章[回归基础:XML In。网](https://www.sitepoint.com/article/dot-net-xml)显示。NET 的 XMLReader 在运行[在这里](https://www.sitepoint.com/dot-net-xml/)和它 PHP 它会非常相似。

基于对 PHP xmlreader 源代码的观察，使用它应该是这样的；

 `// Create the parser
$reader = new XMLReader();`

 `//在一个字符串中加载一些 XML
$ reader->XML($ XML)；

//循环遍历内容
while($ reader->read()){
switch($ reader->nodeType){

//标记开始
case XML reader _ ELEMENT:
echo“获得的开始标记”。读者- >姓名；
突围；` 

`// Tag start
case XMLREADER_END_ELEMENT:
echo 'Got closing tag for '.reader->name;
break;
}
}`

换句话说，与 SAX 回调不同，您可以在一个循环中处理整个文档——这对黑客来说非常好！

同样有趣的是，根据 xmlreader 的原始 [C 示例，它能够根据 DTD 或 Relax NG 模式验证 XML。现在，我相信最新的 DOM 扩展可以验证整个 XML 文档，并给出“是/否”的答案，但这似乎允许您验证文档中的单个节点。](http://xmlsoft.org/xmlreader.html)

根据对处理 XML 的 API 和技术的调查，有 5 种解析 XML 的常用技术。下面是它在 PHP5 中的样子

–Push:[SAX 扩展](http://www.php.net/xml)

–Pull:这个新的 xmlreader 扩展

–树:[DOM 扩展](http://www.php.net/domxml)

–对象映射:[简单 XML](http://www.php.net/simplexml)

–Cursor:我还不相信任何事

所有这些都是基于 PHP5 中的 libxml2 构建的。根据像 http://xmlbench.sourceforge.net/这样的基准测试，libxml2 或多或少是最快的 xml 解析器。

与 PHP4 中 XML 的状态相比，PHP5 看起来像是梦想成真了……在 PHP 4 中，开发人员已经用 SAX 做了[令人惊奇的事情](http://sourceforge.net/projects/phpxpath/)

## 分享这篇文章