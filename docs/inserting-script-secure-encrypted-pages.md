# jQuery 向安全/加密页面插入脚本

> 原文：<https://www.sitepoint.com/inserting-script-secure-encrypted-pages/>

我们遇到了这个耗时的问题，并想与遇到同样情况的人分享。很抱歉这篇文章的长度，但这是一个很难解决的问题！

**问题:**当使用 JavaScript/jQuery 加载页面时，动态地将脚本插入 DOM head 标签。在这个例子中，我们试图在页面上加载一个 openX 脚本。

我们遇到的问题有:

*   IE8 由于加密的页面/网站而导致错误信息——为了解决这个问题，我们可以检查加密的页面，并且只为未加密的页面加载 OpenX 脚本。
*   加载引用第一个脚本创建的变量的第二个脚本会导致未定义的错误消息——为了解决这个问题，我们只是添加了一个 if 语句，以便在使用变量之前检查它是否存在。
*   document.write 导致页面刷新–要解决此问题，请在页面加载后不要使用 document.write。

```
(function() {

if (window.location.protocol !== 'https') {

	var openx = document.createElement('script'); openx.type = 'text/javascript'; openx.async = true;
    openx.src = '<path to="" script="">';

	//Insert into head
	var theHead = document.getElementsByTagName('head')[0];
	theHead.appendChild(openx); 

	console.log('script inserted into head');
}
})();</path>
```

如果你想在头文件中包含多行 js 脚本(即不仅仅是一个. js 文件),你可以这样做。

```
(function() {

	if (window.location.protocol !== 'https:') {

		/*Create dynamic scripts*/
		var openX = document.createElement('script'); 
		openX.type = 'text/javascript'; 
		openX.defer = 'defer'; /*defer is only supported by IE*/
		openX.async = true; /*async is a html5 proposal*/
		openX.src = '<path to="" script="">';
		var multiOpenX = document.createElement('script'); 
		multiOpenX.type = 'text/javascript'; 
		multiOpenX.defer = 'defer'; multiOpenX.async = true;
		multiOpenX.innerHTML = 
		   ['var OX_4ddf11d681ca9 = OX();',
			'OX_4ddf11d681ca9.addPage("2400");',
			'OX_4ddf11d681ca9.fetchAds();'
		   ].join('n');

		/*Insert into head tag*/
		var theHead = document.getElementsByTagName('head')[0];
		theHead.appendChild(openX); 
		theHead.appendChild(multiOpenX); 

	}

})();</path>
```

在 head 部分使用 innerHTML 标签时，IE8 似乎会产生错误。除了不使用 innerHTML 的替代方法，我看不到解决这个问题的办法。我们可以恢复到 jQuery.getScript()，然后在第一个脚本参数加载后传入第二个脚本参数，如下所示:

```
$.getScript('ajax/test.js', function() {
    alert('Load was performed.');
});
```

或者把它放入一个函数中，从体内调用它，就像这样:

您也可以这样编写多行脚本(警告:有些浏览器会在延续处插入换行符，有些不会)。

```
var multiOpenX = 
'  ';
```

## 最终剧本

适用于包括 IE8 在内的所有浏览器的最终脚本:

也可以在这里查看 URL 中引用的不同检查:[http://www . jquery 4 u . com/JavaScript/JavaScript-location-hostnames-URL-examples/](http://www.jquery4u.com/javascript/javascript-location-hostnames-url-examples/) 

## 分享这篇文章