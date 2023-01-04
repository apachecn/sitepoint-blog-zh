# 处理不合格的 HREF 值(第二部分)

> 原文：<https://www.sitepoint.com/dealing-with-unqualified-href-values-part-2/>

在我最初的博客文章[处理不合格的 HREF 值](https://www.sitepoint.com/dealing-with-unqualified-href-values)中，我提出了一种使用来自文档`location`对象的数据将任意格式的`href`值转换成完全合格的 <abbr title="Uniform Resource Locator">URL</abbr> 的方法。

然而，正如一位评论者指出的那样，提议的解决方案不能适应位置上下文中的*变化，比如由`<base>`元素引起的变化，或者在包含的文档(比如样式表或`<iframe>`中的页面)的上下文中的变化。*

为了解决这个问题，有必要完全放弃使用`location`对象，并将<abbr title="Uniform Resource Locators">的 URL</abbr>解析为字符串。但是这样做的结果是一个更加灵活和有用的方法，它可以自动适应`<base>`元素，并且能够接受一个输入位置字符串作为上下文。

所以从原始代码来看，这:

```
//get the current document location object
var loc = document.location;
```

变成了这样:

```
//get the current document location href
var here = document.location.href;

//look for a base element to use instead
var bases = document.getElementsByTagName('base');
if(bases.length > 0)
{
	var basehref = bases[0].getAttribute('href');
	if(basehref && basehref != '')
	{
		here = basehref;
	}
}

//if the context argument is present and non-empty string, use that instead
if(typeof context == 'string' && context != '')
{
	here = context;
}

//extract the protocol, host and path
//and create a location object with the data
var parts = here.replace('//', '/').split('/');
var loc = {
	'protocol' : parts[0],
	'host' : parts[1]
	}
parts.splice(0, 2);
loc.pathname = '/' + parts.join('/');
```

下面是修改后的完整函数:

```
//qualify an HREF to form a complete URI
function qualifyHREF(href, context)
{
	//get the current document location href
	var here = document.location.href;

	//look for a base element to use instead
	var bases = document.getElementsByTagName('base');
	if(bases.length > 0)
	{
		var basehref = bases[0].getAttribute('href');
		if(basehref && basehref != '')
		{
			here = basehref;
		}
	}

	//if the context argument is present and non-empty string, use that instead
	if(typeof context == 'string' && context != '')
	{
		here = context;
	}

	//extract the protocol, host and path
	//and create a location object with the data
	var parts = here.replace('//', '/').split('/');
	var loc = {
		'protocol' : parts[0],
		'host' : parts[1]
		}
	parts.splice(0, 2);
	loc.pathname = '/' + parts.join('/');

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

但是等等…还有更多！

这样做之后，我意识到我离实现 PHP 的 [parse_url](http://www.php.net/parse_url) 方法的 JavaScript 等价物只有一步之遥:

```
//parse a URL to form an object of properties
function parseURL(url)
{
	//save the unmodified url to href property
	//so that the object we get back contains
	//all the same properties as the built-in location object
	var loc = { 'href' : url };

	//split the URL by single-slashes to get the component parts
	var parts = url.replace('//', '/').split('/');

	//store the protocol and host
	loc.protocol = parts[0];
	loc.host = parts[1];

	//extract any port number from the host
	//from which we derive the port and hostname
	parts[1] = parts[1].split(':');
	loc.hostname = parts[1][0];
	loc.port = parts[1].length > 1 ? parts[1][1] : '';

	//splice and join the remainder to get the pathname
	parts.splice(0, 2);
	loc.pathname = '/' + parts.join('/');

	//extract any hash and remove from the pathname
	loc.pathname = loc.pathname.split('#');
	loc.hash = loc.pathname.length > 1 ? '#' + loc.pathname[1] : '';
	loc.pathname = loc.pathname[0];

	//extract any search query and remove from the pathname
	loc.pathname = loc.pathname.split('?');
	loc.search = loc.pathname.length > 1 ? '?' + loc.pathname[1] : '';
	loc.pathname = loc.pathname[0];

	//return the final object
	return loc;
}
```

这就是工具包的第二个*和第一个*！

## 分享这篇文章