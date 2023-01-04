# jQuery titleCaps 函数

> 原文：<https://www.sitepoint.com/jquery-titlecaps-function/>

我看到了这个功能，我想我会分享它，因为它可以方便地动态生成页面标题(如果你的标题是统一的和大写的，对 SEO 有适当的帮助)。这个函数最初是由 John Gruber 编写的，但是几年前由 John Resig 移植到 jQuery 上。

## 使用

```
var someVar = 'we love jquery';
console.log(upper(someVar));
//output: We love jquery

console.log(lower(someVar));
//output: we love jquery

console.log(titleCaps(someVar));
//output: We Love Jquery
```

## jQuery titleCaps 函数

```
/**
 * Title Caps
 *
 * Ported to JavaScript By John Resig - http://ejohn.org/ - 21 May 2008
 * Original by John Gruber - http://daringfireball.net/ - 10 May 2008
 * License: http://www.opensource.org/licenses/mit-license.php
 */
(function($)
{
	var small = "(a|an|and|as|at|but|by|en|for|if|in|of|on|or|the|to|v[.]?|via|vs[.]?)";
	var punct = "([!"#$%&'()*+,./:;?@[\\\]^_`{|}~-]*)";

	/**
	 * Apply title caps to the supplied string
	 * @param {String} title
	 * @returns {String}
	 */
	titleCaps = function(title)
	{
		var parts = [], split = /[:.;?!] |(?: |^)["Ã’]/g, index = 0;

		while (true)
		{
			var m = split.exec(title);

			parts.push( title.substring(index, m ? m.index : title.length)
				.replace(/b([A-Za-z][a-z.'Ã•]*)b/g, function(all)
				{
					return /[A-Za-z].[A-Za-z]/.test(all) ? all : upper(all);
				})
				.replace(RegExp("\b" + small + "\b", "ig"), lower)
				.replace(RegExp("^" + punct + small + "\b", "ig"), function(all, punct, word)
				{
					return punct + upper(word);
				})
				.replace(RegExp("\b" + small + punct + "$", "ig"), upper));

			index = split.lastIndex;

			if(m) parts.push(m[0]);
			else break;
		}

		return parts.join("").replace(/ V(s?). /ig, " v$1\. ")
			.replace(/(['Ã•])Sb/ig, "$1s")
			.replace(/b(AT&T|Q&A)b/ig, function(all){
				return all.toUpperCase();
			});
	};

	function lower(word)
	{
		return word.toLowerCase();
	}

	function upper(word)
	{
	  return word.substr(0,1).toUpperCase() + word.substr(1);
	}

	//Examples
	var someVar = 'we love jquery';
	console.log(upper(someVar));
	//output: We love jquery
	console.log(lower(someVar));
	//output: we love jquery
	console.log(titleCaps(someVar));
	//output: We Love Jquery
})(jQuery);
```

## 分享这篇文章