# 处理不合格的 HREF 价值观

> 原文：<https://www.sitepoint.com/dealing-with-unqualified-href-values/>

当我为寻找未使用的 CSS 规则构建我的[扩展时，我需要一种将任何`href`值限定为完整的<abbr title="Uniform Resource Indicator">URI</abbr>的方法。我需要这样做，因为我希望它支持条件注释中的样式表，但当然对 Firefox 来说，这些只是注释——我必须用正则表达式解析每个注释节点，以提取其中的内容，因此，我得到的`href`值始终只是一个字符串，而不是属性或限定路径。](https://www.sitepoint.com/dustmeselectors/)

这不是我第一次需要这种能力，但在过去，我已经知道了域名和路径，这种情况是可以预见的。但是在这里，这些情况是不可预测的——我需要一个适用于任何域名、任何路径和任何类型的`href`格式的解决方案(记住，`href`值可以是几种格式中的任何一种):

*   相对:`"test.css"`
*   相对于目录:`"foo/test.css"`
*   从这里相对:`"./test.css"`
*   从更高的目录结构相对:`"../../foo/test.css"`
*   相对于 http 根目录:`"/test.css"`
*   绝对:`"https://www.sitepoint.com/test.css"`
*   绝对带端口:`"http://www.sitepoint.com:80/test.css"`
*   不同协议的绝对:`"https://www.sitepoint.com/test.css"`

## HREFs 什么时候有资格？

当我们用 JavaScript 检索一个`href`时，返回的值有一些跨浏览器的特点。最常见的情况是，用简写的`.href`属性检索的值将作为限定的 <abbr title="Uniform Resource Indicator">URI</abbr> 返回，而用`getAttribute('href')`检索的值将(根据规范，应该)作为文字属性值返回。所以有了这个链接:

```
<a id="testlink" href="/test.html">test page</a>
```

我们应该得到这些值:

```
document.getElementById('testlink').href == 'https://www.sitepoint.com/test.html';
document.getElementById('testlink').getAttribute('href') == '/test.html';
```

在 Opera、Firefox 和 Safari 中，这确实是我们得到的。然而，在 Internet Explorer(所有版本，直到并包括 <abbr title="Internet Explorer 7">IE7</abbr> )中，情况并非如此——对于这两个示例，我们得到的是完全限定的 <abbr title="Uniform Resource Indicator">URI</abbr> ，而不是原始属性值:

```
document.getElementById('testlink').href == 'https://www.sitepoint.com/test.html';
document.getElementById('testlink').getAttribute('href') == 'https://www.sitepoint.com/test.html';
```

凯文·杨克和卡梅隆·亚当斯在他们的新书《简单的 JavaScript 》中记录了这种行为怪癖；但是事情变得更奇怪了。尽管这种行为适用于常规链接的`href`(一个`<a>`元素)，但是如果我们对`<link>`样式表做同样的事情，我们会得到*与 <abbr title="Internet Explorer">IE</abbr> 中的*完全相反的行为。这个 <abbr title="HyperText Markup Language">HTML</abbr> :

```
<link rel="stylesheet" type="text/css" href="/test.css" />
```

产生以下结果:

```
document.getElementById('teststylesheet').href == '/test.css';
document.getElementById('teststylesheet').getAttribute('href') == '/test.css';
```

在这两种情况下，我们都获得了原始属性值(而在其他浏览器中，我们获得了与锚相同的结果— `.href`是完全限定的，而`getAttribute`产生了一个文字值)。

## 无论如何…

撇开行为怪癖不谈，我不得不说 <abbr title="Internet Explorer">IE</abbr> 的链接行为几乎总是我想要的。从一个 <abbr title="Uniform Resource Indicator">URI</abbr> 中导出一个路径或文件名是相当简单的，但是做相反的事情要复杂得多。

所以我写了一个助手函数来做这件事。它接受任何格式的`href`，并根据当前文档位置返回一个合格的 <abbr title="Uniform Resource Indicator">URI</abbr> (或者如果该值已经合格，则原样返回):

```
//qualify an HREF to form a complete URI
function qualifyHREF(href)
{
	//get the current document location object
	var loc = document.location;

	//build a base URI from the protocol plus host (which includes port if applicable)
	var uri = loc.protocol + '//' + loc.host;

	//if the input path is relative-from-here
	//just delete the ./ token to make it relative
	if(/^(./)([^/]?)/.test(href))
	{
		href = href.replace(/^(./)([^/]?)/, '$2');
	}

	//if the input href is already qualified, copy it unchanged
	if(/^([a-z]+):///.test(href))
	{
		uri = href;
	}

	//or if the input href begins with a leading slash, then it's base relative
	//so just add the input href to the base URI
	else if(href.substr(0, 1) == '/')
	{
		uri += href;
	}

	//or if it's an up-reference we need to compute the path
	else if(/^((../)+)([^/].*$)/.test(href))
	{
		//get the last part of the path, minus up-references
		var lastpath = href.match(/^((../)+)([^/].*$)/);
		lastpath = lastpath[lastpath.length - 1];

		//count the number of up-references
		var references = href.split('../').length - 1;

		//get the path parts and delete the last one (this page or directory)
		var parts = loc.pathname.split('/');
		parts = parts.splice(0, parts.length - 1);

		//for each of the up-references, delete the last part of the path
		for(var i=0; i<references; i++)
		{
			parts = parts.splice(0, parts.length - 1);
		}

		//now rebuild the path
		var path = '';
		for(i=0; i<parts.length; i++)
		{
			if(parts[i] != '')
			{
				path += '/' + parts[i];
			}
		}
		path += '/';

		//and add the last part of the path
		path += lastpath;

		//then add the path and input href to the base URI
		uri += path;
	}

	//otherwise it's a relative path,
	else
	{
		//calculate the path to this directory
		path = '';
		parts = loc.pathname.split('/');
		parts = parts.splice(0, parts.length - 1);
		for(var i=0; i<parts.length; i++)
		{
			if(parts[i] != '')
			{
				path += '/' + parts[i];
			}
		}
		path += '/';

		//then add the path and input href to the base URI
		uri += path + href;
	}

	//return the final uri
	return uri;
}
```

工具箱再来一个！

## 分享这篇文章