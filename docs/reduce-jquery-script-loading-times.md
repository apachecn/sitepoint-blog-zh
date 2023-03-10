# 减少 jQuery 脚本的加载时间

> 原文：<https://www.sitepoint.com/reduce-jquery-script-loading-times/>

![the-only-script-in-your-head](img/c4ec4cf00dc28f5dabeaeaf8ea71f7e6.png "the-only-script-in-your-head")

嗨，伙计们，我偶然发现了一个 jQuery 脚本，它可以减少 JavaScript 包含文件的加载时间。它的工作原理是在一个数组中同时加载所有的点 js 文件。我已经测试过了，它确实比用标准方式加载文件要快得多。看到下面的证明，自己试试吧！

## 标准方式

```
<!-- 5 singlar js includes -->
<script src="https://github.com/DmitryBaranovskiy/raphael/raw/master/raphael.js"></script>
<script src="https://github.com/jquery/jquery-ui/raw/master/jquery-1.4.4.js"></script>
<script src="https://github.com/smith/scripty2/raw/master/lib/prototype.js"></script>
<script src="https://github.com/headjs/www/raw/master/content/test/jquery-ui.js"></script>
<script src="https://github.com/kosmas58/compass-jquery-plugin/raw/master/lib/jslint.js"></script>
```

![script-in-head](img/4be57ac625fd9856e71a13dcb674e46c.png "script-in-head")

## head.js 道

```
<!-- 1 js include (which calls the others async) -->
<script src="../media/js/head.min.js"></script> 
<script>
head.js("https://github.com/DmitryBaranovskiy/raphael/raw/master/raphael.js")
	 .js("https://github.com/jquery/jquery-ui/raw/master/jquery-1.4.4.js")
	 .js("https://github.com/smith/scripty2/raw/master/lib/prototype.js")
	 .js("https://github.com/headjs/www/raw/master/content/test/jquery-ui.js")
	 .js("https://github.com/kosmas58/compass-jquery-plugin/raw/master/lib/jslint.js");
</script>
```

![head-js-demo](img/50f83a5218d20f2991f9e1659a64eaa6.png "head-js-demo")

## head.js 文档就绪

对于 jQuery 代码，通常需要等到 DOM 准备好。这与使用 head.js 时的情况相同，这也是您可以获得相同结果的方式。

```
//same as document.ready() function
head.ready(function() {
     //code to be executed once .js files have loaded.
});
```

[试玩](http://headjs.com/test/headjs.html)
[下载](http://headjs.com/#download)

## 分享这篇文章