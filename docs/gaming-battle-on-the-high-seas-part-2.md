# 游戏:公海之战，第二部分

> 原文：<https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-2/>

[上周](https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-1/ "Gaming: Battle on the High Seas, Part 1")，我介绍了一款名为 *SeaBattle* 的 HTML5 游戏，作为你可以用 HTML5 的音频、画布和网络存储 API 完成的演示。然后，我向您展示了如何将这个游戏嵌入到网页中，并概述了它的架构。本文从关注初始化开始深入研究这个体系结构。它探索了`SeaBattle`对象的`init(width, height)`和相关函数。

## 初始化 SeaBattle

清单 1 展示了`init(width, height)`函数的实现。

```
init: function(width, height) {
  var canvas = $("<canvas width='"+width+"' height='"+height+"'></canvas>");
  canvas.appendTo("body");
  SeaBattle.ctx = canvas.get(0).getContext("2d");
  SeaBattle.ctx.font = "30px Arial";
  SeaBattle.ctx.textAlign = "center";

  var seed = 5*height/6;
  SeaBattle.hillTops = new Array();
  for (var i = 0; i < width; i++)
  {
    SeaBattle.hillTops.push(seed);
    var x = SeaBattle.rnd(seed);
    if (x < seed/4)
    {
      if (--seed < 2*height/3)
        seed = 2*height/3;
    }
    else
    if (x > 3*seed/4)
    {
      if (++seed > height-1)
        seed = height-1;
    }
  }

  SeaBattle.width = width;
  SeaBattle.height = height;
  SeaBattle.dc = new Array(SeaBattle.MAX_DC);
  SeaBattle.torp = new Array(SeaBattle.MAX_TORP);
  SeaBattle.explosion = null;
  SeaBattle.msg = "";
  SeaBattle.score = 0;
  SeaBattle.hiScore = 0;
  if (SeaBattle.supports_html5_storage())
  {
    var temp = localStorage.getItem("hiScore");
    if (temp != undefined)
      SeaBattle.hiScore = temp;
  }
  SeaBattle.lives = 4;
  window.keydown = {};
  function keyName(event)
  {
    return jQuery.hotkeys.specialKeys[event.which] ||
           String.fromCharCode(event.which).toLowerCase();
  }
  $(document).bind("keydown", function(event) {
    keydown[keyName(event)] = true;
  });
  $(document).bind("keyup", function(event) {
    keydown[keyName(event)] = false;
  });

  SeaBattle.imgTitle = new Image();
  SeaBattle.imgTitle.src = "images/title.png";
  SeaBattle.imgSky = new Image();
  SeaBattle.imgSky.src = "images/sky.png";
  SeaBattle.imgMoon = new Image();
  SeaBattle.imgMoon.src = "images/moon.png";
  SeaBattle.imgShipLeft = new Image();
  SeaBattle.imgShipLeft.src = "images/shipLeft.png";
  SeaBattle.imgShipRight = new Image();
  SeaBattle.imgShipRight.src = "images/shipRight.png";
  SeaBattle.imgSubLeft = new Image();
  SeaBattle.imgSubLeft.src = "images/subLeft.png";
  SeaBattle.imgSubRight = new Image();
  SeaBattle.imgSubRight.src = "images/subRight.png";
  SeaBattle.imgExplosion = new Array();
  for (var i = 0; i < 17; i++)
  {
    var image = new Image();
    image.src = "images/ex"+i+".png";
    SeaBattle.imgExplosion.push(image);
  }

  SeaBattle.imgTorpedo = new Image();
  SeaBattle.imgTorpedo.src = "images/torpedo.png";
  SeaBattle.imgDC = new Image();
  SeaBattle.imgDC.src = "images/dc.png";
  SeaBattle.audBombLoaded = false;
  SeaBattle.audBomb = document.createElement("audio");
  SeaBattle.audBomb.onloadeddata = new function() {
    SeaBattle.audBombLoaded = true;
  };
  SeaBattle.audBomb.src = (navigator.userAgent.indexOf("MSIE") == -1)
                           ? "audio/bomb.wav" : "audio/bomb.mp3";
  SeaBattle.state = SeaBattle.STATE_INIT;
}
```

**清单 1:** 游戏初始化涉及画布和海底地形创建/初始化、热键绑定、游戏资源加载等等。

清单 1 首先使用 [jQuery](http://jquery.com/) 创建一个`<canvas>`元素节点，然后将其安装在浏览器的文档对象模型(DOM)树中。它按如下方式完成这项任务:

1.  调用`jQuery(*html*)`构造函数解析`html`字符串，从解析的 HTML 创建 DOM 节点，并创建/返回一个引用这些节点的`jQuery`对象。清单 1 创建了一个单独的`<canvas>` DOM 节点。
2.  在这个新的`jQuery`对象上调用`appendTo("body")`,将解析后的 HTML 的 DOM 节点附加到网页的`<body>`元素节点上。清单 1 将`<canvas>`节点附加到页面的`<body>`节点。

画布的上下文通过`canvas.get(0).getContext("2d")`获得，并分配给`SeaBattle`的`ctx`属性。接下来，2D 绘图上下文的`font`和`[textAlign](http://www.w3schools.com/tags/canvas_textalign.asp)`属性被初始化，以指定文本将以 30 像素高的 Arial 字体绘制，并使文本易于水平居中。

清单 1 通过随机选择山顶位置来生成海底地形。最左边的山顶位于画布底部三分之一的中点。右侧的每个山顶都是相对于前一个山顶而言的。

接着，传递给`init(width, height)`的`width`和`height`值被保存在同名的`SeaBattle`属性中，以便可以从其他函数中访问它们。此外，下列`SeaBattle`属性被初始化:

*   `dc`被初始化为最多存储`MAX_DC`个深水炸弹物体的数组。
*   `torp`被初始化为一个最多存储`MAX_TORP`个鱼雷物体的数组。
*   `explosion`被初始化为`null`。`update()`函数测试这个属性，看爆炸是否正在进行。当发生爆炸时，`explosion`被分配一个爆炸对象的参考。
*   `msg`被初始化为空字符串。当船只或潜艇获胜时，一个合适的消息被分配给该属性，用于随后在`draw()`功能中显示。
*   `score`初始化为零，反映玩家当前得分。此乐谱出现在画布的左上角。
*   `hiScore`初始化为零，反映玩家之前的最高分。如果当前浏览器支持 HTML5 Web 存储的本地方面，并且如果先前保存了该分数，`hiScore`被设置为保存的值。高分出现在当前分数后面的括号中。
*   初始化为 4，反映游戏结束前可以存活的毁灭者生命总数。毁灭者每被摧毁一次，这个计数就减一。

涉及键盘输入的游戏通常会识别*热键*，这些热键在被按下时会触发各种操作。此外，每个操作通常在其热键被按住时重复。例如，一个对象一直向左移动，直到松开左箭头键。

浏览器如何解释按键事件对象的`keyCode`和`charCode`属性以及其他因素的差异，使得实现您自己的热键响应逻辑变得非常困难。然而，这项任务并不太难完成，如以下步骤所示:

1.  将按键向下和向上事件监听器附加到画布上，如`canvas.onkeydown = keyDown;`和`canvas.onkeydown = keyUp;`所示。`keyDown`和`keyUp`分别标识响应按键按下和按下事件的功能。
2.  创建一个初始为空的关联数组，并将其分配给`window`对象，如`window.keydown = {}`所示。每个条目的键将是被按下的键的名称，当键被按下时，其值将为真，当键被按下时，其值将为假。
3.  对于`keyDown()`和`keyUp()`中的每一个，调用一个函数返回键的名称，这个键可以是字符键，也可以是非字符(特殊)键。然后，将结果作为索引放入`keydown`数组。对于`keyDown()`，将 true 赋给这个数组条目。对于`keyUp()`，赋值为 false。

实现这种解决方案可能很麻烦。比如`charCode`在 Opera 里总是不定义的。为什么不让 jQuery 和 jQuery 热键插件为你处理大部分工作呢？

jQuery 提供了强大的绑定功能，使得注册事件处理函数变得很容易。此外，热键插件有助于返回字符或特殊键的名称。如前所述，清单 1 利用这些功能来安装关键事件处理。

清单 1 现在通过实例化`Image`对象并将图像的位置和名称分配给对象的`src`属性，开始加载存储在`images`目录中的图像资源。它还开始加载一个音频资源，该资源存储在`audio`目录中。与其他浏览器不同，Safari 不提供`Audio`对象。为了确保跨浏览器行为的一致性，`document.createElement("audio")`被用来创建一个等价的对象。

当一个图像完成加载时，`Image`对象将 true 赋给它的`complete`属性。为了检测一个音频文件已经完成加载，一个将 true 赋给`SeaBattle`的`audBombLoaded`属性的`onloadeddata`处理函数被赋给了"`Audio`"对象。

除了 Internet Explorer，本系列第一部分提到的所有浏览器都支持 WAV 格式。相反，Internet Explorer 支持 MP3。清单 1 在选择加载合适的音频文件之前检测当前浏览器是否是 Internet Explorer。当当前浏览器是 Internet Explorer 时，表达式`navigator.userAgent.indexOf("MSIE")`返回-1 以外的值。这个事实有助于清单 1 在`audio/bomb.wav`和`audio/bomb.mp3`之间进行选择，后者被分配给“`Audio`对象的`src`属性。

清单 1 的最后一项任务是向`SeaBattle`对象添加一个`state`属性，并将`STATE_INIT`赋给这个属性。这种状态导致画布呈现居中的`Initializing...`消息，直到所有游戏资源完成加载。

## 获取随机整数

`init(width, height)`函数依靠`SeaBattle`的`rnd(limit)`函数返回随机整数，这样它就可以生成地形。清单 2 展示了`rnd(limit)`的实现。

```
rnd: function(limit) {
  return (Math.random()*limit)|0;
}
```

**清单 2:** 按位运算符使 JavaScript 将浮点数转换成整数。

清单 2 返回一个从零到`limit - 1`随机选择的整数。因为需要一个整数结果，并且因为`Math.random()*limit`返回一个带分数的数字，所以`|0`用于将结果截断为整数。要了解更多关于 JavaScript 转换为整数的能力，请查看 [Javascript 类型转换](http://jibbering.com/faq/notes/type-conversion/)常见问题。具体来说，阅读 FAQ 的 ToInt32 部分，了解 JavaScript 实现的`ToInt32`功能。

## 检测 HTML5 本地存储

`init(width, height)`功能也依赖于`SeaBattle`的`supports_html5_storage()`功能来检测网络存储的本地方面。清单 3 展示了`supports_html5_storage()`的实现。

```
supports_html5_storage: function() {
  try
  {
    return 'localStorage' in window &&
            window['localStorage'] !== null &&
            window['localStorage'] !== undefined;
  }
  catch (e)
  {
    return false;
  }
}
```

清单 3: 当 cookies 被禁用时，旧版本的 Firefox 会引发一个异常。

清单 3 通过检查全局`window`对象是否存在`localStorage`属性来检测对 Web 存储本地方面的支持。当该属性存在且不是`null`或`undefined`时，该函数返回 true 否则，它返回 false。

## 结论

`init(width, height)`函数与`rnd(limit)`和`supports_html5_storage()`函数一起工作，正确初始化`SeaBattle`对象。理解 SeaBattle 游戏体验的下一步是探索`update()`功能，这是本系列第三部分的主题。下周五，你还将学习如何实现 ship 对象。

## 分享这篇文章