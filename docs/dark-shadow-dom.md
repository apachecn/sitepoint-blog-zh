# 大教堂的黑暗阴影

> 原文：<https://www.sitepoint.com/dark-shadow-dom/>

***更新 2015.03.17:我在这篇文章中表达的可访问性问题是不正确的，是基于误解。事实上，影子 DOM 和屏幕阅读器*** 不存在这样的可访问性问题

* * *

影子 DOM 是 [Web 组件](https://www.w3.org/TR/2013/WD-components-intro-20130606/ "Introduction to Web Components (w3.org)")规范的一部分，旨在解决困扰某些 Web 开发的封装问题。

您知道这样的事情—如果您构建一个定制的小部件，如何避免与同一页面上的其他内容发生命名冲突？最重要的是，**你如何防止页面的 <abbr title="Cascading Style Sheets">CSS</abbr> 影响你的小部件？**

如果您控制整个页面，这很容易，但通常情况下并非如此——如果您正在制作供其他人使用的小部件，情况就不是这样了。问题是你不知道还有哪些 T2 的 CSS 会出席。通过将所有的选择器定义为具有高度特异性的事物的后代，您当然可以减少出现这种问题的可能性:

```
#mywidget > .mywidget-container
{
}
#mywidget > .mywidget-container > .mywidget-inner
{
}
```

但这只有在网站定义了一个带有两个身份选择器的规则后才有效。或者你可以用两个，但三个随之而来！

最近，我一直在考虑定义动态选择器的想法——小部件脚本遍历 <abbr title="Document Object Model">DOM</abbr> ,记录下它自己和文档根之间的每个元素 ID，然后编译包含所有这些 ID 的选择器。

但即使这样也不能保证。除了使用`<iframe>`之外，我们真的没有什么可以完全防止这个问题，但这不是一个好的解决方案——iframe 限制了小部件的大小和形状，它们发出了一个额外的服务器请求，并且它们在一些浏览器中创建了一个键盘陷阱(例如 Opera 12，其中一旦你进入了一个 iframe，你就不能跳出它)。因此，出于所有这些原因，最好避免使用 iframes。

## 走进阴影

影子 DOM 旨在解决这个问题。我不会详细介绍它是如何工作和如何使用的([还有其他文章是这样做的](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/ "Shadow DOM 101 (html5rocks.com)"))，但出于本文的目的，我将这样总结它——影子 DOM 通过创建**文档片段**来封装内容。实际上，阴影 DOM 的内容是一个*不同的文档*，它与主文档合并以创建整体的渲染输出。

事实上，一些浏览器已经使用它来呈现一些本地小部件。如果你在 Chrome 中打开开发者工具，从设置面板(右下角的 cog 图标)中选择<q>显示阴影 DOM</q> ，然后检查一个`"range"`输入，你会看到类似这样的内容:

```
<input type="range">
  #document-fragment
    <div>
      <div pseudo="-webkit-slider-runnable-track">
        <div></div>
      </div>
    </div>
</input>
```

但是你不能通过 <abbr title="Document Object Model">DOM</abbr> 访问这些元素，因为它们是隐藏的:

```
alert(input.firstChild);		//alerts null
```

影子内容大致类似于不同域中的 iframe 文档——<abbr title="Document Object Model">DOM</abbr>可以看到 iframe，但看不到其中的任何内容。

所以因为它是隔离的，用户不可能不小心弄坏它，不存在与你使用的任何类或 id 发生命名冲突的可能，主页上的 <abbr title="Cascading Style Sheets">CSS</abbr> 也完全不会影响它。

听起来很棒，不是吗？

## 陷入黑暗

但是等等……如果所有的内容都不在 <abbr title="Document Object Model">DOM</abbr> 中，那不就意味着它也不会暴露给可访问性<abbr title="Application Programming Interfaces">API</abbr>吗？

对，就是这个意思。

基于浏览器的访问技术无法访问您放在影子 DOM 中的任何内容，例如屏幕阅读器。搜索引擎也无法使用它，但脚本内容总是如此。然而，屏幕阅读器是不同的——它们是支持脚本的设备——因此它们可以访问脚本内容。

但不是这个内容！

当然，说明书并不是不知道这种划分。本质上，它假设了包含文本内容或信息属性的元素与那些仅仅是空盒子来创建可视部分的元素(如`"range"`输入的 thumb)之间的区别。让我们把这些称为<q>内容元素</q>和<q>效用元素</q>。

那么，小部件在这两者之间有如此明显的区别有多频繁呢？对于`"range"`输入的例子，这是显而易见的，但是所有的滑块都是这样构建的吗？我最近为一个可访问的视频播放器编写了一个 slider 小部件，它的标记如下所示:

```
<label for="slider-thumb">
  <button type="button" id="slider-thumb" 
    role="slider" aria-orientation="horizontal"
    aria-valuemin="0" aria-valuemax="120" 
    aria-valuenow="75" aria-valuetext="Time: 01:15">
    <span></span>
  </button>
</label>
```

滑块中唯一可以放入影子 DOM 的部分是`<button>`中的`<span>`。`<button>`本身是重要的内容，具有 <abbr title="Accessible Rich Internet Applications">ARIA</abbr> 属性，为屏幕阅读器和其他访问技术提供动态信息。

为了使用 Shadow DOM，我们必须将所有的 <abbr title="Accessible Rich Internet Applications">ARIA</abbr> 属性移到外部`<label>`，赋予它`tabindex`，然后将 Shadow DOM 用于内部元素。但是那将是*不太容易访问的*，因为我们将失去原生语义(例如，标签的`for`属性不再产生有效的关联)，并且它将是*不太有用的*，因为它意味着小部件不能提交任何表单数据(因此我们将需要一个单独的表单控件，比如一个隐藏的输入)。

但是，即使这样很好——即使*我们制作的每一个*小部件在内容和实用元素之间有一个清晰而容易的区分——T2——小部件的内容部分仍然没有被封装；它仍然容易受到命名冲突和不想要的 <abbr title="Cascading Style Sheets">CSS</abbr> 继承的影响。

我们都知道有些人不会理解或尊重这种区别。人们将使用 Shadow DOM 作为内容，并使用它来生成全新一代的不可访问的 web 应用程序。

在研究这个问题时，我阅读了许多关于影子 DOM 的其他文章，它们都做了同样的事情——它们都停下来指出，你不应该将内容放在影子 DOM 中，然后紧接着它们说，<q>但是我们不要担心那个</q>。

太棒了。一个无聊的警告就打发了一群用户！

但是让我们更友好一点，嘿。假设文章例子不能用那些术语来判断。让我们假设每个使用 Shadow DOM 的人都会经过适当的考虑，确保他们只将它用于实用元素，而不是内容。

有了那个需求， **Shadow DOM 只提供了一半的解决方案**；一半的解决方案根本不是解决方案。

## 走进光里

在我看来，暗影 DOM 的整个概念都是错误的。这是一种过度设计的方法，并没有真正解决问题，任何使用文档片段的方法都会有同样的缺陷——只要有必要区分可访问和不可访问的元素。

我们真正需要的是概念上的对立——一种定义**样式封装的子树的方式，这些子树的*仍然是文档***的。

换句话说，我们有一个只有浏览器可以遍历的单个文档，而不是多个只有浏览器可以遍历的文档。

这可以用一个简单的元素属性来表示:

```
<div encapsulated="encapsulated">
```

<abbr title="HyperText Markup Language">HTML</abbr> <abbr title="Document Object Model">DOM</abbr> 对此没有什么不同的解释——它只是一个具有非呈现属性的元素，和其他元素一样。然而 <abbr title="Cascading Style Sheets">CSS</abbr> <abbr title="Document Object Model">DOM</abbr> 会将它解释为一种文档片段，实际上是说**元素和它里面的一切都不是从更高的作用域**继承的。

我们已经可以做相反的事情——对一个子树定义样式——或者使用后代选择器，或者如果你真的需要，使用`[<style scoped>](http://html5doctor.com/the-scoped-attribute/ "The scoped attribute (html5doctor.com)")`(尽管我个人会避免这样做，直到它作为一个`<link>`属性可用，因为`<style>`元素破坏了内容和表示的分离)。

为了使用封装的属性，我们仍然可以使用更好的方法来管理和模板化实用程序元素，但是 <abbr title="HyperText Markup Language">HTML</abbr> 不是这样做的*错误的地方*。实际上，我们根本不应该定义空元素——它们只是功能上的需要，因为我们没有其他方法来定义表示子树——所以这种能力应该添加到 <abbr title="Cascading Style Sheets">CSS</abbr> 中。

换句话说，**单个元素应该可以定义*任意数量*的伪元素**，并且伪元素本身也可以定义伪元素。大概是这样的:

```
#mywidget::after
{
}
#mywidget::after + ::element
{
}
#mywidget::after > ::element
{
}
#mywidget::after > ::element + ::element
{
}
```

这将创建一个虚拟子树，如下所示:

```
<div id="mywidget" encapsulated="encapsulated">
  Text content
  <after>
    <element></element>
    <element></element>
  </after>
  <element></element>
</div>
```

在 <abbr title="Cascading Style Sheets">CSS</abbr> 中定义这种东西将意味着一种清晰而固有的区别，没有开发人员会不明白——内容在 <abbr title="HyperText Markup Language">HTML</abbr> 中，呈现在 <abbr title="Cascading Style Sheets">CSS</abbr> 中，就应该是这样。

我们是否会得到我所建议的任何东西，还有待观察。但与此同时，我只能敦促你记住绝对的区别——**除了不传达任何信息的空元素**之外，不要对*任何东西使用影子 DOM。如果你想听我的最佳建议，那就别费心了。*

## 分享这篇文章