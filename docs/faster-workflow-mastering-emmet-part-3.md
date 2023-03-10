# 更快的工作流程:掌握 Emmet，第 3 部分

> 原文：<https://www.sitepoint.com/faster-workflow-mastering-emmet-part-3/>

在 Emmet 系列的这一部分中，我们将学习语法和函数。

## Emmet 语法和函数

Emmet 使用类似于 CSS 选择器的语法来描述元素在生成的树和元素属性中的位置。您可以使用像`div`或`p`这样的元素名来生成 HTML 标签。

## 应用类和 id。和#

`.`用于向元素添加类，`#`用于添加 id。下面是一些 id 和类的例子。您可以对任何元素应用多个 id 和类。

### 班级

```
ul.nav.nav-main.nav-static
```

会产生

```
<ul class="nav nav-main nav-static"></ul>
```

### 身份证

```
header#site-header
```

会产生

```
<header id="site-header"></header>
```

正如你在上面的例子中看到的，我已经对`ul`应用了多个类。

## 子元素: >

`>`用于将元素嵌套在彼此内部。可以多次使用。创建导航菜单和列表会很有用。见下面的代码，我要创建一个导航菜单与 ul，列表项和列表项将是一个链接。

```
nav>ul>li>a
```

将生成以下导航菜单。`nav`是一个父元素，那么我们有`ul`作为子元素，有`a`标签的`li`作为孙元素。

```
<nav>
	<ul>
		<li>
			<a href=""></a>
		</li>
	</ul>
</nav>
```

## 兄弟姐妹:+

`+`符号用于创建同级项目。在同一层上创建彼此相邻的元素是很有用的。以下缩写将生成三个兄弟姐妹。

```
header+.main+footer
```

将输出

```
<header></header>
<div class="main"></div>
<footer></footer>
```

## 向上爬:^

使用`>`你可以在彼此内部创建元素。但是如果你想在创建的 HTML 树中向上爬一层，你必须使用`^`符号。让我们看一个例子，相同的缩写使用不同的`+`和`^`操作符。

**例 1: +**

```
header+article>p>str+bq+footer
```

将生成以下代码。

```
<header></header>
<article>
	<p>
		<strong></strong>
		<blockquote></blockquote>
		<footer></footer>
	</p>
</article>
```

如您所见，`strong`、`blockquote`和`footer`嵌套在`p`段落标记中，但是我们想将`p`之后的`blockquote`作为兄弟元素，将`footer`作为不同的部分。为此，我们必须使用`^`操作符。

**例 2: ^**

键入以下代码并点击`tab`。

```
header+article>p>str^bq^footer
```

我们有了我们需要的标记:

```
<header></header>
<article>
	<p><strong></strong></p>
	<blockquote></blockquote>
</article>
<footer></footer>
```

## 乘法:*

乘法是 Emmet 中非常有用的功能之一。使用`*`操作符，您可以定义元素应该输出多少次。创建包含多个列表项的导航菜单非常有用。

```
nav>ul>li*5>a
```

将生成一个包含五个列表项无序列表。我还使用了`a`标签使列表项成为可点击的链接。你可以用你想要的数字代替 5。

```
<nav>
	<ul>
		<li><a href=""></a></li>
		<li><a href=""></a></li>
		<li><a href=""></a></li>
		<li><a href=""></a></li>
		<li><a href=""></a></li>
	</ul>
</nav>
```

## 项目编号:$

我们可以使用乘法运算符`*`来重复元素，但是使用`$`我们可以对它们进行编号。在元素名、属性名或属性值中使用`$`运算符，输出重复元素的当前数量。它可用于创建带编号的列表。让我们看一些关于`$`操作符如何工作的例子。

**例 1:项目编号**

```
ul>li.item$*5
```

跟在马克后面。

```
<ul>
	<li class="item1"></li>
	<li class="item2"></li>
	<li class="item3"></li>
	<li class="item4"></li>
	<li class="item5"></li>
</ul>
```

要在数字前添加 0(零)，请使用多个`$`符号。`li.item$$`会产生`<li class="item01"></li>`，`li.item$$$`会产生`<li class="item001"></li>`。

**例 2:在数字前加零**

```
ul>li.item$$*5>a
```

输出以下代码。数字前可以看到`0`。

```
<ul>
	<li class="item01"><a href=""></a></li>
	<li class="item02"><a href=""></a></li>
	<li class="item03"><a href=""></a></li>
	<li class="item04"><a href=""></a></li>
	<li class="item05"><a href=""></a></li>
</ul> 
```

**例 3:改变方向**

也可以改变编号的方向。Emmet 使用`@-`修改器来改变方向。

```
ul>li.item$$@-*5>a
```

将输出下面的 HTML 标记，一个降序排列的无序列表。

```
<ul>
	<li class="item05"><a href=""></a></li>
	<li class="item04"><a href=""></a></li>
	<li class="item03"><a href=""></a></li>
	<li class="item02"><a href=""></a></li>
	<li class="item01"><a href=""></a></li>
</ul> 
```

**改变编号基数:起始编号**

要更改计数器基值，请在`$`上添加一个`@N`修饰符。使用这个修改器，你可以设置你想要的起始数。

```
ul>li.item$@3*5
```

扩展到以下标记。

```
<ul>
	<li class="item3"></li>
	<li class="item4"></li>
	<li class="item5"></li>
	<li class="item6"></li>
	<li class="item7"></li>
</ul>
```

可以同时使用基础`@N`和方向`@-`修改器。这里有一个例子。

```
ul>li.item$@-3*5>a
```

将转换成以下标记。

```
<ul>
	<li class="item7"><a href=""></a></li>
	<li class="item6"><a href=""></a></li>
	<li class="item5"><a href=""></a></li>
	<li class="item4"><a href=""></a></li>
	<li class="item3"><a href=""></a></li>
</ul>
```

## 分组: ()

使用 Emmet 对元素进行分组也非常容易。您可以使用`()`对元素或部分进行分组。使用组，您可以用一个缩写来编写整个页面的标记。

让我们用`header`、`.main`和`footer`为我们的页面创建标记。

```
(header>nav>ul>li*3>a)+(.main>article.primary>h1+p^aside#sidebar)+footer>p{© 2013}
```

将为我们的页面生成以下标记。

```
<header>
	<nav>
		<ul>
			<li><a href=""></a></li>
			<li><a href=""></a></li>
			<li><a href=""></a></li>
		</ul>
	</nav>
</header>

<div class="main">
	<article class="primary">
		<h1></h1>
		<p></p>
	</article>
	<aside id="sidebar"></aside>
</div>

<footer>
	<p>© 2013</p>
</footer>
```

## 带方括号的自定义属性:[ ]

Emmet 可以使用`[attr]`符号(就像在 CSS 中一样)来为你的元素添加定制属性，比如一个链接。向元素添加默认值很有用。您可以在方括号`[ ]`中放置任意多的属性。如果你不指定属性值，Emmet 将在每个空属性中产生制表位(如果你的编辑器支持的话)。

**没有值的属性**

**例 1:无值**

```
a[title]
```

将生成以下不带任何值的标记。

```
<a href="" title=""></a>
```

**示例 2:使用默认值**

```
td[title="Hello world!" colspan=3]
```

将生成以下标记。

```
<td title="Hello world!" colspan="3"></td>
```

**示例 3:具有值的属性(多个或单个)**

```
a[href="https://www.sitepoint.com" title="Learn HTML, CSS and more" target="_blank"]
```

将生成一个带有默认值的链接。

```
<a href="https://www.sitepoint.com" title="Learn HTML, CSS and more" target="_blank"></a>
```

## 文本:{}

向元素添加默认文本很简单，您可以对任何元素`a`、`p`、`h1`使用花括号`{}`来添加定制文本。

简单示例:

```
a{click here}
```

将输出

```
<a href="">click here</a>
```

**更复杂的例子:**

这里有一个更复杂的例子。如果你打字

```
p>{Click }+a{here}+{ to continue}
```

并点击 Tab 或 Ctr+E 展开它，您将有以下标记。

```
<p>Click <a href="">here</a> to continue</p>
```

## 带有 lorem ipsum 的虚拟文本

Web 开发人员经常使用 lorem ipsum 文本向他们的 web 页面添加虚拟数据，并测试他们的 HTML 模板在真实数据下的外观。有第三方网站可以生成 lorem ipsum 虚拟文本，如[http://www.lipsum.com/](http://www.lipsum.com/)和[http://loremipsum.net/](http://loremipsum.net/)，但使用 Emmet，您可以动态生成 lorem ipsum 文本。

通常情况下，lorem ipsum 会生成 30 个单词的虚拟文本，分成几个句子，但是使用 Emmet，您可以指定应该在缩写中生成的单词数。

**例 1:两段缺省的 lorem ipsum**

```
p*2>lorem
```

将生成两个包含虚拟内容的段落。

```
<p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quaerat, eaque, molestiae saepe aut autem in earum magnam harum enim obcaecati eum pariatur sit suscipit nisi repellat quia a ipsum reiciendis!</p
			>
<p>Ipsum, laudantium, quia suscipit qui officia autem dicta alias explicabo illo quis voluptas corporis eveniet laborum ducimus possimus quas debitis sunt quibusdam libero deserunt. Odit nemo beatae officiis perspiciatis delectus.</p>
```

**例 2:指定字数**

在“lorem”后面加上数字就行了。`p>lorem10`将生成 10 个单词，`p>lorem50`将生成 50 个单词的段落。

```
p>lorem10
```

将生成一个 10 个单词的段落。

```
<p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Consequuntur, asperiores.</p>
```

示例 3:使用 lorem ipsum 列出项目

这个缩写将创建一个无序列表，其中有四个条目，带有虚拟文本和类`item`。

```
ul.generic-list>lorem8.item*4
```

这是输出。

```
<ul class="generic-list">
	<li class="item">Lorem ipsum dolor sit amet, consectetur adipisicing elit.</li>
	<li class="item">Quis, dolorum blanditiis reiciendis eius facere nulla porro.</li>
	<li class="item">Repellat, animi, nisi quibusdam esse distinctio nostrum blanditiis!</li>
	<li class="item">Neque, eius temporibus itaque nostrum tempore expedita ad?</li>
</ul>
```

## CSS 缩写

Emmet 有一个特殊的 CSS 解析器，可以将这样的缩写扩展成一个完整的 CSS 属性。对于 CSS 语法，Emmet 有许多预定义的属性片段。就像 HTML 一样，你可以将缩写扩展成完整的 CSS 属性。

这里有一些例子。

**例 1:预定义缩写**

```
ff
```

然后点击 tab。你会得到

```
font-family: ;
```

**例 2:带值的缩写**

```
ff:a
```

将输出下面的 CSS 代码。

```
font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
```

**例 3:预定义缩写**

```
mr
```

将输出下面的 CSS 代码。

```
margin-right: ;
```

**例 4:添加默认值**

```
mr10
```

将输出下面的 CSS 代码。

```
margin-right: 10px;
```

**示例 5:多个值**要将多个值相加，请使用一个分号来分隔它们:

```
m10-20
```

将输出下面的 css 代码。

```
margin: 10px 20px;
```

**示例 6:添加一个负值**会在第一个值前面加上一个连字符，其余的都加上双连字符

```
p-10--20
```

将输出下面的 CSS 代码。

```
padding: -10px -20px;
```

## 供应商前缀

如今，厂商前缀是使用 CSS3 属性的常见方式。Emmet 使得使用供应商前缀变得很容易。此外，在支持制表位的编辑器中(如 Eclipse、Sublime Text 2、Espresso 等)，Emmet 将创建一个链接值占位符，这样您可以键入属性值，它将自动放置在所有生成的属性中。

**示例 1:盒子尺寸**

```
bx
```

将输出下面的 CSS 代码。

```
-webkit-box-sizing: border-box;
-moz-box-sizing: border-box;
box-sizing: border-box;
```

**例 2:边框半径**

```
-bdrs
```

将输出下面的 CSS 代码。

```
-webkit-border-radius: ;
-moz-border-radius: ;
border-radius: ;
```

**例 3:边框半径**

```
-trf
```

将输出下面的 CSS 代码。

```
-webkit-transform: ;
-moz-transform: ;
-ms-transform: ;
-o-transform: ;
transform: ;
```

**明确的供应商前缀**

如果您只需要输出带有指定供应商前缀属性的 CSS 属性，您可以使用 Emmet 来完成。假设您只需要输出带有 webkit 和 moz 前缀的转换属性。在这种情况下，您可以扩展以下缩写:

**示例:边框半径**

```
-wm-trf
```

将输出下面的 CSS 代码。

```
-webkit-transform: ;
-moz-transform: ;
transform: ;
```

CSS3 渐变是一个很难写的 CSS3 特性，我不能自己写。我使用一个在线的 [CSS3 生成器](https://www.sitepoint.com/10-best-css3-code-generators/)工具来获取 CSS3 属性。Emmet 让编写 CSS3 渐变变得简单。下面是您需要键入的内容以及您将得到的输出。

**举例:**

```
div{
	lg(left, #fc0 30%, orange)
}
```

将输出下面的 css 代码。

```
div{
	background-image: -webkit-gradient(linear, 0 0, 100% 0, color-stop(0.3, #fc0), to(orange));
	background-image: -webkit-linear-gradient(left, #fc0 30%, orange);
	background-image: -moz-linear-gradient(left, #fc0 30%, orange);
	background-image: -o-linear-gradient(left, #fc0 30%, orange);
	background-image: linear-gradient(left, #fc0 30%, orange);
}
```

你可以看到我刚刚使用了`lg`的线性梯度和附加值。Emmet 已经把我的缩写变成了完整的 CSS3 代码。

在本系列的第四部分也是最后一部分，我们将看看 Emmet 如何处理现有的标记和代码。

## 分享这篇文章