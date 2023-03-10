# jQuery 将文本转换成 HTML 列表–$。斯特灵托利斯特

> 原文：<https://www.sitepoint.com/jquery-convert-text-html-list-stringtolist/>

这是我写的一个 jQuery 实用函数，它简单地将文本(比如一个长字符串)转换成一个 HTML 列表。它有几个选择转换为有序列表(OL)或无序列表(UL)的设置。列表中每个项目的分隔符是句号。

### 以前

![text-to-list(1)](img/ddaffcff80f234e54c45e7e23f3ce659.png "text-to-list(1)")

### 在...之后

![text-to-list(2)](img/42a9bb074dba96040e06e365bd4caffd.png "text-to-list(2)")

## jQuery.stringToList()

```
/*
 * $.stringToList
 * jQuery Function to convert a block of text into a html list.
 * @requires: full stops after each sentence to match list elements
 * @param: list type: ul or ol
 * Usage: $('#inclusions').stringToList('ul');
 * Author: Sam Deering
 */
$.fn.extend(
{
	stringToList: function(listType)
	{
		var sentenceRegex = /[a-z0-9,'‘- ]+/igm, htmlList = '';
		$.each($(this).html().match(sentenceRegex), function(i, v)
		{
			/* Remove blank elements */
			if (v && (/[a-z0-9]+/igm.test(v)) && v != 'strong') 
			{
				htmlList += '*   + v + '';
			}
		});
		htmlList += '';
		$(this).html(htmlList);
	}
});

/* Convert text to html list */
$('#inclusions').stringToList('ul');
```

## 自定义名称空间版本

```
/*
 * $.stringToList - jQuery Function to convert a block of text into a html list.
 * @requires: full stops after each sentence to match list elements
 * @param: list type: ul or ol
 * Usage: FC.UTIL.stringToList($('#inclusions'),'ul');
 */
stringToList: function(textContainer,listType)
{
	var sentenceRegex = /[a-z0-9,'‘- ]+/igm, htmlList = '';
	$.each(textContainer.html().match(sentenceRegex), function(i, v)
	{
		/* Remove blank elements */
		if (v && (/[a-z0-9]+/igm.test(v)) && v != 'strong') 
		{
			htmlList += '*   + v + '';
		}
	});
	htmlList += '';
	textContainer.html(htmlList);
}

/* Convert text to html list */
NAMESPACE.stringToList('#inclusions','ul');
```

## 分享这篇文章