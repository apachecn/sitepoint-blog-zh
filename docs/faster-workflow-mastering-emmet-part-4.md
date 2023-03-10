# 更快的工作流程:掌握 Emmet，第 4 部分

> 原文：<https://www.sitepoint.com/faster-workflow-mastering-emmet-part-4/>

## 埃米特行动

如果你已经学习了本系列的[前三部分，你现在知道如何*用 Emmet 创建*大代码块。然而，有时你需要编辑你的*现有的* HTML 和 CSS。](https://www.sitepoint.com/series/faster-workflow-mastering-emmet/)

埃米特也能为你做到。在 Emmet 中，您可以轻松地将现有文本换行。您可以轻松地换行或代码块。

让我们来看一些有助于此的函数。

### 前往配对

```
Ctrl+Alt+J
```

在 HTML 中，这允许您在开始和结束标记之间快速遍历。“转到匹配对”操作在内部使用“HTML 匹配器”，因此它也可以在非 HTML 语法中工作。

**示例:**通过这个动作，你可以快速跳转到任何元素的结束标签。

![match pair](img/6ee08a843d4f6dc19c9719fe60241d17.png)

### 用缩写换行

```
Shift+Ctrl+G
```

这是 Emmet 中一个非常强大的工具。它接受一个缩写，将其展开，并将当前选择的内容放在生成的代码片段的最后一个元素中。您可以很容易地用 HTML 标签将文本换行。

![wrap](img/0bdcf5075e73a1d7636f6a489cb52885.png)

在上图中，你可以看到:

*   所有内容都用一个`article`标签包装起来。
*   这两行都用一个`p`标签包裹起来。
*   我还添加了新的`h1`标签。

### 怎么做

选择内容并按`Shift+Ctrl+G`，然后输入您的缩写并按 enter。以上几行，我都用了`article>h1{title here}+p*`缩写。

我也使用了`>`添加了一个`h1`作为子元素，并添加了一个`+`符号来包装两行，并将`p`作为兄弟元素。`*`符号用于换行。

### 换行

```
`Ctrl+Shift+G` or `Ctrl+Alt+Enter` 
```

这是 Emmet 最大的特点之一。你可以很容易地用这个功能包装多个段落、列表等。要重复一个元素，你可以使用`*`操作符，就像我在上面的例子中使用的一样。假设您想要创建一个导航菜单。你是怎么做到的？

**示例导航菜单**假设你有四个项目，你想把它们包装在一个`ul`中，每个`li`列表项目应该是一个`a`链接。

```
Home
About
Portfolio
Contact
```

选择所有项目，按下``Ctrl+Shift+G``或``Ctrl+Alt+Enter``并在缩写面板中输入您的缩写。我用了一个`nav>ul>li*>a`的缩写来包装菜单项。

![wrap each line](img/bba2140a2c5db0da60aeef4bc6388f4b.png)

在这里你可以看到我们的``nav>ul>li*>a`` 缩写产生了以下 HTML 的最终结果。

```
<nav>
	<ul>
		<li><a href="">Home</a></li>
		<li><a href="">About</a></li>
		<li><a href="">Portfolio</a></li>
		<li><a href="">Contact</a></li>
	</ul>
</nav>
```

**更复杂的例子**

Emmet 允许我们控制和提供缺省值或元素。让我们看另一个更复杂的例子。我将用一个更复杂的缩写来包装上面的菜单。这是我们的菜单。

```
Home
About
Portfolio
Contact
```

现在选择这些项目，按``Shift+Ctrl+G``并输入以下缩写。

```
nav>ul>li[title=$#]*>a[href=$#.html]{$#}>img[src=$#.png alt="$#"].nav-bg
```

这将为菜单项生成以下代码。

```
<nav>
	<ul>
		<li title="Home">
			<a href="Home.html">Home
			<img src="Home.png" alt="Home" class="nav-bg">
			</a>
		</li>
		<li title="About">
			<a href="About.html">About
			<img src="About.png" alt="About" class="nav-bg">
			</a>
		</li>
		<li title="Portfolio">
			<a href="Portfolio.html">Portfolio
			<img src="Portfolio.png" alt="Portfolio" class="nav-bg">
			</a>
		</li>
		<li title="Contact">
			<a href="Contact.html">Contact
			<img src="Contact.png" alt="Contact" class="nav-bg">
			</a>
		</li>
	</ul>
</nav>
```

在上面的例子中，你可以看到用 Emmet 生成复杂的 HTML 代码非常容易。掌握 Emmet 语法确实需要一些时间，但是在学会缩写之后，你将能够生成复杂的 HTML 代码。

### 前往编辑点

该操作适用于 HTML 代码块，并允许您在重要的代码点之间快速跳转。您可以轻松地为空属性键入值。

在 SublimeText 中，您可以使用``Tab``前往下一个编辑点，使用``Shift+Tab``前往上一个编辑点。

![edit point](img/5642398b27bc6e9930cc24b5a7d632b6.png)

### 切换注释

```
— `⇧⌥/` / `Shift+Ctrl+/`
```

HTML 和 CSS 中的注释对 web 开发人员非常有帮助。每个文本编辑器都有一些添加注释的快捷键。注释掉代码是非常好的做法，特别是在复杂的网页中，以指示文档的各个部分。注释可以帮助你和其他人理解你的代码。

你可以很容易地用 Emmet 注释掉你的 HTML 和 CSS 代码。通常，当没有选择时，SublimeText 等文本编辑器会在当前行切换注释，而 Emmet 的 toogle comment 特性会在当前上下文中切换注释。对于 HTML，它是一个完整的标签，对于 CSS，它是一个规则或完整的属性。

![toggle comments](img/636ad1b7a606cc55d624ddfb452642a6.png)

当你第一次按下``Shift+Ctrl+/``时，你的代码会被注释掉，再按一次删除注释。

### 拆分、连接和移除标签

有时你需要从代码中移除标签。你可以很容易地用埃米特做到这一点。

### 拆分/合并

`` Shift+Ctrl+` ``:该功能可以将`<header></header>`转换为`<header/>`，反之亦然。(`<header/>`变成`<header></header>`)。

### 移除标签

``Shift+Ctrl+;``快捷键将从元素中移除标签。例如，如果您想从下面的文本`<h2>hello there</h2>`中删除`<h2></h2>`，您需要将光标放在`<h2></h2>`标记内，然后按键盘快捷键``Shift+Ctrl+;``。

![remove split](img/79eecfaf674d24521c34463fa0ad5dd5.png)

仅此而已。我希望这个 Emmet 系列已经帮助了你，并且将使你的编码工作流程更快。别忘了在评论里分享你的经历。

## 分享这篇文章