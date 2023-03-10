# CSS 选择器:组合子

> 原文：<https://www.sitepoint.com/css-selectors-combinators/>

CSS 规则用 **选择器** 匹配元素。有许多方法可以做到这一点，并且您可能对其中的大多数都很熟悉。元素类型、类名、ID 和属性选择器都得到很好的支持 [CSS 选择器](https://www.sitepoint.com/css-selectors/)并被广泛使用。

[选择器 3 级](http://dev.w3.org/csswg/selectors-3/)和[4 级](http://dev.w3.org/csswg/selectors-4/)规格引入了几个新的选择器。在某些情况下，这些是现有类型的新变体。在其他情况下，它们是语言的新特征。

在这一章中，我们将着眼于当前 CSS 选择器的浏览器前景，重点是新的选择器。这包括新的属性选择器和组合子，以及一系列新的伪类。在 *节明智地选择选择器* 中，我们来看特异性的概念。

这一章没有全面介绍所有的选择器――这本身就是一本书。相反，我们将关注具有良好浏览器支持的选择器，它们可能对您当前的工作有用。一些材料可能是旧的帽子，但它被包括在上下文中。

### 提示:选择器的浏览器覆盖率

在 [CSS4-Selectors 中可以找到对当前浏览器选择器支持状态的全面了解。](http://css4-selectors.com/)

![cssmasterthumb](img/e6dad2f67bc57ebaf36a56e667c1e778.png)

下面是我们的书《CSS 大师》的摘录，作者是蒂芙尼·布朗。世界各地的商店都有出售，或者你可以在这里买到电子书。

## 组合子

**组合子** 是字符序列，表示它两边的选择器之间的关系。使用组合子创建了所谓的复杂选择器。 **复杂的选择器** 在某些情况下，可能是定义样式最简洁的方式。

您应该熟悉这些组合子中的大多数:

*   后代组合符或空白字符

*   子组合子，或`>`

*   相邻兄弟组合子，或`+`

*   一般兄弟组合子，或`~`

让我们来举例说明这些组合子。我们将使用它们向如下所示的 HTML 表单添加样式。

![SelectorsCombinators](img/5e36ba30d160113514669e8a07a2825e.png)

该表单是使用以下 HTML 块创建的:

```
<form method="GET" action="/processor">
<h1>Buy Tickets to the Web Developer Gala</h1>
<p>Tickets are $10 each. Dinner packages are an extra $5\. All fields are required.</p>
<fieldset>
	<legend>Tickets and Add-ons</legend>

	<p>
		<label for="quantity">Number of Tickets</label> 
		<span class="help">Limit 8</span>
		<input type="number" value="1" name="quantity" id="quantity" step="1" min="1" max="8">
</p>

	<p>
		<label for="quantity">Dinner Packages</label> 
		<span class="help">Serves 2</span>
		<input type="number" value="1" name="quantity" id="quantity" step="1" min="1" max="8">
	</p>

</fieldset>
<fieldset>
	<legend>Payment</legend>
	<p>
		<label for="ccn">Credit card number</label>
		<span class="help">No spaces or dashes, please.</span>
		<input type="text" id="ccn" name="ccn" placeholder="372000000000008" maxlength="16" size="16">
	</p>
	<p>
		<label for="expiration">Expiration date</label>
		<span class="help"><abbr title="Two-digit month">MM</abbr>/<abbr title="Four-digit Year">MM</abbr>YYYY</span>
		<input type="text" id="expiration" name="expiration" placeholder="01/2018" maxlength="7" size="7">
	</p>

</fieldset>
<fieldset>
	<legend>Billing Address</legend>
	<p>
		<label for="name">Name</label>
		<input type="text" id="name" name="name" placeholder="ex: John Q. Public" size="40"> 
	</p>
	<p>
		<label for="street_address">Street Address</label>
		<input type="text" id="name" name="name" placeholder="ex: 12345 Main Street, Apt 23" size="40">
	</p>

	<p>
		<label for="city">City</label>
		<input type="text" id="city" name="city" placeholder="ex: Anytown">
	</p>

	<p>
		<label for="state">State</label>
		<input type="text" id="state" name="state" placeholder="CA" maxlength="2" pattern="[A-W]{2}" size="2">
	</p>

	<p>
		<label for="zip">ZIP</label>
		<input type="text" id="zip" name="zip" placeholder="12345" maxlength="5" pattern="0-9{5}" size="5">
	</p>
</fieldset>

<button type="submit">Buy Tickets!</button>
</form>
```

### 后代组合子

你可能很熟悉后代组合子。它在 CSS 早期就已经存在了(尽管在 CSS2.1 之前它没有类型名)。它被广泛使用和支持。

**后代组合符** 只是一个空白字符。它按照模式 *A B* 将父选择器与其后代分开，其中 *B* 是由 *A* 包含的元素。让我们在上面的标记中添加一些 CSS，看看它是如何工作的:

```
form h1 {
color: #009;
}
```

我们刚刚改变了表单标题的颜色，结果如下所示。

![DescendantCombinator](img/6ff8b7b46d65a9b232874923b7975a96.png)

让我们再添加一些 CSS，这次是为了增加我们的定价信息的大小(“门票每张 10 美元”):

```
form p {
font-size: 22px;
}
```

然而，正如您在下面看到的，这个选择器有一个问题。我们实际上增加了表单段落的所有*中的文本大小，这不是我们想要的。我们如何解决这个问题？让我们试试子组合子。*

 *![DescendentCombinator2](img/de9c45bca6039868bc10f164591fc509.png)

### 子组合子

与后代组合子相反， **子组合子** ( >)只选择元素的 *直接子* 。它遵循模式 *A > B* ，匹配任何元素 *B* 其中 *A* 是直接祖先。

打个比方，如果元素是人，子组合子将匹配母元素的子元素。但是后代组合子也会匹配她的孙子和曾孙。让我们修改前面的选择器，使用子组合子:

```
form > p {
font-size: 22px;
}
```

现在只有`article`的直接子代受到影响，如下图。

![ChildCombinator](img/c3dedb7d3c83ed8377e7503d966805e1.png)

### 相邻兄弟组合子

使用 **相邻兄弟组合符** ( `+`)，我们可以选择彼此跟随并且具有相同父元素的元素。它遵循的模式是 *A + B* 。样式将应用于紧接在*元素前面的*的 *B* 元素。**

 **让我们回到我们的例子。请注意，我们的标签和输入是相邻的。这意味着我们可以使用相邻的兄弟组合符使它们位于不同的行上:

```
label + input {
display: block;
clear: both;
}
```

你可以在下面看到结果。

![AdjacentSibling1](img/2cf3a7c57f5d025108a7fb6c7288e0b9.png)

让我们看另一个结合了通用选择器(*)和类型选择器的例子:

```
* + fieldset {
margin: 5em 0;
}
```

该示例在每个`fieldset`元素的顶部和底部添加了一个`5em`边距，如下所示。因为我们使用通用选择器，所以不需要担心前一个元素是另一个`fieldset`还是`p`元素。

![AdjacentSibling3](img/6fa324a724a80fd4713017aca9151999.png)

### 注意:相邻兄弟选择器的更多使用

Heydon Pickering 在他的文章[“公理化 CSS 和脑叶切除猫头鹰”中探索了相邻兄弟选择器的更聪明的用法](http://alistapart.com/article/axiomatic-css-and-lobotomized-owls)

如果我们想要设计一个 *不与另一个* 相邻的兄弟元素，比如我们的 **Number of Tickets** 字段，该怎么办？在这种情况下，我们可以使用一般的兄弟组合子。

### 一般兄弟组合子

使用 **通用兄弟组合符**——一个波浪号——我们可以选择共享同一个父元素的元素，而不用考虑它们是否相邻。给定模式 *A ~ B* ，该选择器匹配前面有*元素的所有 *B* 元素，无论它们是否相邻。*

 *我们再来看一下**的票号**字段。它的标记如下所示:

```
<p>
<label for="quantity">Number of Tickets</label> 
<span class="help">Limit 8</span>
<input type="number" value="1" name="quantity" id="quantity" step="1" min="1" max="8">
</p>
```

我们的`input`元素跟在`label`元素后面，但是在它们之间有一个`span`元素。由于一个`span`元素位于`input`和`label`之间，相邻的兄弟组合子将无法在这里工作。让我们把相邻的兄弟组合子改成一般的兄弟组合子:

```
label ~ input {
display: block;
}
```

现在我们所有的`input`元素都位于与它们的`label`元素不同的一行上，如下所示。

![GeneralSiblingCombinator](img/1090d7eaf4a9309d0c500e013a9cf18c.png)

当您缺乏对标记的完全控制时，使用一般的兄弟组合子是最方便的。否则，您最好调整您的标记来添加一个类名。请记住，一般的兄弟组合符可能会在大型代码库中产生一些意想不到的副作用，所以要小心使用。**** 

## **分享这篇文章**