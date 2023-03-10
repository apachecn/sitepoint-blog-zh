# 更好的文本语义的 20 个 HTML 元素

> 原文：<https://www.sitepoint.com/20-html-elements-better-text-semantics/>

语义 HTML 不仅可以帮助 web 设计人员和开发人员在呈现的页面中传达含义，还可以通过用于编码该页面的标签和元素来传达含义，从而潜在地改善可访问性、搜索引擎索引，甚至人类的理解。

作为一个术语，语义或语义学与语言中的意义有关。如果某样东西更具语义性，那么根据定义它就更有意义。语义 HTML 是简单的标记，它在传达某种意图方面更有效。

## 语义 HTML 传达了更多的含义

以`<div>`元素和`<header>`元素之间的差异为例。前者描述了 HTML 文档中一组通用的块内容，而后者更具体地表示一组介绍性内容，可能包括导航元素。

```
<div id="top">Welcome</div>
<header>Welcome</header>
```

在浏览器中，样式表使得相同地呈现`<div>`和`<header>`元素成为可能。但当搜索引擎机器人或屏幕阅读器解析代码时，语义就变得很重要，因为这些工具可能会以不同的方式对待这两个标签，包括为一个标签的内容赋予不同的权重或值。

当意义上的差异很微妙或者标签的使用明确暗示了一种意义时，语义变得更加重要。

## `<s>`元素

[`<s>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-s-element)表示不再相关、准确或适用的内容。它可能会显示电子商务网站上的价格变化、菜单上售罄的商品或不再相关的广告。

```
<h1>Widget for Sale</h1>
<p><s>Reg. Price $19.99</s> Now $9.99</p>

<s>Grilled Cheese Sandwich</s> Sold Out

<s>Three Bedroom Apartment $1,000 Monthly</s>
```

元素不应该用来表示对文档的编辑，比如删除的文本。默认情况下,`<s>`会在其内容上加一条水平线(删除线)。

## `<ins>`和`<del>`元素

[`<ins>`](https://html.spec.whatwg.org/multipage/semantics.html#the-ins-element) 和 [`<del>`](https://html.spec.whatwg.org/multipage/semantics.html#the-del-element) 元素共同描述对 HTML 文档的插入和删除。每个元素支持两个属性。`cite`属性存储了一个指向变更解释的 URL。`datetime`属性表示何时进行了更改。

默认情况下，`<del>`的内容将以删除线显示，而`<ins>`的内容以下划线呈现。

```
<h1>Meeting Agenda</h1>

<ul>
    <li>Discuss Sales Plan</li>
    <li><del timestamp="2014-10-12T18/02-17/00">Review Q3 Marketing</del></li>
    <li><ins cite="//sitepoint.com/change.html">Review Q3 Marketing</ins></li>
</ul>

<p>The meeting will be on <del>Thursday</del> <ins>Friday</ins> afternoon.</p>
```

## `<cite>`元素

不要与各种元素的`cite`属性相混淆，就像上面的`<ins>`和`<del>`、[、`<cite>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-cite-element)本身就是一个标签，代表对一些创造性作品的引用，如文章、书籍、漫画、图片、诗歌、歌曲、视频等。

`<cite>`元素的内容应该是被引用作品的标题、作者或艺术家的名字，或者是被引用作品的 URI。

```
<p>I really like Armando’s article, 
<cite>An Introduction to HTML Imports</cite>.</p>
```

## `<q>`元素

指定简短、内嵌、引用的材料，[`<q>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-q-element)包括必要的标点符号——引号——和用于包含 URL 的`cite`属性。重要的是要记住，`<q>`标记是用于行内引号的，而`<blockquote>`元素更适合于大的独立引号。

```
<p>I had not been aware, but according to <cite>Richard Kerr</cite>,
<q cite="//www.sciencemag.org/content/340/6136/1031">Most robotic 
missions to Mars have failed</q>.</p>
```

## `<abbr>`和`<dfn>`元素

HTML 的[缩写](https://html.spec.whatwg.org/multipage/semantics.html#the-abbr-element)和[定义](https://html.spec.whatwg.org/multipage/semantics.html#the-dfn-element)元素经常一起使用来引入一个缩写或首字母缩略词，特别是当这个缩写或首字母缩略词包含在一个相对复杂、较长或技术性的文档中时。

```
<p>The <dfn><abbr title="Internet Corporation for Assigned Names and Numbers">ICANN</abbr></dfn> 
is an international, not-for-profit organization responsible for managing web addresses.</p>
```

在上面的例子中，浏览器、搜索机器人和查看标记的人会认出“互联网名称与数字地址分配机构”是正在定义的短语,“ICANN”是该短语的首字母缩写。同样，根据标准，包含`<dfn>`标签的段落、列表或部分也必须包含相关的定义。

## `<code>`元素

元素 [`<code>`](https://html.spec.whatwg.org/multipage/semantics.html#the-code-element) 用于指示计算机代码中不应该被执行的部分，而是应该被呈现为可读代码。

```
<p>In jQuery, <code>.attr()</code> retrieves the value of an attribute of the 
first element in the matching set of elements.</p>
```

元素`<code>`也可以与[元素`<pre>`和](https://www.sitepoint.com/everything-need-know-html-pre-element/)一起使用来创建代码块。

```
<pre><code>
function loadAudio( object, url) {
    var request = new XMLHttpRequest();
    request.open('GET', url, true);
    request.responseType = 'arraybuffer';

    request.onload = function() {
        context.decodeAudioData(request.response, function(buffer) {
            object.buffer = buffer;
        });
    }
    request.send();
}
</code></pre>
```

## `<samp>`元素

用于识别来自计算机系统、应用程序或类似程序的样本输出， [`<samp>`标签](https://html.spec.whatwg.org/multipage/semantics.html#the-samp-element)的内容应该是来自某个计算机交互的引用。

```
<p>If the upload fails, the system will notify the users that 
<samp>the file was not uploaded</samp>.</p>
```

## `<kbd>`元素

如果设计人员或开发人员想要传达用户在键盘交互过程中应该或确实输入了什么，[`<kbd>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-kbd-element)将清楚地(语义上)标识该内容。例如，`<kbd>`元素可以用来描述特定的按键组合。当`<kbd>`标签被嵌套时，它们代表单个键或单元。

```
<p>To capture an image of the screen on a Macbook, simultaneously press 
<kbd>Shift</kbd>+<kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>3</kbd>.</p>
```

元素也可以用于键盘输入，比如移动设备上的语音输入。

## `<var>`元素

[`<var>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-var-element)代表数学或计算机编程环境中的变量。

```
<p><var>x</var> = 13</p>

<p>A second variable, <var>pad</var>, is assigned to the pad element object. 
jQuery allows for this sort of concatenated selector.</p>
```

## `<data>`元素

这个 HTML 元素与`value`属性一起，将一些内容与该内容的意义的机器可读翻译相关联。实际上，[`<data>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-data-element)旨在与脚本结合使用。

在这个非常简单的例子中，传递了单词“11”的数值。

```
<data value="11">eleven</data>
```

该标签也可以用于相对更复杂的关联。在下面的例子中，国际标准书号与每本书的书名相关联。

```
<ul>
    <li><data value="978-0987467423">Jump Start Rails</data></li>
    <li><data value="978-0992279455 ">AngularJS: Novice to Ninja </data></li>
</ul>
```

## `<time>`元素

类似于`<data>`元素，[`<time>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-time-element)可以用来传递机器可读的日期和时间信息及其内容。

```
<p>She was born on her grandpa's birthday, 
<time datetime="2014-10-22 19:00">October 22, 2014</time>.</p>
```

奥雷里奥在他最近的 SitePoint 帖子中有关于`<time>`元素的所有细节。

## `<ruby>`、`<rt>`和`<rp>`元素

拼音注释是简洁的文本串，通常用作基于字符的语言(如日语、朝鲜语或中文)的发音指南。

[`<ruby>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-ruby-element)是 HTML 添加 Ruby 注释的方式。这些发音辅助工具通常在相关字符的上方上标出来。

`<ruby>`元素通常与[、`<rt>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-rt-element)和[、`<rp>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-rp-element)一起使用，前者描述拼音注释中单个字符的发音，后者为不支持拼音注释的浏览器创建备用标点。

Web 超文本应用技术工作组(WHATWG)有几个优秀的 Ruby 注释示例，包括如下。

```
<ruby>君<rt>くん</ruby><ruby>子<rt>し</ruby>
は<ruby>和<rt>わ</ruby>して<ruby>同<rt>どう</ruby>ぜず。
```

为了更好地理解这个例子，只考虑第一个 Ruby 注释。

```
<ruby>君<rt>くん</ruby>
```

如果正确渲染，`<rt>`标签后的字符`くん`，将作为上标出现在`君`字符上方。

如果担心与旧浏览器的兼容性，`<rp>`元素可以添加备用标点。

```
<ruby>君<rp>(</rp><rt>くん </rt><rp>)</rp></ruby>
```

在上面的例子中，不支持 Ruby 注释的浏览器会将发音显示为括号，`君(くん )`。

## `<sup>`和`<sub>`元素

分别代表上标和下标，[`<sup>`和`<sub>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-sup-element)表示“印刷惯例”，不应该简单地用于上标和下标复制，否则可以只用 CSS 和一个通用的`<span>`标签来完成。

也许这些标签最常见的例子是某些法语缩写，通常包括上标。具体来说，法语单词 *compagnie* ，在英语中是“公司”，经常被缩写为 C <sup>即</sup>。

```
<span lang="fr"><abbr>C<sup>ie</sup></abbr></span>
```

## `<mark>`元素

如果 HTML 有一个大的黄色荧光笔，它将是[的`<mark>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-mark-element)。这个标签突出显示内容，因为它在某些上下文中有特殊的相关性。例如，如果想要在副本的某个部分显示与搜索查询匹配的特定关键字，那么`<mark>`元素将是合适的标签。在下面的示例中，搜索了单词“audio”

```
<p>Web <mark>Audio</mark> uses an <mark>Audio</mark>Context interface to 
represent <mark>Audio</mark>Nodes. Within the <mark>Audio</mark>Context 
an <mark>audio</mark> file, as an example, is connected to a processing node, 
which in turn, is connected to a destination like the speakers on your laptop. 
Each node in the <mark>Audio</mark>Context is modular so that a web developer 
can plug (or unplug) nodes like a toddler snapping Lego blocks in place to 
build relatively more complicated structures.</p>
```

在 blockquote 中，`<mark>`元素可用于添加原始上下文中没有的强调。

```
<blockquote>I included the jQuery JavaScript library via Google’s content 
delivery network. <mark>jQuery is in no way required</mark> for the Web 
Audio API, but its powerful selectors will make it a lot easier to 
interact with the HTML pads. I am also linking to a local JavaScript file 
that will contain the code for working with the Web Audio API.</blockquote>
```

## `<wbr>`元素

浏览器——无论是运行在笔记本电脑上还是智能手机上——都有一套规则来决定何时何地在副本中添加换行符。这些规则在响应式设计中尤其重要，因为页面上给定元素的宽度会因上下文的不同而有很大差异。

[`<wbr>`标签](https://html.spec.whatwg.org/multipage/semantics.html#the-wbr-element)描述了一个浏览器无法识别的断字或换行机会。这种能力对于特别长的单词、URL 或代码段非常有用。这些特别长的元素通常不会断开，会影响页面布局。

在美国，[夏威夷州的鱼](https://en.wikipedia.org/wiki/Reef_triggerfish)就有一个 21 个字母的名字: *humuhumunukunukuapua`a* 。使用`<wbr>`标签，可以向浏览器展示如何在选择的音节上拆分单词。

```
humu<wbr>humu<wbr>nuku<wbr>nuku<wbr>a<wbr>pua`a
```

## 结论

HTML 提供了许多伟大的语义标签，让网站建设者能够直接与元素交流意义。

并非所有这些元素都具有可访问性或其他好处，但至少从开发人员的角度来看，它们可以使代码更容易阅读。

你觉得这些元素有用吗？请在评论中分享你的想法。

编辑更新:我们已经将所有代码示例放入下面的 CodePen 演示中。这些显示了 Normalize.css 的默认样式:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [mjqEB](http://codepen.io/SitePoint/pen/mjqEB/) 。