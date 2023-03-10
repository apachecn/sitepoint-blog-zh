# jQuery 新手指南，第二部分:渐进增强和 DOM

> 原文：<https://www.sitepoint.com/jquery-tutorial-part-2-dom/>

在上周的文章中，我们介绍了最基本的 jQuery 概念。我们已经学习了使用 jQuery 语句的步骤:确保文档准备好，选择元素，并更改它们。在本文中，我们将应用这些经验来实现一些很酷且有用的效果——以加深您对 jQuery 基础知识的理解。

这篇文章也摘自 Earle Castledine 和 Craig Sharkie 的《jQuery:新手到忍者的第二章。你可以在这里免费下载[第 1、2 和 7 章的 PDF 文件](https://www.sitepoint.com/books/jquery1/samplechapters.php)。该下载还包括整本书的代码档案，所以如果您想了解本文中的示例，请务必获取它。

## 隐藏和显露元素

客户不喜欢网站上的免责声明——他觉得这反映了产品的不好——但他的律师坚持认为这是必要的。因此，客户要求您添加一个按钮，在用户有机会阅读文本后删除文本:

**例 1。`chapter_02/11_hiding/index.html`(节选)**

```
<input type="button" id="hideButton" value="hide" />
```

我们在页面上添加了一个 ID 为`hideButton`的 HTML 按钮。当用户点击这个按钮时，我们希望隐藏 ID 为`disclaimer`的 disclaimer 元素:

**例二。`chapter_02/11_hiding/script.js`(节选)**

```
$('#hideButton').click(function() {  $('#disclaimer').hide();});
```

运行此代码，并确保单击隐藏按钮时免责声明元素消失。

本例中使元素实际消失的部分是`hide`动作。 所以，你可能会问，围绕那条线的其他代码是什么？这就是所谓的事件处理程序——理解它对于成为一名 jQuery 忍者至关重要。有许多我们可以使用的事件处理程序(我们在这里使用了`click`事件处理程序),随着我们的继续，我们将会用到很多。

## 事件处理程序

*事件处理程序* 因其处理事件的功能而得名。事件是发生在网页上的动作和用户交互。当一个事件发生时，我们说它已经 *激发了* 。而当我们编写一些代码来处理事件时，我们说我们 *捕捉到了* 事件。

在一个网页上，每时每刻都有成千上万的事件被触发:当用户移动鼠标，或者单击按钮，或者调整浏览器窗口的大小，或者移动滚动条。我们可以捕捉到这些事件，并采取行动。

本书中向您介绍的第一个事件是文档就绪事件。是的，这是一个事件处理程序:当文档说“我准备好了”时，它触发了一个事件，我们的 jQuery 语句捕获了这个事件。

我们使用`click`事件处理程序告诉 jQuery 在按钮被点击时隐藏免责声明:

```
$('#hideButton').*click*(function() {  $('#disclaimer').hide();});
```

## `this`

当一个事件触发时，我们通常希望引用触发它的元素。例如，我们可能想以某种方式修改用户刚刚点击的按钮。这样的引用可以通过 JavaScript 关键字`this`在我们的事件处理程序代码中获得。为了将 JavaScript 对象转换成 jQuery 对象，我们将它包装在 jQuery 选择器中:

**例 3。`chapter_02/12_this/script.js`(节选)**

```
$('#hideButton').click(function() {  *$(this)*.hide(); // a curious disappearing button.});
```

`$(this)`提供了一种更好的方式来谈论触发事件的元素，而不是重新选择它。

**tip:** Where’s the Action?

当你开始时，这可能会有点混乱，因为 jQuery 语句的“action”组件似乎有几个用途:我们已经看到它用于运行动画、检索值，现在又用于处理事件！这是真的——它到处传播！通常这个动作的名字会给你一个很好的线索来了解它的目的，但是如果你迷路了，最好查阅一下索引。过一会儿，您将从实用程序的动画中整理出处理程序。

## 揭示隐藏的元素

继续我们的任务！客户还指定用户需要能够检索免责声明，以防他们错误地关闭它。因此，让我们在 HTML 中添加另一个按钮，这次使用的是`showButton`的`id`:

**例 4。`chapter_02/13_revealing/index.html`(节选)**

```
<input type="button" id="showButton" value="show" />
```

我们还将在脚本文件中添加另一个 jQuery 语句，以便在点击 show 按钮时显示免责声明:

**例 5。`chapter_02/13_revealing/script.js`(节选)**

```
$('#showButton').click(function() {  $('#disclaimer').show();});
```

## 切换元素

拥有隐藏和显示免责声明的独立按钮似乎是对宝贵的屏幕空间的浪费。最好用一个按钮来完成这两项任务——在可见时隐藏免责声明，在隐藏时显示免责声明。一种方法是检查元素是否可见，然后相应地显示或隐藏。我们将删除旧按钮，并添加这个漂亮的新按钮:

**例 6。`chapter_02/14_toggle_1/index.html`(节选)**

```
<input type="button" id="toggleButton" value="toggle" />
```

当它被点击时，我们检查是否应该显示或隐藏免责声明:

**例 7。`chapter_02/14_toggle_1/script.js`(节选)**

```
$('#toggleButton').click(function() {  if ($('#disclaimer').is(':visible')) {    $('#disclaimer').hide();  } else {    $('#disclaimer').show();  }});
```

这就介绍了`is`动作。 `is`接受我们通常传递给 jQuery 函数的任何相同的选择器，并检查它们是否与调用它的元素相匹配。在这种情况下，我们检查我们选择的`#disclaimer`是否也被伪选择器`:visible`选择。它还可用于检查其他属性:选择是否为`form`或`div`，或者是否已启用。

**important:** The if Statement

如果你完全是编程新手(也就是说，如果你只使用过 HTML 和 CSS)，那么整个代码块可能会很混乱！别担心，这其实很简单:

```
if (condition) {  // this part happens if the condition is true} else {  // this part happens if the condition is false}
```

条件可以是 JavaScript 将评估为`true`或`false`的任何东西。这种结构在任何类型的编程中都非常常见，我们将在本书的其余部分大量使用它。如果你对此感到不舒服，最好的学习方法就是四处试验:尝试使用 jQuery 的`is`动作编写不同的`if` / `else`块，就像我们上面写的那样。你很快就会掌握它的窍门！

根据元素是否匹配选择器，`is`将返回`true`或`false`。对于我们的目的，我们将显示元素，如果它是隐藏的，隐藏它，如果它是可见的。这种逻辑——我们在两种状态之间翻转——被称为*切换* ，是一种非常有用的构造。

在两种状态之间切换元素是如此常见，以至于许多 jQuery 函数都有允许切换的版本。`show` / `hide`的切换版本简称为`toggle`，工作原理是这样的:

**例 8。`chapter_02/15_toggle_2/script.js`(节选)**

```
$('#toggleButton').click(function() {  $('#disclaimer').toggle();});
```

每次单击该按钮，元素都会在可见和隐藏之间切换。

然而，如果这个按钮用一个比“toggle”更有用的词来标记就更好了，因为“toggle”可能会让我们的用户感到困惑。如果你想切换按钮的文本呢？正如使用 jQuery 时经常遇到的情况一样，有几种方法可以解决这个问题。这里有一个:

**例 9。`chapter_02/16_toggle_text/script.js`(节选)**

```
$('#toggleButton').click(function() {  $('#disclaimer').toggle();  if ($('#disclaimer').is(':visible')) {    $(this).val('Hide');  } else {    $(this).val('Show');  }});
```

这段代码中有很多对您来说是新的。我们将把大部分细节留到以后，但是看一看它，看看你自己是否能弄清楚。(提示:记住选择器`$(this)`指的是导致事件触发的元素——在本例中是按钮。)

## 渐进增强

我们的免责声明功能运行得非常完美——我们的客户无疑会对它印象深刻。然而，我们的解决方案有一个微妙的方面需要注意:如果用户使用不支持 JavaScript 的浏览器访问我们的站点，他们会在页面上看到一个按钮，当他们单击它时，它什么也不做。这将导致一个非常困惑的用户，甚至可能会放弃我们的网站。

“不支持 JavaScript？”你可能会哼哼。“什么样的浏览器无法运行 JavaScript？!"

不使用 JavaScript 浏览网页的人可能比你想象的要多:使用非常旧的计算机或有限设备(如手机)的用户；需要屏幕阅读器来使用 Web 的有视觉障碍的人；有些人担心 JavaScript 是不必要的安全风险，因此选择禁用它。

根据你的站点的人口统计，任何地方都有 5%到 10%的用户可能在没有 JavaScript 功能的情况下浏览，没有人想疏远他们 10%的客户！解决方案是为这些用户提供可接受的体验——并为其他人提供更好的体验。这种做法被称为渐进增强。

对于我们的免责声明功能，我们可能会妥协:我们希望免责声明对所有用户都可见，所以我们将它放在 HTML 中。然后，我们添加了为使用 JavaScript 的用户隐藏它的功能。也就是说，我们希望避免向无法使用显示/隐藏按钮的用户显示该按钮。

实现这一点的一种方法可能是用 CSS 隐藏我们的按钮，只通过 jQuery `css`语句显示它。这个技巧的问题是，如果用户的浏览器也缺乏对 CSS 的支持，它将会失败。我们真正想做的是通过 jQuery 将按钮添加到页面中；这样，只有使用 JavaScript 的用户才能看到这个按钮。完美！

## 添加新元素

到目前为止，我们已经看到了用于选择的 jQuery 函数，但是它还有另一个同样重要的功能:创建新元素。事实上，您放入 jQuery 函数中的任何有效的 HTML 字符串都将被创建并准备好粘贴在页面上。下面是我们如何创建一个简单的段落元素:

```
$('<p>A new paragraph!</p>')
```

当您编写这段代码时，jQuery 执行几个有用的操作:它将 HTML 解析成 DOM 片段并选择它——就像普通的 jQuery 选择器一样。这意味着它立即准备好进行进一步的 jQuery 处理。例如，要给我们新创建的元素添加一个`class`，我们可以简单地写:

```
$('<p>A new paragraph!</p>').addClass('new');
```

新段落现在将被赋予`class` `new`。使用这种方法，您可以通过 jQuery 本身创建任何需要的新元素，而不是在 HTML 标记中定义它们。这样，我们可以完成我们的目标，逐步增强我们的网页。

**tip:** innerHTML

在内部，通过创建一个简单的元素(比如一个`div`)并将那个`div`的 innerHTML 属性设置为您提供的标记来解析 HTML 字符串。您传入的一些内容不能很容易地转换——所以最好保持 HTML 片段尽可能简单。

一旦我们创建了新元素，我们需要一种方法来插入到页面中我们想要它们去的地方。有几个 jQuery 函数可用于此目的。我们要看的第一个是`insertAfter`函数。 `insertAfter`将获取我们当前的 jQuery 选择(在本例中，是我们新创建的元素)并将其插入到另一个选择的元素之后，我们将该元素作为参数传递给函数。

一个例子是展示这是如何工作的最简单的方法。这就是我们如何使用 jQuery 创建切换按钮:

**例 10。`chapter_02/17_insert_after/script.js`(节选)**

```
*$('<input type="button" value="toggle" id="toggleButton">')  .insertAfter('#disclaimer');*$('#toggleButton').click(function() {  $('#disclaimer').toggle();});
```

如[图 1 所示，“用 jQuery 创建并插入的按钮”](#fig_toggle_button "Figure 1. A button created and inserted with jQuery")，这个按钮被插入到我们页面的免责声明之后，就像我们把它放在 HTML 文件中一样。

**图一。用 jQuery 创建并插入的按钮**

![A button created and inserted with jQuery](img/57a9c607222ce33d860faa92559d13e1.png)

`insertAfter`函数将新元素作为兄弟元素直接添加到 disclaimer 元素之后。如果你想让按钮在免责声明元素之前出现 *，你可以选择免责声明之前的元素并使用`insertAfter`，或者，更合理地说，使用`insertBefore`方法。*

**例 11。`chapter_02/18_insert_before/script.js`(节选)**

```
$('<input type="button" value="toggle" id="toggleButton">')  .*insertBefore*('#disclaimer');
```

快速复习一下:当我们谈到 DOM 、、*的兄弟*、指的是 DOM 层次结构中同一级别的元素。如果有一个包含两个`span`元素的`div`，那么`span`元素就是兄弟元素。

如果您想要添加新元素作为现有元素的 *子元素* (也就是说，如果您想要新元素出现在现有元素 内的 *，那么您可以使用`prependTo`或`appendTo`函数:*

**例 12。`chapter_02/19_prepend_append/script.js`(节选)**

```
$('<strong>START!</strong>').prependTo('#disclaimer');$('<strong>END!</strong>').appendTo('#disclaimer');
```

正如您在[图 2 中看到的，“prependTo 和 appendo in action”](#fig_prepend_append "Figure 2. prependTo and appendTo in action")，我们的新元素已经被添加到实际免责声明`div`的开始和结尾，而不是之前或之后。还有更多的插入和删除元素的操作，但是由于在这一轮的更改中不需要它们，我们将在稍后处理它们。

**图二。`prependTo`和`appendTo`在动作**

![prependTo and appendTo in action](img/61f7fb46993502eae898ec5c234fca9c.png)**important:** Inserting Multiple Elements

对于与选择器 匹配的每个元素，都会插入一个新的项目 *。例如，如果您的选择器匹配每个段落标签，那么`insertAfter`动作将在每个* 段落标签的 *之后添加一个新元素。这使得它成为一个相当强大的功能！*

## 分享这篇文章