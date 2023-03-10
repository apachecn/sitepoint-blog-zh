# 你的网站有 Konami 代码吗？

> 原文：<https://www.sitepoint.com/does-your-site-have-the-konami-code/>

![konami-contra-cover](img/1883587f72a990fdbdaf1f619ecd634c.png "konami-contra-cover")

为自己辩护的是，我刚刚结束了为期两周的海外假期，大部分时间都是离线状态。所以当我的朋友罗伊斯直接给我发了一条推特时，我不知道他是什么意思:

> 为什么你的网站上没有 konami 代码？

现在，在这一点上，我有机会快速使用谷歌来维持我对所有最新迷因都很熟悉的脆弱的伪装。但是没有，我被突如其来的诚实折磨着，发了一个不太好的回复:

> 什么是 Konami 代码？

我应该使用谷歌；我的极客信誉已经毁了。如果有读者还没有听说过 Konami Code，那么这一期将把你从同样的命运中拯救出来。

我喜欢网站上的复活节彩蛋；我有一种反常的吸引力，喜欢花时间和精力去写一些毫无意义的代码。Konami 代码又回到了老派的游戏机游戏作弊代码；组合键是上，上，下，下，左，右，左，右，B，a。如果你想知道更多，Youtube 上的一个视频[解释了一切](http://www.youtube.com/watch?v=mkvWXbGrCx0)。已经有很多网站实现了复活节彩蛋，当你输入代码时就会触发。[ESPN 网站为愚人节](http://keithlam.com/2009/04/28/espncom-unicorns/)做了这件事，而 [jQuery 网站](http://jquery.com/)已经做了很久了，还有[谷歌阅读器](http://www.google.com/reader/)甚至[脸书](http://www.facebook.com/)。访问 [Konami 代码站点](http://konamicodesites.com/)找到站点列表。

我浏览了几个基于 JavaScript 的实现复活节彩蛋的解决方案，发现了保罗·爱尔兰的一个非常巧妙的 jQuery 例子，展示了一点 JavaScript-fu。该脚本使用一个事件监听器来拦截`keydown`事件，并跟踪所有输入的键。如果按下的组合键包含 Konami 代码，则触发复活节彩蛋:

```
var kkeys = [], konami = "38,38,40,40,37,39,37,39,66,65";
$(document).keydown(function(e) {
  kkeys.push( e.keyCode );
  if ( kkeys.toString().indexOf( konami ) >= 0 ){
    $(document).unbind('keydown',arguments.callee);
    // Launch easter egg here          
  }
});
```

该脚本首先声明几个变量；`kkeys`，一个空数组，用于存储用户的按键；以及`konami`，代表正确组合键的键码的字符串。

一个事件监听器被附加到`document`对象来监听`keydown`事件。加载页面时，每次按下一个键，`keycode`就会被添加到`kkey`数组中。然后脚本检查是否输入了 Konami 代码。如果有，那么事件监听器被移除，复活节彩蛋被显示。

注意`arguments.callee`的用法？(我在《科技时报》第 215 期上写过这个奇怪但有用的`arguments`物体。)属性`callee`指的是当前函数，所以使用`arguments.callee`是事件监听器函数移除自身的一个好方法。在本例中，它还使事件监听器功能保持匿名。

保罗爱尔兰网站上的复活节彩蛋使用了 Cornify 的服务，这是一个非常有用的[独角兽和彩虹网络服务](http://cornify.com/):

```
$.getScript('http://www.cornify.com/js/cornify.js', function(){
  cornify_add();
  $(document).keydown(cornify_add);
});
```

我还发现了一些其他值得注意的例子。Matt Snider 有一个使用 YUI 的例子，其中脚本创建了一个自定义事件，还有另外两个普通的旧 JavaScript 例子。第一个是 [konami-js](http://code.google.com/p/konami-js/source/browse/trunk/konami.js) ，如果按键之间有长时间的延迟，它会使用超时来重置 konami 代码检测例程。第二个是 James Padolsey 的 [onKonamiCode 事件处理程序](http://james.padolsey.com/javascript/konami-craziness/)。

你还在等什么？你有密码。今天就在你的网站上添加 Konami 代码。

## 分享这篇文章