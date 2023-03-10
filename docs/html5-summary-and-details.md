# HTML5 快速功能:概要和详细信息

> 原文：<https://www.sitepoint.com/html5-summary-and-details/>

上周，WebKit 团队增加了对 HTML5 `details`和`summary`元素的支持。这可能会让你们中的一些人感到疑惑:“好吧，但是什么是*`details`和`summary`元素？”*

 *很公平。不像性感的`article`和`section`，或者功利主义的新输入类型，HTML5 中有相当多的新特性是大多数开发者不知道的。所以让我们来看看最新的浏览器支持:`summary`和`details`。

## 可扩展的盒子

许多网站和应用程序的一个共同特征是可扩展的信息框。想象一个标题列表，点击它会展开一个小盒子，里面有关于这个主题的更多信息。在过去，这可以用多种方式标记，可能使用 hx 元素和 div。然后使用 JavaScript 来处理展开和折叠。

以这个例子为例，来自 WordPress 文章编辑页面:

[![](img/6461e97a07fb1f57cdb00b3c1cfc0f06.png)](https://www.sitepoint.com/html5-summary-and-details/)

WordPress 使用了一系列 div 来实现这一点:

```
<div id="commentstatusdiv">
<div title="Click to toggle"><br></div><h3><span>Discussion</span></h3>
<div>
  <input type="hidden" value="1" name="advanced_view">
  <p>
    <label for="comment_status"><input type="checkbox" checked="checked" value="open" id="comment_status" name="comment_status"> Allow comments.</label><br>
    <label for="ping_status"><input type="checkbox" value="open" id="ping_status" name="ping_status"> Allow trackbacks and pingbacks on this page.</label>
  </p>
</div>
</div>
<div id="authordiv">
<div title="Click to toggle"><br></div><h3><span>Author</span></h3>
<div>
  <label for="post_author_override">Author</label>
  <select id="post_author_override" name="post_author_override">
    <option>Aaron Boodman</option>
    ...
    <option>Zak Ruvalcaba</option>
  </select></div>
</div>
```

当然，这种标记并没有什么问题。它可能不是超级语义的，但是每一部分都用一个标题清晰地描述，所以它非常有用和容易理解。

但这种类型的信息框是网站和 web 应用程序的一种非常常见的行为，目前需要 JavaScript 来实现，因此它是 HTML5 中标准化为基于浏览器的功能的理想目标。

## HTML5 带来了什么

HTML5 规范为此添加了一组元素:`details`和`summary`。

从规格来看:

> details 元素表示一个 disclosure 小部件，用户可以从中获得附加信息或控件。
> 
> 元素的第一个 summary 元素子元素(如果有)表示详细信息的摘要或图例。如果没有子汇总元素，用户代理应提供自己的图例(如“详细信息”)。

因此，在 HTML5 中，上述示例可以重写如下:

```
<details>
  <summary>Discussion</summary>
  <p>
    <label for="comment_status"><input type="checkbox" checked="checked" value="open" id="comment_status" name="comment_status"> Allow comments.</label><br>
    <label for="ping_status"><input type="checkbox" value="open" id="ping_status" name="ping_status"> Allow trackbacks and pingbacks on this page.</label>
  </p>
</details>
<details>
  <summary>Author</summary>
  <p>
    <label for="post_author_override">Author</label>
    <select id="post_author_override" name="post_author_override">
      <option>Aaron Boodman</option>
      …
      <option>Zak Ruvalcaba</option>
    </select>
  </p>
</details>
```

给定这个标记，浏览器应该只显示单词“讨论”和“作者”当这些术语中的任何一个被点击时，相关的摘要就会打开并显示出来。

summary 元素有一个`open`属性，用于指示摘要是“打开的”，或者是展开的。这是一个布尔属性，所以你可以像使用`<summary open>`一样使用它，或者，如果你坚持 XHTML 风格的语法，可以使用`<summary open="open">`。对于上面的例子，如果您希望两个框都默认打开(就像它们在 WordPress 界面中一样)，您只需要将这个属性添加到两个`details`元素中。

正如我在前面提到的，目前只有 WebKit 的 SVN 主干支持这个元素。其他浏览器将显示所有内容，而不管`open`属性，并且不允许您通过点击摘要来切换细节的可见性。

## 多填充支架

如果只有最先进的 SVN 版本的渲染引擎支持这个元素，为什么还会有人想使用它呢？

首先，对 HTML5 和 CSS3 特性的支持正以极快的速度被添加到大多数浏览器中。一旦这个从 WebKit 转移到 Chrome，Mozilla 和 Opera 也不会落后太多。因此，好处很快就会开始显现。

但主要原因是没有坏处。如果您已经使用 JavaScript 在您的站点上提供了这种功能，您可以继续这样做。除此之外，现在您只能在不支持的浏览器上使用 JavaScript，并为您的尖端用户依赖本机实现。

Mathias Bynens 有一个快速的 jQuery 脚本来检测对细节的支持，并为旧浏览器添加支持。查看描述他的解决方案的[博文，以及伴随的](http://mathiasbynens.be/notes/html5-details-jquery)[演示](http://mathiasbynens.be/demo/html5-details-jquery)。这是双赢的:你为每个人提供了功能，通过依赖更快、更标准的原生功能，为超现代的浏览器做得更好，并且避免用类似`class="expandable open"`的东西来混淆你的标记。

## 包裹

你已经熟悉细节和概要了吗，还是第一次听说？你会在你的网站上用 Mathias 的 polyfilly 测试它们吗？还是暂时坚持 divs？请在评论中告诉我。

## 分享这篇文章*