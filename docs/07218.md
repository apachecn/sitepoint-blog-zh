# 设计灵活的移动菜单

> 原文：<https://www.sitepoint.com/responsive-mobile-menu-design-2/>

[![](img/252dd4da6eb1c089ce941795029a62ce.png)](http://www.onbile.com/info/best-mobile-websites-responsive-web-design/)

响应式网站设计的最大挑战之一是让你的菜单发挥作用。如果你曾经不得不去弄乱它，你知道我在说什么。您可以使用媒体查询来定义在不同屏幕尺寸时应用的 CSS，但有时您需要额外的功能来实现理想的设计。

### 什么是手机菜单设计？

手机菜单设计就是简单地让菜单适应手机屏幕的大小。响应式网页设计的核心概念之一是让所有内容在所有屏幕尺寸下随时可用。同样重要的是，我们希望只发布一次内容— [而不是创建我们内容的“移动版本”](https://www.sitepoint.com/forget-mobile-sites-time-for-a-responsive-web/ "Forget Mobile Sites! Time for a Responsive Web: 11 Responsive Design Resources")。

因此，创建响应性菜单，允许在所有屏幕尺寸场景下工作的大量菜单项是很重要的。

### 为什么要用手机菜单设计？

大的甚至不太大的导航菜单在小屏幕上占据了很大的空间。理想情况下，您可以压缩菜单项，同时使菜单可用。太小，用户看不到它们，或者可能很难触摸到按钮。太大了，我们让我们的用户滚动，滚动，再滚动。

因此，手机菜单设计成为一项重要的可用性任务。

### 一种手机菜单设计方法

我喜欢把菜单折叠成下拉菜单。这变得越来越流行，用户通常能够快速找到菜单并弄明白它。下拉菜单也是一个巨大的节省空间的工具。它们允许你在屏幕的一小部分上组织近乎无限的项目——只需点击并滚动选项。

### TinyNav.js 用于快速移动菜单设计

说到移动设计，一个考虑因素是用户必须下载的代码量。如果不小心的话，你可以设计大量的功能，创建一个臃肿的网站，所以小文件是至关重要的。

我发现的一个解决方案是 TinyNav.js，它是一个小的 JavaScript 文件(433 字节),可以自动将菜单项转换为下拉菜单。它接入 jQuery，这是另一个 30-100k，取决于您使用哪个版本。

TinyNav.js 很容易实现，并且可以使用媒体查询来控制，所以只有在特定的媒体查询调用中声明它时，它才会折叠菜单项。

### 在 WordPress 上安装 TinyNav.js

如果你有一个 WordPress 网站，[有一个 TinyNav.js WordPress 插件](http://wordpress.org/extend/plugins/tinynav/)——只需安装插件，调整你的媒体查询，就大功告成了。只需要几分钟。

实际上有一些主题已经将 TinyNav.js 融入了它们的核心。它正成为一个越来越受欢迎的解决方案——以至于，我希望 TinyNav.js 能作为一个基本特性包含在 WordPress 即将发布的版本中，或者至少作为 TwentyEleven 主题的一部分。

### 自行实现 TinyNav.js

下面我创建了一个 TinyNav.js 的简单实现，向您展示了一种将该工具整合到任何环境中的方法。我做了一个有两个页面的网站——“主页”和“关于”——这是一个响应式的网站。如果缩小屏幕尺寸，菜单会缩小。

以下是供您下载和实验的 home.html 代码:

[sourcecode language="html"]

tinynav . js Demo
<script type = " text/JavaScript " src = " https://Ajax . Google APIs . com/Ajax/libs/jquery/1.7/jquery . min . js "></script><script type = " text/JavaScript " src = " tinynav . min . js "></script>

这种设计在屏幕宽度低于 600 像素之前没有反应。

[/sourcecode]

这是“关于”页面。复制并粘贴到一个文本编辑器，并保存为 home.html 在同一个文件夹作为您的主页。html:

[sourcecode language="html"]

tinynav . js Demo
<script type = " text/JavaScript " src = " https://Ajax . Google APIs . com/Ajax/libs/jquery/1.7/jquery . min . js "></script><script type = " text/JavaScript " src = " tinynav . min . js "></script>

< h1 > TinyNav.js 演示！</h1>
<div id = " nav container ">
<ul id = " nav ">
<李><a href = " Home . html ">Home</a></李>
<李 class = " selected "><a href = " About . html ">关于< /a <李> < a href="#" >菜单项</a></李>
<李> < a href="#" >菜单项</a></李>
</ul>
</div> </H2>
<pre>
你可以在 GitHub 网站找到更多关于 TinyNav.js 的信息:<a href = " https://GitHub . com/viljamis/tinynav . js ">https://github.com/viljamis/TinyNav.js</a>

TinyNav.js 的创建者有自己的代码主页:[http://tinynav.viljamis.com/](http://tinynav.viljamis.com/)

你可以从资源库获得 WordPress 插件:[http://wordpress.org/extend/plugins/tinynav/](http://wordpress.org/extend/plugins/tinynav/)

[/sourcecode]

最后，这是您的 JavaScript，取自缩小的 TinyNav.js 文件。将此文件另存为 tinynav.min.js，与其他两个文件放在同一文件夹中。

[source code language = " JavaScript "]
/*！http://tinynav.viljamis.com v 1.1 by @ viljamis */
(function(a，I，g){ a . fn . tinynav = function(j){ var b = a . extend({ active:" selected "，header:" "，label:""}，j)；返回 this . each(function(){ g++；var h=a(this)，d="tinynav"+g，f= "。l_"+d，e=a(" <选择>/选择>)。attr("id "，d)。addClass(" tinynav "+d)；if(h.is("ul，ol "){ " "！==b.header & & e.append(a(" ")。文本(b . header))；var c =h.addClass("l_"+d)。find("a ")。each(function(){ c+= "；var b；for(b = 0；b " })；e .追加(c)；b . header | | e . find(":eq("+a(f+" Li ")。索引(a(f+" li。"+b.active))+")。attr("selected "，0);e . change(function(){ I . location . href = a(this)。val()})；答(f)。(e)之后；b . label&e . before(a(" ")。attr("for "，d)。addClass(" tinynav _ label "+d+" _ label ")。append(b.label))}})}})(jQuery，this，0)；[/源代码]

现在你可以走了！使用基本的 CSS，你可以得到你想要的菜单。

*你用过 TinyNav.js 吗？如果是这样，请随时与我们分享任何提示！*

## 分享这篇文章