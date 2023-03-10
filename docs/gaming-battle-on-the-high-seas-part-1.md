# 游戏:公海之战，第一部分

> 原文：<https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-1/>

支持 HTML5 的音频、画布和 Web 存储 API 的 Web 浏览器是一个令人兴奋的游戏平台。这些 API 可以用来创建有赚钱潜力的有趣游戏。作为演示，本文开始了一个由五部分组成的 HTML5 游戏开发系列，重点是一个简单的 *SeaBattle* 游戏。第 1 部分介绍了 SeaBattle，展示了如何将它嵌入到 Web 页面中，并概述了它的体系结构。这里展示的游戏已经在 Chrome、Firefox、Internet Explorer 9、Opera 12 和 Safari 5 桌面浏览器中进行了测试。


## 介绍 SeaBattle

几年前，我玩了一个游戏，一艘驱逐舰和多艘潜艇交战。因为这个游戏玩起来很有趣，所以我为这个系列实现了一个更简单的游戏。图 1 显示了我的 SeaBattle 游戏标题屏幕的快照。毁灭者照片是从[维基共享资源](http://commons.wikimedia.org/wiki/File:Underwater_Explosion_with_a_Blue_Water_Navy_Ship.jpg)获得的。

[![SeaBattle Title Screen](img/5cee214fcf1be809696a505c3d22001a.png "SeaBattle Title Screen")](https://www.sitepoint.com/wp-content/uploads/2012/10/seabattle-title-screen.jpg)

图 1:标题屏幕介绍了 SeaBattle。

图 1 的标题屏幕向您介绍了 SeaBattle，并告诉您按回车键开始这个游戏。当您按下这个键时，您会看到一个类似于图 2 所示的屏幕。

![SeaBattle Gameplay](img/9f4975e3310a411dbe32680c5533a3af.png "SeaBattle Gameplay")

图 2:一艘驱逐舰与一艘潜艇作战。深水炸弹和鱼雷的尺寸被夸大了，以提高这些游戏对象的可视性。

图 2 展示了一个场景，你，毁灭者，出现在一个星空背景前。当前分数和最近的最高分数(在括号中)出现在左上角。从本地存储中检索最高分。右下角毁灭者图像的数量表示剩余的生命数量。

在你下方的某个地方，一艘潜艇进入场景并开始发射鱼雷。你可以使用左右方向键来躲避鱼雷。毁灭者图像改变以反映新的方向。当它到达画布边缘时，它的速度会改变。

你可以按空格键发射两枚深水炸弹。当深水炸弹击中潜艇时，潜艇被摧毁，你的分数提高 100 分。如果超过了高分，它将被更新并保存在本地存储中。

当前一轮游戏持续到潜艇被深水炸弹摧毁或者驱逐舰被鱼雷摧毁。此时，会出现一条消息，说明您是赢了还是输了，以及游戏是否结束。当您重新开始已结束的游戏时，分数会重置为零。

## 在网页中嵌入 SeaBattle

SeaBattle 由一个依赖于 jQuery 和 jQuery 热键插件的`SeaBattle.js` JavaScript 文件组成(在本系列的第 2 部分中讨论)。要将这个游戏嵌入到网页中，请包含这些文件，如清单 1 所示。

```
<script type="text/javascript" src="https://code.jquery.com/jquery-1.7.2.min.js"></script><script type="text/javascript" language="javascript" src="jquery.hotkeys.js"></script>
<script type="text/javascript" src="SeaBattle.js"></script>
```

**清单 1:** SeaBattle 依赖于三个外部 JavaScript 文件。`SeaBattle.js`必须最后包含。接下来，在页面的主体中嵌入一个初始化 SeaBattle 的`<script>`元素，并重复执行一个更新游戏状态的函数，重新绘制画布以反映新的状态。清单 2 展示了完成这项任务的一种方法。

```
<script type="text/javascript">// <![CDATA[
  SeaBattle.init(800, 480);

  // The following function is courtesy of Opera Engineer Erik Mіller -- see
  // http://my.opera.com/emoller/blog/2011/12/20/requestanimationframe-for-smart-er-animating
  (function()
   {
     var lastTime = 0;
     var vendors = ['ms', 'moz', 'webkit', 'o'];
     for (var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x)
     {
        window.requestAnimationFrame = window[vendors[x]+'RequestAnimationFrame'];
        window.cancelRequestAnimationFrame = window[vendors[x]+'CancelRequestAnimationFrame'];
     }

     if (!window.requestAnimationFrame)
     {
       var f = function(callback, element)
               {
                 var currTime = new Date().getTime();
                 var timeToCall = Math.max(0, 16-(currTime-lastTime));
                 var id = window.setTimeout(function()
                                            {
                                              callback(currTime+timeToCall);
                                            }, timeToCall);
                 lastTime = currTime+timeToCall;
                 return id;
               };
       window.requestAnimationFrame = f;
     }

     if (!window.cancelAnimationFrame)
       window.cancelAnimationFrame = function(id)
                                     {
                                       clearTimeout(id);
                                     };
  }());

  (function gameloop()
  {
    SeaBattle.update();
    requestAnimationFrame(gameloop);
    SeaBattle.draw();
  })();
// ]]></script>
```

**清单 2:** SeaBattle 初始化，然后进入一个无限的更新-绘制循环。

清单 2 首先通过调用其`init(width, height)`函数初始化预先创建的`SeaBattle`对象，该函数创建一个指定宽度(800 像素)和高度(480 像素)的`<canvas>`元素，加载游戏资源，并执行其他任务。

接下来，安装一个跨浏览器`requestAnimationFrame()`功能，该功能委托给一个特定于浏览器的功能。浏览器函数通过调度一个像素绘制回调函数在下一个浏览器窗口重画之前调用来产生更平滑的动画。

提供自己请求动画帧功能的浏览器(比如 Mozilla 的`mozRequestAnimationFrame()`功能)可以在你切换到另一个标签时自动降低帧速率。毕竟，当输出不可见时，游戏以最高速度运行是没有意义的。然而，并不是所有的浏览器都支持这个功能:Internet Explorer 9 就是一个例子。对于这些浏览器，`setInterval()`用于调用回调函数。无论调用哪个函数，渲染都以每秒 60 帧的速度进行。

最后，清单 2 指定并调用了一个`gameloop()`函数，它定义了 SeaBattle 的游戏循环。该功能执行以下任务:

1.  执行`SeaBattle`的`update()`函数，根据用户输入和其他因素计算新的游戏状态。
2.  在绘制浏览器窗口之前(如果支持“`requestAnimationFrame()`”)或者在下一个时间点(通过`setTimeout()`)执行`requestAnimationFrame(gameloop)`来调度`gameloop()`进行调用。
3.  执行`SeaBattle`的`draw()`函数，用更新后的游戏状态重绘画布。

## 概述 SeaBattle 的 JavaScript 架构

在某种程度上，您会想要增强 SeaBattle，所以您需要了解它是如何工作的。获得这些知识的第一步是掌握对象的整体 JavaScript 架构。请参见清单 3。

```
var SeaBattle =
{
  init: function(width, height)
        {
        },

  update: function()
          {
          },

  draw: function()
        {
        },

  MAX_DC: 2,
  MAX_TORP: 15,
  STATE_INIT: 0,
  STATE_TITLE: 1,
  STATE_PLAY: 2,
  STATE_WINLOSE: 3,
  STATE_RESTART: 4,

  allResourcesLoaded: function()
                      {
                      },

  intersects: function(r1, r2)
              {
              },

  makeDepthCharge: function(bound)
                   {
                   },

  makeExplosion: function(isShip)
                 {
                 },

  makeShip: function(x, y, bound1, bound2)
            {
            },

  makeSub: function(x, y, bound1, bound2)
           {
           },

  makeTorpedo: function(bound)
               {
               },

  rnd: function(limit)
       {
       },

  supports_html5_storage: function()
                          {
                          }
}
```

**清单 3:** `SeaBattle`定义了 19 个静态属性。附加属性被动态添加到该对象中。

清单 3 的全局`SeaBattle`对象首先呈现了一个由`init(width, height)`、`update()`和`draw()`组成的公共 API。然后，它提供了一个私有 API，该 API 定义了以下*伪常量*(一个假装为常量的变量)属性:

*   指定在任何给定时间可以使用的深水炸弹的最大数量。一个小的数值会使摧毁潜艇变得更加困难，从而带来更有趣的游戏。这个伪常数出现在`init(width, height)`、`update()`和`draw()`中。
*   指定在任何给定时间可以使用的最大鱼雷数量。比深水炸弹数量更大的数值会使游戏更有趣。这个伪常数出现在`init(width, height)`、`update()`和`draw()`中。
*   `STATE_INIT`标识游戏的初始状态。SeaBattle 加载图像和音频资源，并显示初始化消息。加载完所有资源后，状态变为`STATE_TITLE`。这个伪常数出现在`init(width, height)`、`update()`、`draw()`中。
*   `STATE_TITLE`标识游戏的标题状态。SeaBattle 会显示一条消息，告诉您按 Return 键来玩游戏。这个伪常数出现在`update()`和`draw()`中。
*   `STATE_PLAY`表示游戏的播放状态。当 SeaBattle 处于这种状态时，您可以通过按下左箭头键、右箭头键和空格键来与游戏进行交互。这个伪常数只出现在`update()`中。
*   `STATE_WINLOSE`表示游戏的输赢状态。爆炸完成后，游戏被设置为这种状态，并用于确保显示一条赢/输消息。这个伪常数出现在`update()`和`draw()`中。
*   `STATE_RESTART`表示游戏的重启状态。游戏设定为爆炸结束后的状态，没有生命了。它用于确保显示“游戏结束”消息，将分数重置为零，并将生命总数重置为四。这个伪常数出现在`update()`和`draw()`中。

私有 API 还定义了以下函数属性:

*   当所有图像和音频资源都已加载时，`allResourcesLoaded()`返回 true 否则，它返回 false。
*   当`r1`定义的矩形与`r2`定义的矩形相交时，`intersects(r1, r2)`返回 true 否则，它返回 false。
*   `makeDepthCharge(bound)`用指定的下限`bound`创建深水炸弹对象。深水炸弹一旦到达这个界限就消失了。
*   `makeExplosion(isShip)`制造爆炸，其中`isShip`确定船只或潜艇是否正在爆炸。
*   `makeShip(x, y, bound1, bound2)`创建一艘新船，其图像的中心位置传递给`x`和`y`，其水平移动由左边的`bound1`和右边的`bound2`限定。
*   `makeSub(x, y, bound1, bound2)`创建一个新的潜艇对象，其图像的中心位置被传递给`x`和`y`，其水平移动被左边的`bound1`和右边的`bound2`限制。
*   `makeTorpedo(bound)`用指定的上限`bound`创建鱼雷。鱼雷一旦到达这个界限就会消失。
*   `rnd(limit)`返回从零到`limit` -1 的随机整数。
*   当浏览器支持 Web 存储的本地方面时，`supports_html5_storage()`返回 true 否则，它返回 false。

## 结论

SeaBattle 是一个示例 HTML5 游戏，它利用了音频、画布和 Web 存储 API。现在，您已经了解了这个游戏，学会了如何将它嵌入到网页中，并且了解了架构概述，您已经准备好深入学习了。下周五，[第二部分](https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-2 "Gaming: Battle on the High Seas, Part 2")通过探索`init(width, height)`、`rnd(limit)`和`supports_html5_storage()`函数开始这项任务。

## 分享这篇文章