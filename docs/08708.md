# 如何把你的 CSS3 放到:target

> 原文：<https://www.sitepoint.com/css3-target-selector/>

CSS 伪类已经伴随我们很多年了，它会根据用户的动作改变样式。你几乎肯定用过:hover，:active 和:focus 我还记得 1997 年改变 IE4 链接颜色时的兴奋。

CSS3 还介绍了:target 一个强大的伪类，可以减少交互式小部件中的脚本需求。考虑一个页面 URL，比如 http://mysite.com/page**# my target**；id 为“mytarget”的元素可以应用 matching :target 样式。

## :目标浏览器支持

所有现代浏览器都支持:target，你在使用 IE9 或大多数版本的 Chrome、Firefox、Safari 和 Opera 时都不会遇到问题。不幸的是，我们仍然只能使用 IE 的旧版本。我不会太担心 IE6 和 ie7，但 IE8 仍然是世界上使用最多的浏览器版本。然而，并非一切都完了，因为像[selectivitizr](http://selectivizr.com/)这样的垫片可以增加:目标支持，而不需要复杂的变通方法。

## 一个简单的文档:目标

我们最近一直在讨论网站合同。一般合同小字，如付款时间表、托管条件、取消条款、支持政策、词汇表等。可以包含在一个或多个网页中。即使你尽量保持简洁，文档也会变得很长！

让我们来看看在*contract.html*中文档的 HTML5 的一个片段:

```
 <h1>Website Contract</h1>

<nav>
	<ul>
		<li><a href="#payment">Payment Schedule</a></li>
		<li><a href="#support">Support &amp; Maintenance</a></li>
		<li><a href="#hosting">Hosting Terms</a></li>
		<li><a href="#glossary">Glossary</a></li>
	</ul>
</nav>

<article id="payment">
<h2>Payment Schedule</h2>
<p>Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
</article>

<article id="support">
<h2>Support &amp; Maintenance</h2>
<p>Ut euismod tempor porttitor.</p>
</article>

<article id="hosting">
<h2>Hosting Terms</h2>
<p>Suspendisse ac nisl lorem, ut fermentum erat.</p>
</article>

<article id="glossary">
<h2>Glossary</h2>
<p>Aenean id nibh eget nisl blandit hendrerit lobortis ac tortor.</p>
</article> 
```

我们可以使用:target 属性来突出显示活动部分，例如

```
 article:target
{
	background-color: #ffc;
	border: 2px solid #fcc;
} 
```

查看合同 的任何人 [**都可以点击导航菜单项来突出显示适当的部分。您还可以向需要特定信息的客户发布直接链接，例如**](https://blogs.sitepointstatic.com/examples/tech/css3-target/document.html)**[**contract . html # support**](https://blogs.sitepointstatic.com/examples/tech/css3-target/document.html#support)。**

:target 选择器提供了更多的功能——不使用 JavaScript 也可以在 HTML5 和 CSS 中创建动态效果。更多 SitePoint 文章即将推出…

## 分享这篇文章