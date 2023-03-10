# String.prototype.split() …几乎的力量

> 原文：<https://www.sitepoint.com/the-power-of-stringprototypesplit-almost/>

如果你觉得作为一名网络开发人员，你没有得到足够的尊重，T2，这里有一个很好的馅饼可以扔向人们。

实际上，我认为“花在希望比尔·盖茨慢慢痛苦死去上的时间”这一部分需要扩展——比尔没有直接的责任。事实上，如果 [IE 团队](http://blogs.msdn.com/ie/)能更积极主动地为特性命名，那就太好了，这样我们就知道*到底该骂谁了:“你好，我是【插入名字】，我就是那个在你的数组末尾放了一个未定义值的人，每次你离开那个尾随的逗号，就会产生让你开心几个小时的 bug:)”。*

在玩一个 Javascript 版本的[这个](http://dev.splitbrain.org/view/darcs/dokuwiki/inc/parser/lexer.php)的时候被[这个特殊的](http://simon.incutio.com/archive/2003/10/26/reSplit#comment10)蛰了一下，现在有点苦。尽管有这么多 AJAXy 的东西，编写跨浏览器代码仍然感觉像瞎飞一样。允许我抱怨一会儿…

从[到规格](http://www.ecma-international.org/publications/standards/Ecma-262.htm)(p103/104)；

> 如果分隔符是包含捕获括号的正则表达式，那么每次分隔符
> 匹配时，捕获括号的结果(包括任何未定义的结果)被拼接到
> 输出数组中。[…]

事实上，这种行为对 Javascript 来说并不特别。

例如 Perl…

```
 use Data::Dumper;
print Dumper(split(/(:)/, 'a:b:c')); 
```

…输出…

```
$VAR1 = 'a';
$VAR2 = ':';
$VAR3 = 'b';
$VAR4 = ':';
$VAR5 = 'c';

```

…还有 PHP…

```
 print_r(preg_split('/(:)/', 'a:b:c', -1, PREG_SPLIT_DELIM_CAPTURE)); 
```

…输出…

```
Array
(
    [0] => a
    [1] => :
    [2] => b
    [3] => :
    [4] => c
)

```

…和 Python…

```
 import re
print re.compile('(:)').split('a:b:c') 
```

…输出…

```
['a', ':', 'b', ':', 'c']

```

在 Javascript 中，这可能像…

```
 alert( "a:b:c".split(/(:)/) ); 
```

…这是 Firefox(在 Firebug 的帮助下)给我的；

```
["a",":","b",":","c"]

```

同样，Opera 9 也做对了这一点。但是在 IE (6)中…

```
a,b,c

```

我的俘虏分离者在哪里！。

正如西蒙所说:

> 为什么这是一件大事？因为它突然让编写简单的解析器和标记器变得容易多了。

实际上指责 IE 团队可能是不公平的——这似乎是由 [JScript 团队](http://www.gotdotnet.com/team/jscript/)和*提供的一个“特性”,似乎*也悄悄进入了 JScript.NET，例如像`split.js`这样的脚本包含；

```
 import System.Windows.Forms;
MessageBox.Show("a:b:c".split(/(:)/)); 
```

我可以在 DOS 下用 [jsc](http://www.devsource.com/article2/0,1895,1955461,00.asp) 编译器编译它，就像`D:js> C:WINDOWSMicrosoft.NETFrameworkv2.0.50727jsc.exe /nologo split.js`一样，然后运行输出`split.exe`得到完全相同的结果——`a,b,c`。叹气。

无论如何——以后再讨论这个 lexer(最终设法解决了这个问题)。顺便说一句，如果你需要在 Javascript 中进行严肃的解析(尽管只有 Moz ),看看 Javascript 中的这个[编译器生成器。](http://www.navyrain.net/compilergeneratorinjavascript/)

## 分享这篇文章