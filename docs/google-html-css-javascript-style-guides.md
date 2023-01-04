# 谷歌展示了它的 HTML、CSS 和 JavaScript 风格指南

> 原文：<https://www.sitepoint.com/google-html-css-javascript-style-guides/>

您是否被迫遵守 1969 年某个时候制定的公司编码指南？或者你的开发伙伴可以自由地用嵌入的 ASCII 艺术表达他们艺术的一面吗？

我喜欢浏览风格指南。通常有一些显而易见的规则，那些看起来很奇怪的规则和一些你以前没有发现的隐藏的宝石。不幸的是，很少有公司敢于公布内部指导方针。英国广播公司在 2010 年披露了他们的文件，但是 T2 谷歌最终发布了公司内部项目使用的风格指南。

为 C++、Objective C、Python、XML 和 R 提供了规则——但是我们对 HTML、CSS 和 JavaScript 最感兴趣。这些文件非常简短。我见过太多开发者被期望学习 1001 条神秘和主观的规则，这些规则炫耀作者的知识，而不是传授有用的建议。

让我们仔细看看…

## [HTML](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml)

在 [HTML 风格指南](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml)中几乎没有惊喜:

*   将语义 HTML5 作为文本/html
*   独立的标记、样式和脚本
*   尽可能验证您的标记
*   当像`canvas`这样的技术不被支持时，使用备用方案

谷歌建议去掉每个字节:文件应该是 UTF-8 编码的，尾部的空白应该被删除，你应该避免实体引用，比如—和”。他们甚至建议省略可选结构和结束标记，例如

```
 <!DOCTYPE html>
<title>Saving money, saving bytes</title>
<p>Qed. 
```

呃。我完全赞成节省几个字节，但仍然喜欢更严格的类似 XHTML 的语法。

奇怪的是，谷歌推荐缩进两个空格而不是一个制表符。这不是你需要的空白字符数量的两倍吗？！

## [CSS](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml#CSS_Style_Rules)

样式表指南由进一步的最佳实践和字节节省技术主导:

*   使用有效的 CSS，除非处理错误或专有语法(供应商前缀)
*   使用短的 ID 和类名，但要确保它们不晦涩难懂或缺乏代表性(例如#blue-button)
*   对于较大的项目，考虑使用前缀名称，例如#xyz-help。xyz 列
*   尽可能简化选择器，例如#example 而不是 ul#example
*   使用速记语法符号
*   不要在`url()`中使用引号或零后的单位值
*   使用简短的#abc 十六进制颜色表示法，而不是#aabbcc
*   用分号结束每个声明(即使是最后一个)
*   避免浏览器黑客攻击

谷歌建议在测量中省略前导零，比如. 5em。这样可以节省一个字符，但我更喜欢更容易扫描的代码。

指南建议对应用于块的每个逗号分隔的选择器使用换行符。我倾向于同意，尽管我对使用长长的选择器字符串列表感到内疚。

最后，它规定财产申报应按字母顺序排列，例如

```
 #example
{
	border: 1px solid #000;
	border-radius: 6px;
	display: block;
	font-family: sans-serif;
	margin: 1em;
	outline: 0;
	padding: 10px;
	text-align: center;
} 
```

就个人而言，我更喜欢在相关的块中排序属性，例如块类型/尺寸、字体、文本对齐、填充、边距、颜色、背景、边框和其他属性。也许它会让一些开发人员感到困惑，但是我已经使用这个方法很多年了，并且对我很有效。

## [JavaScript](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)

可以理解的是， [JavaScript 风格指南](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)比较长，但是可以让你轻松掌握基础知识:

*   总是使用`var`来声明变量
*   总是用分号结束行
*   使用标准功能而不是非标准功能
*   使用 camelCaseNames 和大写常量，但避免使用 IE 不支持的关键字`const`
*   使用命名空间技术
*   避免`eval()`除了反序列化(奇怪的是 JSON。没有提到 Parse 作为一种替代方法)
*   避免在对象上使用`with()`，在数组上使用`for-in`
*   使用数组和对象文字，而不是更详细的声明
*   注意[真假法则](https://www.sitepoint.com/javascript-truthy-falsy/)
*   不要在你的 JavaScript 源代码中使用 IE 条件注释
*   不要修改内置对象的原型——这很遗憾，因为这是 JavaScripts 更强大的特性之一，但是你知道这会导致问题
*   小心使用闭包，不要引入循环引用
*   同样，[小心使用‘this’](https://www.sitepoint.com/javascript-this-gotchas/)

有一个不寻常的建议:不要在块中使用函数，例如:

```
 if (x) {
  var foo = function() {}
} 
```

而不是:

```
 if (x) {
  function foo() {}
} 
```

你会发现第二种语法随处可见。它工作没有问题，但它不是有效的 ECMAScript。

该文档还提到包装器对象不应该用于原语，除非您正在进行类型转换。这可能会导致意想不到的结果，例如:

```
 var x = new Boolean(false);
if (x) {
	// this code evaluates
} 
```

愚蠢的 JavaScript。

最后，该指南指出，在分隔*可能*包含 HTML 属性的字符串时，应该使用单引号(')而不是双引号(")。我到处都用双引号，我不确定我能轻易改变这个习惯。也就是说，我对静态 PHP 字符串使用单引号，所以也许我太挑剔了！

最好的建议留在文件的最后:保持一致。开发者很少会同意每一个编程准则，但是你应该遵循你自己的规则。这会让其他人更容易理解你的代码，当你六个月后再回到代码上的时候。

你同意谷歌的风格指南吗？你的公司强迫你遵循奇怪的开发实践吗？

## 分享这篇文章