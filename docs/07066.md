# 6 个漂亮、省时的 HTML 属性

> 原文：<https://www.sitepoint.com/6-nifty-time-saving-html-attributes/>

有成千上万的 HTML 属性。有些比较知名，应用也比较广泛，比如`style`、`href`，有些则比较隐晦。

然而，模糊的属性不一定是不太有用的。许多鲜为人知的属性就像快捷方式——它们可以节省你很多时间，让你的程序员生活更轻松。

我从那些来 Code Conquest 学习代码的人那里了解到，人们很容易低估这些省时工具的用处。在这篇文章中，我将向你展示六个鲜为人知但很棒的属性。

## 1.自动对焦属性

`autofocus`属性是一个可以赋予 HTML 中的`<input>`、`<textarea>`或`<button>`元素的属性。包含该属性将会“自动聚焦”该元素——也就是说，它将允许您立即开始键入(在文本字段的情况下)或点击 Enter(在按钮的情况下)。

属性是一个布尔属性，这意味着无论你给它什么值，它都会完成它的工作。*布尔型*，如果你不知道的话，指的是这样一个概念，即某物不是开就是关，是真还是假，有还是没有——这与二进制代码是同一个概念。

### 使用

```
<input type="text" autofocus="" /> <!-- Works with input of any type -->
<textarea autofocus="">Enter Text Here</textarea>
<button autofocus="">Click Me!</button>
```

因为一次只能聚焦一个 HTML 元素，所以在 HTML 源代码中应该只使用一次`autofocus`属性。但是，如果您碰巧不止一次地包含它，它将是最后一个具有获得焦点的属性的元素。

如果你有一个输入字段或按钮是焦点的网页，比如联系页面或搜索页面，你可以使用`autofocus`属性为你的用户节省一点时间。谷歌很好地做到了这一点，它在主页上自动聚焦搜索框。

除了为用户节省时间之外，`autofocus`属性也为编码人员节省了时间。如果它不存在，您将需要编写一两行 JavaScript 来实现相同的效果。我不知道你，但我发现属性更简单，更快。

Internet Explorer 10、谷歌 Chrome、Mozilla Firefox 和 Safari 支持`autofocus`属性，但 Opera 或更老版本的 IE 不支持*。*

## 2.占位符属性

`placeholder`属性也适用于`<input>`和`<textarea>`元素。与`value`属性一样，`placeholder`属性在字段中显示默认文本，但与`value`属性不同，一旦您开始键入，文本将被自动清除，如果您没有输入任何自己的文本，文本不会随表单一起提交。

### 使用

```
<input type="text" placeholder="Email Address" />
<textarea placeholder="Message"></textarea>
```

如上面的代码所示，`placeholder`属性可用于联系人表单、电子邮件提交表单、搜索表单或用户输入文本的任何地方。它很好地提示了用户应该输入什么。

在引入`placeholder`属性之前，您必须编写一些 JavaScript 事件处理程序来处理占位符文本。使用该属性可以节省您的时间和精力。

Opera、谷歌 Chrome、Mozilla Firefox 和 Safari 支持`placeholder`属性，但当前任何版本的 Internet Explorer 都不支持*属性。*

### 现场演示

<input type="text" placeholder="Start typing here!">

## 3.拼写检查属性

按照文本字段元素属性的主题，`spellcheck`属性用于启用或禁用浏览器拼写检查。它取值为`true`(启用拼写检查)或`false`(禁用拼写检查)。

### 使用

```
<input type="text" spellcheck="false" /> <!-- Spellcheck disabled -->
<textarea spellcheck="true"></textarea> <!-- Spellcheck enabled -->
```

默认情况下，大多数浏览器都会启用拼写检查，所以您可能不需要使用`true`值，但是有些时候使用设置为`false`的属性会派上用场。例如，许多人都有自己的用户名，所以如果你有一个文本字段让人们输入他们的用户名，你可能需要禁用拼写检查，这样人们的用户名就不会被标记为无法识别的单词。

属性非常漂亮，因为它不仅节省时间，而且做了一些没有它根本不可能做的事情——即使用 JavaScript 也不行。

Opera、Google Chrome 和 Safari 支持`spellcheck`属性，但 Mozilla Firefox 或 Internet Explorer 不支持*属性。*

### 现场演示

键入一些胡言乱语，看看会发生什么…
<textarea spellcheck="false">键入一些胡言乱语，看看这里会发生什么…</textarea>

## 4.Maxlength 属性

我在这里介绍的文本字段的最后一个属性是`maxlength`属性。此属性设置您可以在文本字段中输入的字符数的限制。

### 使用

```
<input type="text" maxlength="20" /> <!-- Works with input of any type -->
<textarea maxlength="20"></textarea>
```

在上面的例子中，输入长度的限制都是 20 个字符。设置限制的需求在网站上经常出现，比如用户名长度或电子邮件的长度，而`maxlength`属性正好做到了这一点。

这个属性是一个真正的省时器，因为它为您完成了表单验证的一个重要部分。如果它不存在，您可能需要在服务器端检查输入长度，这不仅浪费了您的时间(因为您必须编写额外的代码)，也浪费了您的用户的时间。

(编辑注:最佳实践不是仅仅依赖客户端验证:这太容易了。服务器端验证带来的安全性值得花费额外的时间来设置和实现。)

所有五种主流浏览器都支持`maxlength`属性。万岁！

### 现场演示

<input type="text" maxlength="16" value="Maximum 16 chara">

## 5.颠倒的属性

当我们想到`<ol>`有序列表时，我们通常会想到从 1 开始向上计数的列表。但是您知道吗，在您的`<ol>`元素中包含`reversed`属性可以让您的有序列表对倒计数*？*

属性是一个布尔属性，这意味着无论你给它什么值，它都会完成它的工作。

### 使用

```
<ol reversed="">
    <li>Number 3</li>
    <li>Number 2</li>
    <li>Number 1</li>
</ol>
```

不再需要在你想倒数的时候手动写出你的列表。属性为您做了繁重的工作，为您节省了大量时间。

一个缺点是`reversed`属性虽然有用，但目前只在谷歌浏览器中受支持。

### 现场演示

1.  列表项的数目是起始数，
2.  然后倒计时…
3.  …到 1。

## 6.开始属性

`start`属性也用在`<ol>`元素上，并指定除 1 之外的起始数字。

### 使用

```
<ol start="6">
    <li>Number 6</li>
    <li>Number 7</li>
    <li>Number 8</li>
</ol>
```

如果您想将一个有序列表分成几个单独的部分，每个部分从上一个部分停止的地方继续，那么这个属性非常有用。再说一次，不要再手动写出你的编号列表。

所有五种主流浏览器都支持`start`属性。万岁！

### 现场演示

6.  1 号在哪？
7.  2 号在哪？
8.  3 号在哪？
9.  4 号在哪？
10.  5 号在哪？

您甚至可以将`start`属性与`reversed`属性一起使用！下面的列表从 2 开始，然后以相反的顺序倒计数…

2.  第二个
3.  第一名
4.  数字 0
5.  第一名
6.  第二名

## 奖励:内容可编辑属性

是的，我知道这篇文章的标题是 *6 个漂亮、省时的 HTML 属性*，但是我不得不加上额外的第七个属性。:)

属性是那些不太为人所知的特性之一，但是非常漂亮。简单地说，它允许你作为一个网站浏览者在你的浏览器中编辑一个元素的内容。

为了证明`contenteditable`属性确实改变了 HTML 元素的内容，在浏览器的开发工具中找到该元素，您将看到 HTML 确实发生了变化。

`contenteditable`属性是一个全局属性，这意味着它将适用于任何 HTML 元素。它可用于启用或禁用内容编辑。它的值为`true`(启用编辑)或`false`(禁用编辑)。

### 使用

```
<p contenteditable="true"></p> <!-- Content editing enabled -->
<p contenteditable="false"></p> <!-- Content editing disabled -->
```

虽然`contenteditable`属性实际上不是任何东西的快捷方式，但你不得不同意——它很漂亮。

所有五种主流浏览器都支持`contenteditable`属性。万岁！

### 现场演示

你可以编辑这段文字！

## 结论

每个程序员都喜欢节省时间的快捷方式，我在这篇文章中展示的 HTML 属性正是如此。我鼓励你只要有机会就在你的 HTML 项目中实现它们。

你知道任何其他漂亮的 HTML 属性吗？请在评论中与我们分享。

## 分享这篇文章