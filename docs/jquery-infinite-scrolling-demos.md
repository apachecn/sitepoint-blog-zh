# 6 个 jQuery 无限滚动演示

> 原文：<https://www.sitepoint.com/jquery-infinite-scrolling-demos/>

无限滚动现在是一个常见的特性，在一些情况下它非常有用。例如，在一些网站上，我们无法想象一个好的分页系统，比如 Twitter，甚至脸书。无限滚动有用的另一个例子是搜索引擎:当你找不到你想要的链接时，你会想继续查看新的链接，分页系统会减慢你的搜索速度。

如果你需要在你的项目中使用无限滚动，这里有六个演示，你可以用来作为实现它的灵感。

注意，除了最后一个，所有这些演示都是用 jQuery 编写的，有些例子使用了 jQuery 插件。然而，其他的例子可以很容易地适用于普通的 JS，没有任何问题。

## 1.无限滚动和网格

这个演示使用了 [jQuery Masonry 插件](http://masonry.desandro.com/)和[无限滚动插件](http://www.infinite-scroll.com/)。砖石插件有利于获得流体网格布局。Paul Irish 的无限滚动插件擅长加载已经存在的页面(因此对你的 SEO 有好处)。你可以用它来加载`page2.html`、`page3.html`等静态页面。，不过这个插件也处理生成的页面，比如`page.php?p=2`、`page.php?p=3`。然而，要使用它，你需要有一个页码来增加你的网址，所以，如果你有像`page.php?data=xxx`这样的网页，那么这个插件不适合你。

[//embed.plnkr.co/Uml5zQ/?show=preview](//embed.plnkr.co/Uml5zQ/?show=preview)

### 用法–HTML

```
<div class="grid">
	<div class="grid-item grid-item-2">
		<p>content</p>
	</div>
	…
</div>

<!-- The next page which content will be loaded when scrolled -->
<nav id="pagination">
	<p><a href="page-2.html">Page 2</a></p>
</nav>
```

### 用法–jQuery

```
$(document).ready(function() {
	var grid = $('.grid');

	grid.masonry({
		itemSelector: '.grid-item',
		columnWidth: 200
	});

	grid.infinitescroll({
		// Pagination element that will be hidden
		navSelector: '#pagination',

		// Next page link
		nextSelector: '#pagination p a',

		// Selector of items to retrieve
		itemSelector: '.grid-item',

		// Loading message
		loadingText: 'Loading new items…'
	},

	// Function called once the elements are retrieved
	function(new_elts) {
		var elts = $(new_elts).css('opacity', 0);

		elts.animate({opacity: 1});
		grid.masonry('appended', elts);
	});
});
```

## 2.无限滚动浏览博客文章

这个演示没有使用任何插件或库来处理无限滚动功能。每当用户到达页面末尾时，它就会加载一个新的 post，这个 post 是由一个 PHP 脚本生成的，它会回显相应的 HTML 代码。演示永远不会到达内容的结尾，但是你可以通过，例如，当没有更多文章要显示时，回显一个空字符串来达到这个目的。本着 Twitter 的精神，我们在页面的末尾显示一个加载图像。

注意，在下面的现场演示中，新帖子是由 JavaScript 函数生成的，因为我们不能在 CodePen 中使用 PHP 脚本。

看到笔[在](http://codepen.io/SitePoint/pen/JGQPPa/) [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )无限滚动浏览博文。