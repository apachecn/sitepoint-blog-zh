# 用 PHP DOM 将 XML 转换成文本

> 原文：<https://www.sitepoint.com/mangling-xml-as-text-with-php-dom/>

最近，我不得不将 <abbr title="HyperText Markup Language">HTML</abbr> 文件批量转换为[DITA XML](http://dita.xml.org/)——这是我为即将发布的 JavaScript Ultimate Reference 编写的材料(第三个，也可以说是最复杂的，是 [SitePoint Reference](https://reference.sitepoint.com/) 的一部分)。

但是我遇到过几次的一个问题是递归元素转换的复杂性——从`<code>`变成`<jsvalue>`(或者十几个相似元素中的一个)，`<a>`变成`<xref>` …这就足够简单了；但是这些元素中的每一个都可能包含另一个，或者像`<em>`这样的进一步的子元素，当我们穿过 <abbr title="Document Object Model">DOM</abbr> 时，潜在递归的发生率就会增加，直到我的大脑爆炸。

我能理解多少递归是有限度的——或者更确切地说——在我离开之前我准备了多少递归是有限度的<q>见鬼，为什么我不能用正则表达式把它分解成文本呢！？</q>

不幸的是，似乎没有办法用 <abbr title="HyperText Pre-Processor">PHP</abbr> <abbr title="Document Object Model">DOM</abbr> 来获得任意节点的文本等价物，但是我们可以在`Document`或`DocumentFragment`级别做到这一点；所以，经过一点思考，我想出了一个利用这种能力的方法，并让它在`Node`级别上工作。

例如，让我们从这个 <abbr title="eXtensible Markup Language">XML</abbr> 开始:

```
<?xml version="1.0" encoding="utf-8"?>
<root id="introduction">
	<div class="section">
		The fundamental data type is <code>Node</code>
	</div>
</root>
```

我们引用了保存在名为`$xmldom`的 <abbr title="HyperText Pre-Processor">PHP</abbr> 变量中的 <abbr title="Document Object Model">DOM</abbr> 。我们希望解析它，使`<code>`元素变成`<jstype>`，而`<div class="section">`变成简单的`<section>`，所有这些都不影响文档的其余部分。

下面是完成这项工作的完整代码，我将一步一步地讲解:

```
$node = $xmldom->documentElement->firstChild;

$doc = new DOMDocument();
$doc->loadXML('<xmltext/>');
$node = $doc->importNode($node, true);
$doc->documentElement->appendChild($node);
$xmltext = ereg_replace('^.*<xmltext>(.*)</xmltext>.*$', '\1', $doc->saveXML());

$xmltext = ereg_replace('<([/]?)code>', '<\1jstype>', $xmltext);
$xmltext = ereg_replace('<([/]?)div[^>]*>', '<\1section>', $xmltext);

$node = $xmldom->createDocumentFragment();
$node->appendXML($xmltext);

$xmldom->documentElement->replaceChild($node, $xmldom->documentElement->firstChild);
```

在第一步中，我们获取一个对我们想要处理的元素的引用，并将其保存到`$node`。

在第二步中，我们创建一个新文档，并使用`loadXML()`创建一个占位符根节点( [`loadXML`方法](http://au.php.net/manual/en/domdocument.loadxml.php)将文本输入转换为 <abbr title="eXtensible Markup Language">XML</abbr> ，并且是我们过程的基石之一)。接下来，我们将原始节点导入到该文档中，然后使用`saveXML()`将整个文档转换为文本(方法[`saveXML`](http://au.php.net/manual/en/domdocument.savexml.php)将 XML 文档转换为文本，对于我们这里所做的事情来说，它和`loadXML()`一样重要)。使用 [`ereg_replace`](http://au.php.net/manual/en/function.ereg-replace.php) 解析文本输出，删除文档的外部内容(它的序言和根节点),这样我们得到的是原始输入节点的等效文本。

第三步，我们做任何我们需要的基于文本的篡改；在这种情况下，它是简单的元素名称转换，但它可以是任何东西。

在第四步中，我们希望将解析后的文本转换回 <abbr title="eXtensible Markup Language">XML</abbr> ，我们通过创建一个文档片段，然后使用`appendXML()`加载文本并将其转换成<abbr title="eXtensible Markup Language">XML</abbr>([`appendXML`方法](http://au.php.net/manual/en/domdocumentfragment.appendxml.php)与`loadXML()`做同样的事情，但它不需要创建整个文档)。

最后，在第五步中，我们将处理过的 <abbr title="eXtensible Markup Language">XML</abbr> 合并回我们的原始文档。文档片段以原始文档为其所有者，因此我们可以简单地使用 [`replaceChild`](http://au.php.net/manual/en/domnode.replacechild.php) 方法将原始节点及其子节点替换为处理后的版本。(每当一个文档片段被添加到一个文档时，实际上只添加了它的子文档，文档片段本身被丢弃；`DocumentFragment`是一个虚拟的构造，实际上从未出现在文档中。)

第一步和最后一步都是任意的——我们可以处理整个文档，或者只是单个节点，并相应地编辑我们的引用和合并语句。或者我们可以从内部步骤构建一个方法，该方法接受`$node`作为参数(也可能是替换表达式的数组)，并在最后返回处理过的节点:

```
function mangleXML($node)
{
	...

	return $node;
}
```

## 分享这篇文章