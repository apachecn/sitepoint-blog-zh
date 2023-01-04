# 设置我们的鲶鱼–第 2 部分:SlideMe

> 原文：<https://www.sitepoint.com/catfish-ads-part-2/>

在第一部分的[中，Alex 介绍了我们对](https://www.sitepoint.com/the-catfish-part-1/)[鲶鱼](http://en.wikipedia.org/wiki/Catfish)广告的实现，并演示了我们如何设法让它们出现在所有浏览器的窗口底部，而不会在滚动时出现抖动。

如果你错过了，[回去看看第一部分](https://www.sitepoint.com/the-catfish-part-1/)。我们找到了一种简便的方法来解决 Internet Explorer 不支持元素固定定位的问题。

现在，我们有了一条工作鲶鱼广告。它贴在屏幕的底部，当滚动的时候它会停留在那里。

问题是，它一直都在。如果鲶鱼“溜”进视野，而不是立即出现，它会更加引人注目。让你的网站访客大吃一惊！

**将鲶鱼滑入视野**

用一点 DHTML jiggery-pokery，我们可以定位鲶鱼，使它看不见，它的顶部就在我们的浏览器窗口的底部下面。这是稍后“将其滑入视图”的理想位置。

*提示:这也给了我们一个开始在鲶鱼中预加载图像的机会，这样当它出现时，图像就应该准备好了。*

负的下边距很好地隐藏了它。

```
margin-bottom: -79px;
```

如果为此使用负边距会让您觉得有点不干净，请不要担心。当我们把鲶鱼放入视野时，我们会去掉边缘。这样我们也不会遇到鲶鱼将要去的地方，我们将暂时移除我们添加到 body 元素底部的空白。

```
/*
html {
padding:0 0 58px 0;
}
*/
```

现在加载文档，如果您认为绝对没有鲶鱼，那是情有可原的。它就在那里，只是看不见而已。我们必须设置一些暂停时间来公开讨论这个问题。

我创建了一个名为 [catfishdeploy.js](https://www.sitepoint.com/examples/catfish-part2/catfishdeploy.js) 的新文件，它与我们在 sitepoint.com 上使用的代码非常相似。

```
// Deploy the Catfish

// The Catfish should be located in an element of id 'catfish' and should be hidden
// out of view

function deploycatfish()
// initializing
{
	catfish = document.getElementById('catfish');

	catfishheight = 79; // total height of catfish in pixels
	catfishoverlap = 21; // height of the 'overlap' portion only (semi-transparent)
	catfishtimeout = setTimeout(startcatfish, 2000);
}
```

这个函数的最后一行设置了一个 2 秒的超时，它将调用`startcatfish`。这条鲶鱼的图像约占 10KB。这可能有点高，但对于演示来说已经足够了。在显示鲶鱼之前等待 2 秒钟应该会给图像一点时间来加载，即使是在调制解调器连接上。

在`startcatfish`中，我们设置了一个`catfishposition`变量来保存当前的鲶鱼位置。`setInterval`可以用来平稳地转移鲶鱼。

```
function startcatfish()
// starts the catfish sliding up
{
	catfishposition = 0; // catfishposition is expressed in percentage points (out of 100)
	catfishtimeout = setInterval(positioncatfish, 25);
}
```

因为鲶鱼的位置大约每 25 毫秒改变一次(在最好的情况下)，所以当我们改变位置时，我们不想做任何太耗时的事情。我们可以解决额外的问题，比如在`html`元素的底部增加空间，为鲶鱼预留空间——一旦它完全可见。现在，我们将通过改变鲶鱼的底部边缘来改变它的位置。

```
function positioncatfish()
{
	catfishposition += 10;
	catfish.style.marginBottom = '-' + (((100 - catfishposition) / 100) * catfishheight) + 'px';
	if (catfishposition >= 100)
	{
		clearTimeout(catfishtimeout);
		catfishtimeout = setTimeout(finishcatfish, 1);
	}
}
```

一旦鲶鱼在全景中，我们设置一个超时来调用`finishcatfish`。`finishcatfish`将在 body 元素上添加一些填充，以便为它“保留空间”。这确保了访问者能够阅读页面的页脚，而不会被鲶鱼遮住。参见[第 1 部分](https://www.sitepoint.com/the-catfish-part-1/)了解这是如何实现的。

```
function finishcatfish()
{
	catfish.style.marginBottom = '0';	
	// jump the bottom of the document to give room for the catfish when scrolled right down
	document.body.parentNode.style.paddingBottom = (catfishheight - catfishoverlap) +'px';

	// here you could use AJAX (or similar) to log the popup hit for tracking purposes	
}
```

**结果**

[看看结果吧！](https://www.sitepoint.com/examples/catfish-part2/slideme.php)鲶鱼现在暂时躲在我们的视线之外，最终在我们最意想不到的时候突然出现。它看起来很光滑。它也非常吸引眼球，但又不过分烦人(它不会把注意力从页面的其他部分转移开)。

我们还能做什么？

在 SitePoint，我们轮流提供一系列不同的鲶鱼广告。广告本身是通过 JavaScript 部署的，而不是静态出现在我们的代码中。关于如何做到这一点的信息将会在以后的博客中发布！

## 分享这篇文章