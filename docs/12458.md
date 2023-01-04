# Techy Treasures #4:美元函数的内部是什么？

> 原文：<https://www.sitepoint.com/techy-treasures-4-whats-inside-a-dollar-function/>

今天的 Techy Treasure 是我的另一个基本主食，一些简单的东西，但我几乎在我写的每个脚本中都使用它。这是一种检索元素引用的方法——可以是单个元素，也可以是由标记名引用的元素集合，还可以根据属性匹配进行筛选。

大多数 JavaScript 库都有这样一个函数，通常称之为`$`(美元)。虽然它们的工作方式略有不同，但它们做的基本上是一样的。因此，如果您是 JavaScript 新手，并且一直想知道这些无处不在的美元函数的内部是什么，这里有一点解剖供您参考！

和我在本专栏中写过的所有方法一样，我在这里将它展示为一个全局函数，以使示例更加简单明了；但是在实践中，你最好把它定义为一个自定义对象的方法:

```
function get(find, context, conditions)
{
	if(find.indexOf('#') != -1)
	{
		return document.getElementById(find.split('#')[1]);
	}

	else 
	{
		if(typeof context == 'undefined') 
		{ 
			context = document; 
		}

		var nodes = [], tmp = context.getElementsByTagName(find);
		for(var i=0; i<tmp.length; i++)
		{
			nodes.push(tmp[i]);
		}

		if(typeof conditions == 'undefined')
		{
			return nodes;
		}

		var filtered = [];
		for(i=0; i<nodes.length; i++)
		{
			var add = true;
			for(var c in conditions)
			{
				if(!conditions.hasOwnProperty(c)) { continue; }

				var attr = c == 'class' 
					? nodes[i].className : nodes[i].getAttribute(c);

				if(attr == null 
					|| attr == '' 
					|| new RegExp('(' + conditions

 `+ ')', '').test(attr) == false)

				{

					add = false;

				}

			}

			if(add == true)

			{

				filtered.push(nodes[i]);

			}

		}

		return filtered;

	}

}`

方法做了三件事。首先，它可以检索单个元素，这是`getElementById`的简写:

```
var content = get('#content');
```

其次，它可以检索元素的集合，这是`getElementsByTagName`的简写:

```
var paragraphs = get('p');
```

但是第三，也是我认为最有趣的一点，它可以根据属性匹配过滤元素集合。这个特性相当于一个`getElementsByAttributeMatch()`方法，可以在一个表达式中匹配多个属性:

```
var codeblocks = get('code', document, { 
	'class':'javascript' 
	});
```

第三个例子是检索类名为“javascript”的`<code>`元素的集合；或者更准确地说，元素的属性`class`的值*包含*字符串“javascript”。

事实上，每个键/值对的值参数都被计算为正则表达式，所以您可以这样做:

```
var codeblocks = get('code', document, { 
	'class':'j(ava)?script|php' 
	});
```

您会注意到在这些例子中，condition 对象之前有第二个参数，它定义了搜索的上下文。您可以传入一个不同的文档引用来检索该上下文中的集合(比如一个`iframe`中的页面)，或者您可以传入一个元素引用来检索一个特定元素中的子元素集合(比如一个列表中的所有列表项)。

最后，这个方法返回什么也取决于你要求它做什么。对于单个元素，它将返回该元素或`null`；对于元素集合(无论是否通过属性条件过滤)，它将返回元素的*数组*(一个真正的 JavaScript 数组，而不是 <abbr title="Document Object Model">DOM</abbr> 集合)，如果没有找到匹配的元素，这个数组将为空。 
```

## `分享这篇文章`