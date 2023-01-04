# 用模板 IT 进行标记分离

> 原文：<https://www.sitepoint.com/markup-separation-with-template-it/>

我在进行 PHP 开发时遇到的更具挑战性的事情之一是有效地将表示与程序逻辑分开。在许多项目中，我觉得我做得很好，直到我不得不做一个标记更改并跨越许多障碍来实现它。在我最近的项目中，我已经开始在 PEAR 中使用 HTML 模板 IT 扩展，这让事情变得简单多了。

HTML 模板 IT 扩展提供了一个可靠的模板系统，允许代码和表示完全分离。标记包含在单独的模板文件中，而不是放在 PHP 文件中。在这些模板的标记中有一些变量，当模板被解析时，这些变量可以被动态数据替换。

识别替换变量很容易，设计人员编辑文件也很简单。只要引用还在，它们就会在模板被解析时被替换。这里的主要目标是提供一种改变 HTML 标记而不影响站点功能的方法。

下面是一个基本模板的示例:

```
<!-- BEGIN Article -->
<div>
	<h2>{ArticleTitle}</h2>
	{ArticleAuthor}
	<p>{ArticleBody}</p>
</div>
<!-- END Article -->
```

因此，现在您已经有了一个模板文件，您将需要使用 Template IT 扩展来解析它。如果你有自己的服务器，你必须使用 PEAR 来安装它。如果您在一个托管系统上，很可能已经安装了这个和其他常见的 PEAR 扩展。

一旦安装，扩展很容易使用。这个过程有 3 个步骤:加载模板、设置替换变量和解析模板。下面是一个如何解析上面例子中的模板的例子:

```
 <?php
	require_once "HTML/Template/IT.php"; //Require the extension
	//Instantiate a template object and set the template directory
	$tpl = new HTML_Template_IT("templates");
	//Load the template file
	//the boolean values tell the parser to remove empty or unknown items
	$tpl->loadTemplateFile("article.tpl", true, true);
	//Provide data for the replacement variables
	$tpl->setVariable("ArticleTitle",$title);
	$tpl->setVariable("ArticleAuthor",$author);
	$tpl->setVariable("ArticleBody",$body);
	//Parse template and print
	$tpl->show();
?>
```

上面的例子非常简单，但是模板系统提供了很大的灵活性来做更多的事情。在模板中使用 BEGIN 和 END 注释 HTML 允许在一个文件中定义多个块。你甚至可以有嵌套的模板。例如，如果模板包含选择列表，可以将选项模板块嵌套在模板中。该选项块可以根据需要进行多次解析，然后可以解析包含它的块。这将为您留下一个完整的页面，并允许根据需要多次重复各个部分。

还有另一个解析模板的选项。除了 show 方法之外，还有一个 get 方法，它以字符串的形式返回解析后的模板，而不是打印出来。您可以轻松地使用它将一个模板插入到另一个模板的替换变量中。这允许您使用一个模板来包含导航、页眉和页脚，并将解析后的模板作为内容插入其中。然后，设计者可以对主模板文件进行全局更改，而不会影响单个页面内容。

通过 PEAR 和其他来源，PHP 还可以使用其他几个模板系统。它是最简单的系统之一，也很容易进入，但是它提供的功能不如其他一些系统。如果你喜欢模板的想法，但需要更多的选项，可以看看 [Smarty](http://smarty.php.net/) 或 [Flexy。](http://pear.php.net/package/HTML_Template_Flexy)无论你是与设计者一起工作的开发人员，还是独自工作的开发人员，使用模板都会使维护代码变得更加容易。

## 分享这篇文章