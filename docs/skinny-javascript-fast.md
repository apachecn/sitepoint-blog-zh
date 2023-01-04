# 瘦 JavaScript——快！

> 原文：<https://www.sitepoint.com/skinny-javascript-fast/>

**从你的网页浏览器最喜欢的书签中启动的 JavaScript 程序可以实现惊人的特效。您可以放大字体、改变颜色，甚至显示隐藏的表单域值。这些“[书签](http://www.www.bookmarklets.com)”在一些浏览器(IE5)中可能长达 2000 个字符，而在其他浏览器(IE6)中可能只有 500 个字符。最令人印象深刻的小书签就在你的浏览器中动态构建自己的 HTML、CSS 和 JavaScript。我曾经感叹 HTML 是如此庞大…直到我终于做了些什么！**

我写了一个 JavaScript [库](http://www.freewebs.com/ca_redwards/html.js),使你能够使用极其紧凑的 JavaScript 代码来呈现大型 HTML 和 CSS 结构。虽然这个紧凑的库中塞满了数百个支持函数(当 www.brainjar.com 的“crunchinator”删除了注释和空白时，这个库的重量不到 5K)，但所有函数都是以你已经知道的 HTML 标签、属性和层叠样式表(CSS)属性命名的。事实上，只需要学习六个类别:

1.  便利(`write`、`status`、`alert`……)
2.  封装内容的容器(`DIV`、`SPAN` …)
3.  “空”的标签(`IMG`、`INPUT` …)
4.  属性(`NAME`，`TYPE` …)
5.  属性(`color`，`font` …)内嵌或嵌入样式
6.  实体(`nbsp`、`quot`……)

HTML 生成器(#2-4)为您返回带有小写标签/属性名和引号文字的 xml 就绪代码。上面引用的例子使用了它们相应的 JavaScript 名称。

容器(#2)在字符串上被调用，就像 JavaScript 的内置粗体函数一样。标签(#3)和属性(#4)是在窗口对象上隐式调用的，因此根本不需要点符号。

```
HREF('http://www.yahoo.com')

yields:

 href="http://www.yahoo.com"

TITLE('Yahoo!')   

yields:

 title="Yahoo!"

The leading space is not a typo.  It just anticipates concatenation with other attributes for easy inclusion into tags and containers (#3-4).

'Yahoo!'.A(HREF('http://www.yahoo.com')+TITLE('Yahoo!'))    

yields:

<a href="http://www.yahoo.com" title="Yahoo!">Yahoo!</a>
```

此时，您可能会想，“JavaScript 代码与预期的 HTML 大小完全相同！”我会谈到这一点，但首先…

```
IMG(SRC('images/yahoo.gif')+ALT('Yahoo!')) 

yields:

<img src="images/yahoo.gif" alt="Yahoo!" />
```

```
HR()

yields:

<hr />
```

“大不了！”你说。“只保存一两个字符？”是的，但是容器(和标签*)函数也可以在数组上调用。这包含了每个数组元素。链式函数可以包装早期的构造。

```
[1,2,3].TD().TR().TABLE()     

yields:

<table><tr><td>1</td><td>2</td><td>3</td></tr></table>

Combine this with a few variables, and you can see why it works so well.

```
b=INPUT(TYPE('button')+NAME('day')); 

w=[b,b,b,b,b,b,b].TD(); 

c=[w,w,w,w,w,w].TR().TABLE();

This construct is actually the basis of a JavaScript calendar  component I built.  In just three statements, c gets assigned 1875 characters of HTML! "标题是链接属性(#4)还是文档标题(#2)？"嗯，都是！

`TITLE('hint')`
yields:
 `title="hint"`

`'home'.TITLE()`
yields:
`<title>home</title>`

同样，`STYLE`可以定义一个内联样式属性(#4)或者包含嵌入的 CSS 代码(#2)。说到 CSS 代码...

大写的属性函数(#4)和小写的属性函数(#5)都在 window 上被隐式调用。为了避免与减法混淆，带连字符的属性名在它们的 JavaScript 函数名中使用下划线(注意:因为 top 是一个预先存在的浏览器对象，所以我使用`_top`作为这个关键 css 属性的组合)。

```
color('red')      

yields:

 color: red;

width(25)         

yields:

 width: 25;

In the earlier calendar example, the buttons may not be square.  Using attribute-like css support, it's easy to inline the exact button dimensions.

```
x=25;  

s=WIDTH(x)+HEIGHT(x)+STYLE(width(x)+height(x));  

b=INPUT(TYPE('button')+NAME('day')+s);  

w=[b,b,b,b,b,b,b].TD();  

c=[w,w,w,w,w,w].TR().TABLE();

This bit of JavaScript now generates 4185 characters of flawless HTML!  Some browsers will accept the WIDTH and HEIGHT attributes right inside of the button's INPUT tag (NS4.x), yet others require the width and height in an inline STYLE.  Having both makes this code cross-browser.  Also, variable x makes it trivial to resize the buttons.  You might prefer x=30.

Alternatively, these styles could be embedded.  A style function called on a css selector string (or array) accepts a properties parameter.

```
x=25;  

s='table tr td input'.style(width(x)+height(x)).STYLE();    yields  

<style>table tr td input{ width: 25; height: 25; }</style>
```

如果重要的话，这将减少生成输出的大小。但是一定要在渲染日历`c`之前嵌入样式表`s`！或者您更喜欢外部样式表？

`LINK(REL('stylesheet')+TYPE('text/css')+HREF('cal.css'))    yields  

<link rel="stylesheet" type="text/css" href="cal.css" />`

内嵌、嵌入或外部。不管你怎么发展，我都支持。

HTML 实体(#6)实际上不是函数。将它们视为字符串常量。你会发现 quot 和 acute 可以帮助你构建那些语法上“具有挑战性”的内容字符串。

`muse='Francis Edward Smedley: '.B();

muse+=(quot+'All'+acute+'s fair in love and war.'+quot).I();`

XML 爱好者会很高兴地知道，只需调用库的最终语句中的`HTML()`构造函数就可以添加额外的函数。它的三个参数的指定非常简单:在第一个参数中，容器/标记/属性项(由空格分隔)的大小写是大写、正确还是小写，以产生适当的函数。在第二个参数中，css 属性系列(用分号分隔)后面附加了冒号及其从属项(用逗号分隔)。最后，第三个参数列出命名实体(用空格分隔)。

`HTML('LANGUAGE COPYRIGHT DESCRIPTION LINK');

HTML('URL IMAGE ITEM CHANNEL RSS version');

rss='My Blog'.TITLE();

rss+='en-us'.LANGUAGE();

rss+='Copyright 2003 by Richard Renfrow.'.COPYRIGHT();

rss+='Favorite RSS feeds'.DESCRIPTION();

rss+='list.rss'.LINK();

img='Me!'.TITLE()+'myPic.jpg'.URL()+'home.html'.LINK();

rss+=img.IMAGE();

itm='Blog'.TITLE()+'RSS'.DESCRIPTION()+'blog.rss'.LINK();

rss+=itm.ITEM();

rss=rss.CHANNEL().RSS(VERSION(0.92));`

最后但同样重要的是，便利函数(#1)使得编写字符串、在状态行中显示字符串或弹出警告变得非常容易。在开发过程中，您可能会在 JavaScript 代码中插入几个`.alert()`调用，以查看构造是如何构建的。为了获得更好的交互性，您可以提示输入内容或简单地确认中间结构。上面的最后三行可以这样扩充...

`do {  

itm='Blog'.prompt('Blog title?').status().TITLE();  

itm+='RSS'.prompt('Blog description?').DESCRIPTION();  

itm+='blog.rss'.prompt('Blog feed url?').LINK();  

rss+=itm.ITEM().confirm();  

} while (confirm('Add another?'));  

rss=rss.CHANNEL().RSS(VERSION(0.92)).prompt('paste as list.rss');`

*当在数组上调用标记函数时，所需的标记位于每个数组元素之前。为了简单起见，我选择这样做来标记一个水平单选按钮组。

`['daily','weekly','monthly'].INPUT(TYPE('radio')+NAME('frequency'))`

尽情享受吧！！

## 分享这篇文章

```

```

```

```