# 代码示例的最佳实践

> 原文：<https://www.sitepoint.com/best-practice-for-code-examples/>

大多数关于 web 开发的文章都包含代码示例，在 web 上，我们看到它们的格式和呈现方式有很大的不同。

但是它们中的很多都不是很好——因为代码格式很差，难以阅读，或者在没有不需要的垃圾的情况下无法复制和粘贴。所以在这篇文章中，我想仔细看看代码示例，调查它们的常见问题，并尝试建立一些应该如何做的最佳实践。

随着我们的深入，您会不可避免地注意到 SitePoint 上的代码示例并不符合要求！这是我们在不久的将来将要改进的事情之一。

## 代码示例是用来做什么的？

技术文章和文档中代码示例的目的可以归结为**两个关键前提**:

*   说明一个概念或想法，或记录某事的语法
*   为读者提供复制粘贴代码

第一个前提是关于代码示例如何呈现的——它们应该易于阅读，并且很明显它们是代码。然而，如果这就是最重要的，为什么不只是一张照片呢？事实上，有时在我看来，这正是许多网站关注代码示例的原因，关注视觉外观而忽视了它们的实际用途。

但是代码不是图片，它是重要的文本内容，这就是为什么第二个前提是关于代码示例如何实现的原因——应该很容易从代码中复制和粘贴，而不会丢失格式，也不会在文本中出现不需要的垃圾。代码例子也应该有好的语义，给辅助技术和机器人理解它们的最好机会。

## 优秀代码示例的原则

基于这些前提，这里列出了我认为优秀代码示例的核心原则。我们将从这个列表开始，然后依次查看每一项，看看如何实现它:

1.  代码示例应该使用良好的语义标记。
2.  代码中的制表符不应转换为空格。
3.  代码应该突出显示基本语法。
4.  代码示例可以有水平滚动，但不应该有垂直滚动。
5.  代码示例应该有行号，这些行号不包括在文本选择中。

我还将提出另一个想法，你可能以前没有考虑过，但它实际上对代码示例的可用性产生了巨大的影响(正如我们将看到的):

5.  代码示例应该是可编辑的。

现在，这些原则中的一些似乎很难坚持。例如，看到制表符被转换成空格组是很常见的，因为大多数浏览器以 8 个字符的宽度呈现制表符。同样常见的是用一个`<li>`将每一行换行，但这当然意味着剪贴板文本仍将包含数字。

许多网站为弥补这些问题所做的是在代码块的角落添加一个单独的图标，它会打开一个包含原始代码的弹出窗口，从中进行复制和粘贴。但是我不认为这是一个可接受的解决方案——坦率地说，这很令人恼火——而且打开弹出窗口对可访问性或可用性来说从来都不是好事。

无论如何，这不是必要的:正如我们将看到的，有办法以一种干净、可访问和可用的方式实现所有这些需求。

### 1.例子应该使用好的语义标记

我见过的几乎每个代码示例都使用相同的基本元素——组合了`<pre>`和`<code>`——这是一个完美的选择。

内部的`<code>`提供了语义，对于代码来说从来都不是错误的选择，尽管还有一个`<samp>`元素可以用来显示流程的输出(比如显示一个请求返回的 <abbr title="HyperText Transfer Protocol">HTTP</abbr> 头的样本)。外部的`<pre>`是一个块级元素，具有原生的保留空白的格式，所以即使在 <abbr title="Cascading Style Sheets">CSS</abbr> 不可用时，我们也能获得该格式:

```
<pre><code>while(sheep != "zzz")
{
  sleep ++;
}</code></pre>
```

由于`<pre>`保留了其内容中的所有空白，这将包括在`<pre>`和`<code>`标签之间的任何内容，或者在`<code>`标签和文本之间的任何内容，这就是它们一起运行的原因。

最后，在 <abbr title="HyperText Markup Language Version 5">HTML5</abbr> 中，我们可以用一个 [`<figure>`](http://html5doctor.com/the-figure-figcaption-elements/ "The figure & figcaption elements (html5doctor.com)") 来包装整个东西。`<figure>`元素是为标记图表、图片和代码示例等东西而设计的，并且可以选择使用`<figcaption>`元素来添加标题。我们还可以通过在指向`<figcaption>`(如果有)的`<pre>`上添加一些<abbr title="Accessible Rich Internet Applications">咏叹调</abbr>属性— [`aria-labelledby`](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute "Using the aria-labelledby attribute (developer.mozilla.org)") ，以及指向前面文本的 [`aria-describedby`](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-describedby_attribute "Using the aria-describedby attribute (developer.mozilla.org)") (如果描述了例子)来提高可访问性。

因此，让我们也添加这些内容，然后我们将拥有一个可靠且可访问的结构，具有出色的全面语义:

```
<p id="example1-description">
  This is the descriptive text before the code example:
</p>
<figure>
  <figcaption id="example1-caption">
    This is the caption
  </figcaption>
<pre 
  aria-labelledby="example1-caption" 
  aria-describedby="example1-description"><code>function getToSleep()
{
  while(noise &lt;= 10 &amp;&amp; sleep !== "zzz")
  {
    sheep ++;
  }
  if(noise &gt; 10)
  {
    return false;
  }
  return true;
}</code></pre>
</figure>
```

让我们看看到目前为止我们的代码示例是什么样子的。这个演示包括一个位于页面中心的内容列，而代码块本身没有定义宽度，所以它占据了整个列。我还定义了一些基本的设计和字体属性，值得注意的是如何将相同的核心字体应用于`<pre>` *和* `<code>`元素，这是避免浏览器差异所必需的(因为浏览器有不同的默认值)，然后允许从`<pre>`控制整个`font-size`:

*   [**代码示例演示—原理 1**](http://jspro.brothercake.com/code-examples/principle1.html)

### 2.代码中的制表符不应转换为空格

当读者从代码示例中复制并粘贴时，文本应该与您在普通编辑器中使用的格式相同。没有哪个明智的人会用空格而不是制表符来编写代码，所以代码示例也不应该这样做。

然而，我们发现的一个问题是，如前所述，浏览器呈现的标签宽度为 8 个字符，这比大多数人想要的要大得多。这也加剧了水平空间有限的问题(我们将在后面讨论)。

但是 **<abbr title="Cascading Style Sheets">和</abbr> `tab-size`属性**有一个巧妙的解决方案。它在 Chrome、Firefox 和 Opera 中得到支持，后两者使用了厂商前缀，所以为了让它工作，我们需要三个变体:

```
pre
{
  -moz-tab-size:4;
  -o-tab-size:4;
  tab-size:4;
}
```

一些网站更喜欢 2 个字符的标签，虽然我认为 4 个字符的可读性更好，但这并没有太大的区别。关键是选项卡应该是选项卡——它们有多大由你决定。

当然，不支持该属性的浏览器仍然会以默认大小显示标签；这很不幸，但总比用空格代替好。

当我们谈论空白的时候，我还要说**空行不应该被删除**。我们在代码中添加空行是有原因的——创建逻辑部分并使其更具可读性——代码示例也是如此。

现在让我们看看添加了`tab-size`的演示:

*   [**代码示例演示—原理二**](http://jspro.brothercake.com/code-examples/principle2.html)

### 3.代码应该有基本的语法突出显示

语法突出显示的目的是使代码更容易理解。这种改进是显著的，尤其是对初学者而言。

要实现它，我们别无选择，只能将额外的元素包装在代码位周围，这可以用 <abbr title="Hypertext Pre-Processor">PHP</abbr> 或 JavaScript 来完成，或者硬编码到示例中。就我个人而言，我认为硬编码是最好的(因为没有额外的处理开销)，但是这类问题超出了本文的范围，所以我将把决定权留给您。就最终结果而言，没什么区别

我想谈的是**使用哪些元素**——我的建议如下:

*   使用`<i>`或`<em>`进行注释
*   使用`<b>`或`<strong>`进行关键字和常量编程， <abbr title="Cascading Style Sheets">CSS</abbr> 属性名， <abbr title="HyperText Markup Language">HTML</abbr> 标签和属性名
*   使用`<u>`作为编程字符串， <abbr title="Cascading Style Sheets">CSS</abbr> 字符串， <abbr title="HyperText Markup Language">HTML</abbr> 属性值

这在 <abbr title="Cascading Style Sheets">CSS</abbr> 中通过几个派生组合得到了认可，因此字符串覆盖了关键字，注释覆盖了一切:

```
pre b
{
  font-weight:normal;
  color:#039;
}
pre u, pre u b
{
  text-decoration:none;
  color:#083;
}
pre i, pre i *, pre i * *
{
  letter-spacing:-0.1em;
  text-decoration:none;
  font-style:normal;
  color:#c55;
}
```

这种对元素的选择可能会引起争议，而且肯定是相当严格的——对不同的类使用`<span>`有更大的范围，如果这是您喜欢的解决方案，我不能告诉您这是错误的。但是语法突出显示不需要那么广泛，它只需要足以提高理解能力，使用我建议的元素的优点是**即使没有 <abbr title="Cascading Style Sheets">CSS</abbr>** 我们仍然可以得到基本的突出显示。

实际上，还有另一个原因——但我把它留到以后再说！

你会从 <abbr title="Cascading Style Sheets">CSS</abbr> 中注意到，我选择了使用`<b>`和`<i>`而不是`<strong>`和`<em>`，这个选择也值得商榷。例如，你可能会认为使用`<em>`是正确的，因为这是一种强调，但同样，你也可能会认为使用`<i>`是正确的，因为这种差异主要是视觉上的。我个人倾向于后一种观点。

因此，让我们看看我们的演示是如何通过增加语法高亮来改进的:

*   [**代码示例演示—原理三**](http://jspro.brothercake.com/code-examples/principle3.html)

### 4.示例可以有水平滚动，但不应该有垂直滚动

这与我们将页面视为一个整体的方式相反，在这种情况下，水平滚动是非常避免的，但是垂直滚动是不可避免的。正是因为我们以这种方式制作页面，所以代码示例应该反其道而行之——因为水平空间是有限的，但垂直空间却是无限的。

内部滚动区域通常对可用性没有好处，因为它们更难交互，并且不能用键盘控制，除非元素本身获得焦点。所以我们应该把水平滚动看作是在长行的情况下的一个退路。

换句话说，最好**以避免长行**的方式编写示例代码，但是如果我们随后添加`overflow-x:auto`，我们将为不可能的情况做好准备，或者当它在非常小的屏幕上被查看时。

唯一会失败的时候是打印页面的时候，但是在这种情况下，我们可以忽略滚动，用 [`white-space:pre-wrap`](https://reference.sitepoint.com/css/white-space "CSS white-space property (reference.sitepoint.com)") 来补偿:

```
pre
{
  overflow-x:auto;
}
@media print
{
  pre
  {
    overflow-x:visible;
    white-space:pre-wrap;
  }
}
```

我一般不推荐使用`pre-wrap`——因为换行会使代码更加难以理解——但是如果我们不这样做，那么最长的代码行将完全丢失。

回到我们更新后的演示，如果你将窗口的宽度调整到一个更小的值，你会看到水平滚动开始了:

*   [**代码示例演示—原理四**](http://jspro.brothercake.com/code-examples/principle4.html)

### 5.示例应该有行号，这些行号不包含在剪贴板数据中。

我见过的几乎所有有行号的代码示例都是通过在每一行周围包装额外的标记来实现它们的——要么为每一行使用带有`<li>`的`<ol>`元素，要么像这样使用`<table>`标记:

```
<table>
  <tbody>
    <tr>
      <td><code>1</code></td>
      <td><code>while(sleep != "zzz")</code></td>
    </tr>
    <tr>
      <td><code>2</code></td>
      <td><code>{</code></td>
    </tr>
    <tr>
      <td><code>3</code></td>
      <td><code>  sheep ++;</code></td>
    </tr>
    <tr>
      <td><code>4</code></td>
      <td><code>}</code></td>
    </tr>
  </tbody>
</table>
```

我认为很难从语义上证明这一点，但是除此之外，真正的问题是当读者复制并粘贴代码时，它的数字仍然包含在内。即使这些数字是使用 <abbr title="Cascading Style Sheets">CSS</abbr> 生成的内容实现的，一些浏览器仍然会将它们包含在剪贴板数据中。

我之前提到过解决这个问题的常见方法，那就是在代码块的角落添加一个图标，打开一个包含原始代码的弹出窗口。我已经拒绝了，因为这是一个乏味且不可接受的解决方案！

一定有更好的方法——确实有，事实上非常简单。我们所需要做的就是用代码之外的元素*实现数字**。基本上是这样:***

![](img/ebddd763f5f5b641acb4893ca47e13ca.png)
**我们需要创建行号的方框，显示数字元素如何完全在代码之外。**

棘手的是保持数字与代码行垂直同步。这意味着的第一件事是，我们**肯定不能使用预包装格式**，因为那样我们就没有代码行和数字之间的固定链接。但是第二件也是最重要的事情是，我们**必须确保所有的元素使用相同的字体和行高**。

因此，这意味着代码示例的样式会有许多变化。首先，我们必须使用内部代码元素作为主要布局块，应用之前在`<pre>`上的`padding`、`overflow`和`tab-size`。为了 <abbr title="Internet Explorer 7">IE7</abbr> (其他浏览器只是继承了它)，我们还必须定义显式的`white-space`:

```
pre, pre code, pre samp
{
  display:block;
  margin:0;
}
pre code, pre samp
{
  white-space:pre;

  padding:10px;

  overflow-x:auto;
}
```

我们还必须扩展字体重置，使其适用于`<pre>`中的*和*，而整体的`font-size`仍然以同样的方式控制:

```
pre, pre *
{
  font:normal normal normal 1em/1.4 monaco, courier, monospace;
}
pre
{
  font-size:0.8em;
}
```

这些变化释放了`<pre>`来充当 numbers 容器的定位上下文；默认情况下，容器是隐藏的，直到应用了额外的`class`(稍后我会解释原因):

```
pre.line-numbers
{
  position:relative;
}
pre.line-numbers code, 
pre.line-numbers samp
{
  margin-left:3em;
}
pre div
{
  display:none;
}
pre.line-numbers > div
{
  display:block;

  position:absolute;
  top:0;
  left:0;
}
```

在容器内部，我们将创建一堆空的`<span>`，每个都有`block`显示，所以它们形成了一个垂直堆栈，其中`width`和`padding`组合在一起，创建了我们在`<pre>` `margin-left`中允许的`3em`空间:

```
pre.line-numbers > div > span
{
  display:block;
  width:2.5em;
  padding:0 0.5em 0 0;

  text-align:right;
}
```

数字本身是使用 <abbr title="Cascading Style Sheets">CSS</abbr> 生成的内容实现的，这比普通文本更好，因为没有 <abbr title="Cascading Style Sheets">CSS</abbr> 它们是不可见的(而普通文本会在`<pre>`上方显示为一行数字):

```
pre.line-numbers > div
{
  counter-reset:line;
}
pre.line-numbers > div > span
{
  counter-increment:line;
}
pre.line-numbers > div > span::before
{
  content:counter(line);
}
```

但这也符合我的普遍看法，即[生成的内容并不是真正的内容](https://www.sitepoint.com/is-generated-content-actually-content/ "Is Generated Content Actually Content? (sitepoint.com)")——因为**的行号也不是真正的内容，它们是表示**。容器`<div>`也应该有`aria-hidden`，这样屏幕阅读器就不会读到数字；有些屏幕阅读器根本不阅读生成的内容，但那些阅读的人会一次阅读所有内容，并且脱离上下文，就像它是代码之前的一个大数字一样。

为了生成所有这些内容，我将使用 JavaScript——部分是为了易于维护，但主要是因为它将让我们有机会过滤掉 <abbr title="Internet Explorer 8">IE8</abbr> 和更早的版本(它们不支持生成的内容)。这也是为什么额外的样式是用一个`class`选择器定义的——所以除非支持脚本，否则它们不会应用:

```
(function()
{
  if(typeof(window.getComputedStyle) == 'undefined') 
  { 
    return; 
  }
  var pre = document.getElementsByTagName('pre');
  for(var len = pre.length, i = 0; i < len; i ++)
  {
    var code = pre[i].getElementsByTagName('code').item(0);
    if(!code)
    {
      code = pre[i].getElementsByTagName('samp').item(0);
      if(!code)
      {
        continue;
      }
    }
    var column = document.createElement('div');
    column.setAttribute('aria-hidden', 'true');
    for(var n = 0; n < code.innerHTML.split(/[nr]/g).length; n ++)
    {
      column.appendChild(document.createElement('span'));
    }
    pre[i].insertBefore(column, code);
    pre[i].className = 'line-numbers';
  }  
})();
```

最终结果是完全对齐的数字，这些数字不会出现在复制的文本中—当您选择代码时，您得到的只是:

![](img/b7b20033db8a790991bcf29543ea5945.png)
**代码可以选择和复制，不包括行号。**

如果任何一行足够长以至于触发水平滚动，数字*不会随之滚动——它们会停留在相同的位置，使它们始终可见！*

然而，你可能还记得之前我们是如何使用`white-space:pre-wrap`进行打印的，我们不能把它和这个技术结合起来，因为我们不能补偿换行。没有简单的方法来检测一行何时换行，所以我们唯一能做的就是去掉打印的数字:

```
@media print
{
  pre code
  {
    overflow-x:visible;
    white-space:pre-wrap;
  }
  pre.line-numbers div
  {
    display:none;
  }
  pre.line-numbers > code, 
  pre.line-numbers > samp
  {
    margin-left:0;
  }
}
```

查看更新后的演示，了解它在实践中是如何工作的:

*   [**代码示例演示—原理 5**](http://jspro.brothercake.com/code-examples/principle5.html)

### 6.代码示例应该是可编辑的

读者想要编辑代码示例并没有什么特别的原因(除非你正在编写代码编辑器，但那完全是另外一回事)。

不，可编辑代码示例有用的原因是可编辑区域有**额外的导航和选择控件**，例如:

*   你可以在代码内部点击并使用`Ctrl+A`来选择它。
*   您可以使用`Shift+Arrow`通过键盘进行部分文本选择。
*   您可以使用`Home`和`End`等键在内部导航。
*   现在可以用键盘滚动了(导航的结果)。

令人高兴的是，这很容易实现，只需将 [`contenteditable`](http://html5doctor.com/the-contenteditable-attribute/ "The contenteditable attribute (html5doctor.com)") 添加到内部的`<code>`或`<samp>`元素中。它必须在内部元素上，因为那是一个滚动的元素，更不用说把它放在`<pre>`上会让用户意外删除里面的所有内容！我们还将添加`tabindex`使其可以通过键盘访问，并禁用`spellcheck`，因为它对代码基本上没用(尽管这不会阻止它在 Firefox 中的运行):

```
<code contenteditable="true" tabindex="0" spellcheck="false">
```

这不会影响我们的语法高亮，因为可编辑区域仍然可以包含标记。事实上……你还记得我提到的使用`<b>`、`<i>`和`<u>`的一个额外的令人信服的理由吗？原因是这样的——因为使用这些元素意味着**读者可以用本地编辑器击键`Ctrl+B`、`Ctrl+I`和`Ctrl+U`来编辑突出显示自己的**语法。

然而，浏览器在使用哪些元素来实现这些动作方面有所不同——例如，一些浏览器通过将`<strong>`环绕在文本周围来实现`Ctrl+B`，而其他浏览器则使用`<b>`。所以为了让它跨浏览器工作，我们需要加倍语法规则(这也意味着`<strong>`或`<b>`是否是正确的语义选择的问题现在有点悬而未决！):

```
pre b, pre strong
{
  font-weight:normal;
  color:#039;
}
pre u, pre u b, pre u strong
{
  text-decoration:none;
  color:#083;
}
pre i, pre em, pre i *, pre em *, pre i * *, pre em * *
{
  letter-spacing:-0.1em;
  text-decoration:none;
  font-style:normal;
  color:#c55;
}
```

当然，我已经说过我们在这里不是在构建一个编辑器，所以没有*真正的理由*为什么拥有这种控制是重要的。一点都不重要，只是手感好而已！因此，如果你宁愿使用`<span>`来突出显示语法，而不使用这些突出显示的按键，不要让我说服你。

但是使用`contenteditable`的原因是为了提供额外的选择和导航控制——可编辑的代码示例已经大大提高了的可用性和可访问性。有办法<q>全选</q>本身就是足够的理由。

现在，让我们最后看一下添加了该内容的演示。我还定义了一些`:focus`样式来提供视觉提示，添加了白色背景和强烈的嵌入阴影:

*   [**代码示例演示—原理 6**](http://jspro.brothercake.com/code-examples/principle6.html)

## 结论

现在我们有了好代码示例的六个指导原则，确保它们可访问、可用、语义有效、易于阅读、易于复制和粘贴。

您可以下载包含本文所有演示的 zip 文件:

*   [**代码示例演示(原则 1–6)**](http://jspro.brothercake.com/code-examples/demos.zip)

## 分享这篇文章