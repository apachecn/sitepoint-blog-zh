# 通过书签更好地生活

> 原文：<https://www.sitepoint.com/bookmarklets-2/>

Bookmarklets 是一种为你的网络浏览器添加功能的简单方法，对于你的网络开发人员来说，它也是一个有用的补充。

在本文中，我将指出一些有用的小书签，提供构建自己的小书签的技巧，并演示一些高级技术来充分利用这些强大的工具。

bookmarklet 是嵌入在浏览器书签中的一小段 JavaScript。当书签被选中时，JavaScript 被执行。神奇的是，这个执行发生在当前页面的上下文中*。书签小程序可以访问页面的完整文档对象模型，并且可以修改它并使用其中的信息来启动新窗口或将浏览器重定向到其他站点。*

如果您以前从未尝试过 bookmarklets，那么您应该在阅读本文其余部分之前尝试一下。www.bookmarklets.com 和 www.favelets.com[都提供了广泛的书签库，杰西·鲁德曼在 www.squarefree.com/bookmarklets](http://www.favelets.com)拥有网上最全面的书签库。尤其是 Jesse 的 [Web 开发书签](http://www.squarefree.com/bookmarklets/webdevel.html)，应该是任何 Web 开发人员浏览器的必备附件。

bookmarklet 适用于所有支持 JavaScript 的现代浏览器，但有些浏览器比其他浏览器对 bookmarklet 更友好。Internet Explorer 6 for Windows 的书签大小受到令人恼火的 508 个字符的限制，这严重减少了该浏览器可用的书签范围，尽管我稍后将介绍的技术确实提供了部分补救措施。为了获得 bookmarklet 的全部好处，我建议您使用基于 Mozilla 的浏览器，比如优秀的 Firefox，它还附带了许多有用的工具来帮助 bookmarklet 开发。

然而，bookmarklet 开发的魅力之一是避免了经常困扰严肃 JavaScript 开发的跨浏览器问题:为特定浏览器开发 bookmarklet(尤其是供个人使用的 bookmarklet)没有任何问题，因此开发人员可以做浏览器允许他们做的任何事情。

##### 常见的书签变体

如果您浏览过前面提到的任何网站，您应该对 bookmarklets 可以提供的大量功能有所了解。最简单和最常见的 bookmarklet 是简单导航 bookmarklet，它获取当前页面的 URL 并将其传递到另一个站点。典型的例子是“验证”书签，它将用户重定向到当前页面的在线 HTML 或 CSS 验证服务。

这些书签创建起来很简单，但是可以用于一些非常有用的目的。我为基于网络的内容管理系统支持的网站创建了“编辑这个页面”书签，从中获得了很多乐趣。许多这样的系统在公共站点上的页面的 URL 中嵌入一个 ID，直接对应于在站点管理系统中用于编辑该页面内容的表单的 URL 中使用的 ID。“edit this page”书签小程序提取当前页面的 URL，并使用它立即将用户重定向到该页面的编辑界面。

例如，想象一个具有如下 URL 的站点:

```
http://www.site.com/articles/123
```

该网站还有一个受密码保护的管理界面，在以下地址提供“编辑页面”界面:

```
http://www.site.com/admin/edit-article?id=123
```

一个“编辑这个页面”的书签小程序可以为上述网站实现如下:

```
javascript:document.location='http://www.site.com/admin/ 
edit-article?id='+document.location.href.split('/').pop()
```

如果您对网站上使用的内容管理系统有一些控制，这个概念可以大大扩展。例如，在一个不在 URL 中公开文章内部 ID 的站点中，您可以将 ID 放在 HTML meta 标记中，然后通过 DOM 将该标记提供给“编辑此页面”bookmarklet。

许多 bookmarklets 以某种方式修改当前页面。常见的例子包括用于“切换”恼人事物的书签小程序，例如不想要的 Flash 动画，甚至是大小适合横幅广告的常见尺寸的图像。这些可能很有趣，但通常受限于每次加载页面时都需要手动激活。

更有用的是 bookmarklets，它可以帮助 Web 开发人员分析页面结构，甚至“实时”修改页面结构。我个人最喜欢的是杰西·鲁德曼的“测试风格”、“编辑风格”和“祖先”，来自他的 [Web 开发](http://www.squarefree.com/bookmarklets/webdevel.html)系列。后者显示了指向鼠标光标下的页面部分的 HTML 元素层次结构，这对于弄清楚如何将 CSS 应用于页面非常有用。前两个允许当前页面的 CSS 被“实时”修改，提供对潜在设计变化的即时反馈。

我的[图像拖动](http://simon.incutio.com/archive/2003/02/13/bookmarkletFixed) bookmarklet for Mozilla 使页面上的每一个非背景图像都是“可拖动的”，这在考虑调整页面设计时也是一个帮助。

一个有用但经常被忽视的 JavaScript 技巧是整个 HTML 页面可以嵌入到一个 bookmarklet 中。尝试在浏览器的地址栏中直接输入以下内容:

```
javascript:'<h1>This is HTML</h1>'
```

浏览器应该显示字符串中呈现的 HTML。之所以这样做，是因为字符串是作为表达式计算的，其结果将显示在当前浏览器窗口中。同样的技巧甚至可以用来把你的浏览器变成一个过度指定的计算器:

```
javascript:1 + 4;
```

以这种方式编写嵌入整个页面的 bookmarklets 有时会很有用，特别是当它们需要比简单的`confirm()`和`prompt()`框更复杂的用户界面时。

##### 辅助文本输入

我在网上花了相当多的时间盯着文本框。我更新的三个博客都是通过 textareas 维护的，我在工作中开发的网站以及我参加的各种在线论坛也是如此。文本区域无处不在。它们也不是处理文本的最佳方式，尤其是与专用的文本编辑软件相比。Bookmarklets 可以大大减轻处理文本区域的痛苦，并且可以为它们提供有用的附加功能。

我最喜欢的增强文本区的书签工具是 Jesse Ruderman 的另一个例子: [Blogidate XML 良构性](http://www.squarefree.com/archives/000033.html) —一个 Mozilla/FireFox 书签工具，它检查页面上每个文本区中的文本是否是良构的 XML，并相应地改变文本区的背景颜色。这对于在将 XHTML 发布到站点之前捕捉其中的简单错误非常有用。Jesse 还有一套[HTML 验证书签](http://www.squarefree.com/archives/000031.html)，它使用 WDG 验证器检查文本区域中 HTML 片段的语法。

我经常使用的另一个工具是我的 bookmarklet。这将对 textareas 中的文本应用一系列简单的转换:

1.  以*开头的行成为项目符号列表

示例:

```
= Header 

Paragraph 

* list 1 
* list 2
```

变成了:

```
<h4>Header</h4> 

<p>Paragraph</p> 

<ul> 
 <li>list 1</li> 
 <li>list 2</li> 
</ul>
```

IE 有一个更短的版本。这个版本牺牲了标题支持来适应 508 个字符的限制: 。

展开 HTML 速记:
————
`javascript:(function() { var tas = document.getElementsByTagName('textarea'); for (var i = 0; i < tas.length; i++) { var ta = tas[i]; var text = ta.value.replace(/(rn|r|n)/g, 'n'); var paras = text.split(/n{2,}/); for (var i = 0; i < paras.length; i++) { if (/^* /.test(paras[i])) { var lines = paras[i].split('n'); for (var j = 0; j < lines.length; j++) { lines[j] = ' <li>' + lines[j].replace(/^* /, '') + '</li>'; } paras[i] = '<ul>n' + lines.join('n') + 'n</ul>'; } if (/^= /.test(paras[i])) { paras[i] = '<h4>' + paras[i].replace(/^= /, '') + '</h4>'; } if (!/^<(p|ul|li|h4)>/.test(paras[i])) { paras[i] = '<p>' + paras[i]; } if (!/</(p|ul|li|h4)>$/.test(paras[i])) { paras[i] += '</p>'; } } ta.value = paras.join('nn'); } })();`
———

展开 HTML 速记 IE:
————
`javascript:(function(){var tas=document.getElementsByTagName('textarea'),ta,t,ps,i,l,j;for(i=0;i<tas.length;i++){ta=tas[i];
t=ta.value.replace(/(rn|r|n)/g,'n');ps=t.split(/n{2,}/);for(i=0;i<ps.length;i++){if(/^* /.test(ps[i])){l=ps[i].split('n');for(j=0;j<l.length;j++){l[j]=' <li>'+l[j].replace(/^* /,'')+'</li>';}ps[i]='<ul>n'+l.join('n')+'n</ul>';}if(!/^<(p|ul|li|h4)>/.test(ps[i])){ps[i]='
<p>'+ps[i];}if(!/</(p|ul|li|h4)>$/.test(ps[i])){ps[i]+='</p>';}}ta.value=ps.join('nn');}})();`
———

未展开的源(删除空白之前)如下所示:

```
javascript:(function() {  
    var tas = document.getElementsByTagName('textarea');  
    for (var i = 0; i < tas.length; i++) {  
        var ta = tas[i];  
        var text = ta.value.replace(/(rn|r|n)/g, 'n');  
        var paras = text.split(/n{2,}/);  
        for (var i = 0; i < paras.length; i++) {  
            if (/^* /.test(paras[i])) {  
                var lines = paras[i].split('n');  
                for (var j = 0; j < lines.length; j++) {  
                    lines[j] = '  <li>' + lines[j].replace(/^* /, '') + '</li>';  
                }  
                paras[i] = '<ul>n' + lines.join('n') + 'n</ul>';  
            }  
            if (/^= /.test(paras[i])) {  
                paras[i] = '<h4>' + paras[i].replace(/^= /, '') + '</h4>';  
            }  
            if (!/^<(p|ul|li|h4)>/.test(paras[i])) {  
                paras[i] = '<p>' + paras[i];  
            }  
            if (!/</(p|ul|li|h4)>$/.test(paras[i])) {  
                paras[i] += '</p>';  
            }  
        }  
        ta.value = paras.join('nn');  
    }  
})();
```

##### 书签创建工具

你只需要一个文本编辑器就可以创建书签——或者，如果你真的有信心，你可以把它们直接输入到浏览器的“新书签”栏中。然而，对于比简单的导航书签更复杂的东西，利用专用工具是有意义的。

如果你正在使用 Firefox，你已经可以使用一些有用的工具来创建 bookmarklet。Firefox 的 JavaScript 控制台是一个非常有价值的调试工具，当编写修改页面内容的 bookmarklets 时，DOM inspector 对于浏览页面的 DOM 树是一个很好的帮助。Jesse Ruderman 为 Firefox 和 Mozilla 开发的 [shell bookmarklet](http://www.squarefree.com/bookmarklets/webdevel.html#shell) 提供了一个附加到当前页面上下文的交互式 JavaScript 提示，这是在将新技术提交给文本编辑器之前尝试它们的一个很好的方式。

虽然 bookmarklets 不能包含换行符，但是在编写大量语句时，保持源代码缩进是很重要的。My remove linebreaks bookmarklet(下图)是一个从 JavaScript 块中移除制表符、换行符和多个空格的工具。在许多情况下，这就是从一个简单的代码块创建 bookmarklet 所需要做的全部工作，尽管您必须记住在转换它之前用分号结束每一行。bookmarklet 也是嵌入书签的 HTML 页面的一个例子。

移除换行符:
————
`javascript:'<textarea rows=%2220%22 cols=%2260%22 id=%22ta%22></textarea><br><a href=%22http://%22 onclick=%22ta=document.getElementById('ta'); ta.value = ta.value.replace(/\n|\t/g, ' ').replace(/ +/g, ' '); return false;%22>Replace newlines and tabs</a>';`
———

##### 可变范围避免

bookmarklet 引入的一个潜在问题是名称空间冲突:如果您的 bookmarklet 使用或重新定义了一个已经被页面上的其他脚本使用的变量，该怎么办？避免这种情况的一种技术是使用不太可能已经在使用的随机变量名，但这会使 bookmarklet 代码更加难以阅读，并给 bookmarklet 增加不必要的长度。更好的解决方案是将 bookmarklet 创建为一个匿名函数，有自己的变量作用域。这是如何工作的:

```
javascript:(function() {  
  /* Bookmarklet code here - declare variables with 'var' before use */  
})();
```

其中的函数`() { }`部分是一个匿名函数——一个没有为其声明名称的函数。通过将函数放在括号中并在末尾添加`()`,函数一旦被创建就被执行一次，即小书签被激活的瞬间。只要匿名函数体中的变量是使用“var”关键字声明的，它们就将被限制在函数的范围内，不会干扰文档其余部分中同名的变量。

由于 JavaScript 的函数性质，您甚至可以在匿名函数中声明其他函数，而无需将它们添加到文档的全局范围中。

##### 附加更长的脚本

我前面提到过，有一种方法可以绕过 Internet Explorer 对书签长度的限制。这种方法还允许用标准缩进 JavaScript 编写 bookmarklet，而不需要将整个脚本放在一行中，这使得它对于为任何浏览器实现的更复杂的 bookmarklet 来说都是一种有用的技术。诀窍是将实际的 bookmarklet 实现作为外部托管。js 文件放在某个 Web 服务器上。然后，bookmarklet 只需要包含加载到脚本其余部分的“存根”代码——在 508 个字符的限制内，这个任务很容易完成。

下面是加载存根 bookmarklet 代码，为了便于阅读，进行了缩进:

```
javascript:(function() {  
  var s = document.createElement('script');  
  s.setAttribute('src', 'http://url-of-main-bookmarklet-script');  
  s.setAttribute('type', 'text/javascript');  
  document.getElementsByTagName('head')[0].appendChild(s);  
})();
```

除去空格，上面的代码(减去外部脚本 URL)有 193 个字符。

这段代码有一个缺点:它不适用于麦金塔电脑的 IE5。如果 IE5 Mac 支持对您的 bookmarklet 很重要，liorean 有一个加载存根的扩展版本，它也使用浏览器检测来迎合该浏览器。

##### 进一步阅读

了解 bookmarklets 的最好方法是看看其他人写的:

*   [www.bookmarklets.com](http://www.bookmarklets.com)
*   [www.favelets.com](http://www.favelets.com)
*   [www.squarefree.com/bookmarklets/](http://www.squarefree.com)

我希望这个 bookmarklets 的旋风之旅已经激发了您尝试创建自己的 bookmarklets。

## 分享这篇文章