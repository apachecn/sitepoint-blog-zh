# 如何像禅师一样编码

> 原文：<https://www.sitepoint.com/how-to-code-like-a-zen-master/>

我的编程生活被永远改变了。我已经开悟了……*，你也可以*。

HTML 和 CSS 可能已经彻底改变了网络和我们的职业生涯，但每天写标签可能会成为一件苦差事。许多 ide 和文本编辑器都提供了诸如自动完成和键盘快捷键等功能，但它们不会显著减轻您的输入负担。

禅宗编码可能是答案。这是一个强大的缩写引擎，它将类似 CSS 选择器的表达式扩展成 HTML 代码。

让我们试一个简单的例子。假设您需要一个 ID 为“content”的`div`，它包含 3 个`p`标签。您应该在编辑器中键入以下字符串:

```
 div#content>p*3 
```

然后点击 Zen 编码的“扩展缩写”菜单项或键盘快捷键。代码被神奇地转换成有效的 HTML:

```
 <div id="content">
  <p></p>
  <p></p>
  <p></p>
</div> 
```

想更冒险一点吗？以下缩写:

```
 div#page>p.top+ul#nav>li*5>a 
```

扩展到:

```
 <div id="page">
  <p class="top"></p>
  <ul id="nav">
    <li><a href=""></a></li>
    <li><a href=""></a></li>
    <li><a href=""></a></li>
    <li><a href=""></a></li>
    <li><a href=""></a></li>
  </ul>
</div> 
```

不够硬核？输入以下内容:

```
 html:5>(div#header>h1)+(div#content>p*3)+(ul#nav>li.item$*6>a[href="#"])+div#footer 
```

要生成完整的 HTML5 模板:

```
 <!DOCTYPE HTML>
<html lang="en-US">
<head>
  <meta charset="UTF-8">
  <title></title>
</head>
<body>
  <div id="header">
    <h1></h1>
  </div>
  <div id="content">
    <p></p>
    <p></p>
    <p></p>
  </div>
  <ul id="nav">
    <li class="item1"><a href="#"></a></li>
    <li class="item2"><a href="#"></a></li>
    <li class="item3"><a href="#"></a></li>
    <li class="item4"><a href="#"></a></li>
    <li class="item5"><a href="#"></a></li>
    <li class="item6"><a href="#"></a></li>
  </ul>
  <div id="footer"></div>
</body>
</html> 
```

这会为您节省多少时间和精力？还有一系列用于样式表的 CSS 缩写。

## 下载禅宗的艺术

[Zen 作为插件](http://code.google.com/p/zen-coding/downloads/list)可用于各种流行的 ide 和编辑器，包括:

*   阿普塔纳
*   BBEdit
*   乐章结尾部
*   梦想编织者
*   黯然失色
*   Emacs
*   浓咖啡
*   GEdit
*   科莫多岛
*   记事本++
*   PSP
*   文字样式
*   顶层风格
*   UltraEdit
*   精力
*   可视化工作室

许多缩写是显而易见的，但是如果你需要进一步的帮助，一个[禅宗备忘单](http://zen-coding.googlecode.com/files/ZenCodingCheatSheet.pdf)是可用的。

为什么不试试禅呢？你的同事会惊讶于你的工作效率，你的手指也会感谢你！

## 分享这篇文章