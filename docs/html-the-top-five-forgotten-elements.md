# HTML:被遗忘的 5 大元素

> 原文：<https://www.sitepoint.com/html-the-top-five-forgotten-elements/>

所以你是一个前端专家，每天都在制作复杂的 CSS 布局和动态 DOM 魔法。您已经掌握了跨浏览器布局，对自己的 DOM 脚本技能很有信心，并且尽一切努力使您的页面尽可能地可用和可访问。见鬼，也许你已经使用[微格式](http://www.microformats.org/)给你的网站添加了额外的语义。

但是你还能做更多吗？一切所依赖的 HTML 基础到底有多完美？

在你宣布你美味的[网络标准蛋糕](http://www.stuffandnonsense.co.uk/archives/web_standards_trifle.html)已经准备好了之前，通读一下下面五个你可能忽略的元素。所有主流浏览器都支持它们，实现它们很容易，几乎不需要时间。

dfn

The `dfn` element is used to introduce a term to your readers. Here’s an example:

```
<p>An <dfn>array</dfn> is a single programming variable with
multiple "compartments". Each compartment can hold a value.</p>
```

浏览器默认以斜体呈现`dfn`元素，但是当然通过 CSS 的力量，你可以让它们看起来像你喜欢的任何旧的样子。例如，如果我想让它们以粗体显示，我可以这样做:

```
dfn {
  font-weight: bold;
  font-style: normal;
}
```

cite

The `cite` element is for marking up citations, for example to a magazine, book or web site. Here’s how you use it:

```
<p>The SitePoint book <cite>Build Your Own Web Site The Right 
Way</cite>, by Ian Lloyd, is a great primer for learning 
<acronym title="HyperText Markup Language">HTML</acronym> and 
<acronym title="Cascading Style Sheets">CSS</acronym>.</p>
```

忘记`cite`元素最常见的原因是因为链接到相关文档感觉更自然——可以是网页，也可以是你引用的书的销售页面。那也行！你可以两者都做，就像这样:

```
<p>The SitePoint book <a 
href="https://www.sitepoint.com/books/html1/"><cite>Build Your 
Own Web Site The Right Way</cite></a>, by Ian Lloyd, is a great 
primer for learning <acronym title="HyperText Markup Language">HTML</acronym> and 
<acronym title="Cascading Style Sheets">CSS</acronym>.</p>
```

var

The `var` element can be used to mark up a variable, when talking about programming code. By default it renders in a monospace font in most browsers. Here’s an example:

```
<p>For each iteration in the following Ruby code, the 
<var>car</var> variable is set to the current element of 
the array.</p>
```

samp

The `samp` element is used to mark up sample output, such as the output to your screen when running a script. Here’s how it’s used:

```
<p>When I ran the program from the command line, it just 
printed <samp>File Not Found</samp> to the screen.</p>
```

kbd

The `kbd` element should be used to indicate keyboard input required by the user. Pretty straightforward:

```
<p>If you need help at any time, hit <kbd>F1</kbd> to display 
the online help menu.</p>
```

这就是全部了。简单吧。

虽然你可能认为这种详细程度的回报与所需的努力不相称，但请记住，有很多原因可以说明为什么在页面中添加语义意义是有意义的。

*   您的页面将自动变得(潜在地)更容易访问，因为辅助技术在解释其内容时将拥有更多关于您页面的元数据。
*   你可能会看到搜索引擎优化排名有所提高，因为搜索引擎机器人将能够更好地理解你页面上的内容。
*   如果合适的微格式出现的话，你将比大多数人更容易利用它(或者语义网，如果这种事情真的发生的话)。

在标记中包含这些被遗忘的元素既快捷又容易，并且为页面增加了额外的语义丰富性。

## 分享这篇文章