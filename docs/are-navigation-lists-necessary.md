# 导航应该在列表中定义吗？

> 原文：<https://www.sitepoint.com/are-navigation-lists-necessary/>

Chris Coyer 的 [CSS-Tricks](http://css-tricks.com/) : [*列表导航上贴了一篇有趣的文章:做还是不做*](http://css-tricks.com/navigation-in-lists-to-be-or-not-to-be/) 。这篇文章引发了过多的评论和辩论。这里有一个总结…

当面对创建导航菜单时，大多数 web 开发人员会实现如下代码:

```
<nav>
	<ul>
		<li><a href="#">Home</a></li>
		<li><a href="#">Products</a></li>
		<li><a href="#">Services</a></li>
		<li><a href="#">About Us</a></li>
		<li><a href="#">Contact Us</a></li>
	</ul>
</nav> 
```

没有从 HTML4 土地上移民过来的，就省略`<nav>`，用`<ul id="nav">`或者类似的。

问题:*列表元素是必要的吗？*
裸链接更精简更干净…

```
<nav>
	<a href="#">Home</a>
	<a href="#">Products</a>
	<a href="#">Services</a>
	<a href="#">About Us</a>
	<a href="#">Contact Us</a>
</nav> 
```

在 HTML5 之前，列表是必需的:

*   旧的浏览器可能很难设计内联元素的样式
*   相邻链接可能会导致屏幕阅读器中的可访问性问题
*   你必须在某个地方放置导航——列表是一个很好的选择。

随着 HTML5 `<nav>`元素的引入，这些问题大多消失了。多余的列表元素是不必要的，因为浏览器/屏幕阅读器理解它是一个导航块，可以相应地处理链接。

也就是说，有许多保留列表的好理由:

1.  可以定义层次菜单和子菜单。没有理由不使用其他元素，尽管好处很少，例如

    ```
    <nav>
    	<h1>Main Menu</h1>
    	<a href="#">Home</a>
    	<a href="#">Products</a>
    	<section>
    		<h2>Product Menu</h2>
    		<a href="#">Product One</a>
    		<a href="#">Product Two</a>
    	</section>
    	<a href="#">Services</a>
    </nav> 
    ```

2.  列表为 CSS 挂钩和样式提供了额外的元素。
3.  列表是一种被广泛采用的技术，开发人员都知道并期待它的出现。

导航列表已经成为根深蒂固的决定性技术。我们中很少有人考虑无精打采的导航；这感觉不对——但真的是这样吗？这将取决于环境，并且很容易陷入语义争论，但我怀疑克里斯是正确的:列表通常是不必要的。

我打算在我的下一个项目中尝试裸链接导航。你会吗？

## 分享这篇文章