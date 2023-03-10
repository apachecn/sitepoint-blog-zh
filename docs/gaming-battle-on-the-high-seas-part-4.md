# 游戏:公海之战，第 4 部分

> 原文：<https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-4/>

[上周](https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-3 "Gaming: Battle on the High Seas, Part 3")，我们的游戏系列通过讨论`SeaBattle`对象的`update()`功能以及它的`makeShip(x, y, bound1, bound2)`构造函数，更深入地挖掘了 SeaBattle 的架构。这是我们五篇系列文章中的第四篇，继续探索这个体系结构，包括潜艇、深水炸弹、鱼雷和爆炸的构造器。它还讨论了`intersects(r1, r2)`和碰撞检测。

## 制造潜艇

`update()`函数负责创建潜艇和其他游戏对象。它在`makeSub(x, y, bound1, bound2)`构造器的帮助下完成潜艇的创建。清单 1 展示了这个构造函数的实现。

```
makeSub: function(x, y, bound1, bound2) {
  this.x = x;
  this.y = y;
  this.bound1 = bound1;
  this.bound2 = bound2;
  this.bbox = { left: 0, top: 0, right: 0, bottom: 0 };
  this.LEFT = 0;
  this.RIGHT = 1;
  this.dir = (x >= SeaBattle.width) ? this.LEFT : this.RIGHT;
  this.exploded = false;
  this.height = SeaBattle.imgSubLeft.height;
  this.vx = SeaBattle.rnd(5)+2;
  this.width = SeaBattle.imgSubLeft.width;
  this.draw = function() {
    SeaBattle.ctx.drawImage((this.dir == this.LEFT)?
                             SeaBattle.imgSubLeft :
                             SeaBattle.imgSubRight,
                             this.x-this.width/2,
                             this.y-this.height/2);
  }
  this.getBBox = function() {
    this.bbox.left = this.x-this.width/2;
    this.bbox.top = this.y-this.height/2;
    this.bbox.right = this.x+this.width/2;
    this.bbox.bottom = this.y+this.height/2;
    return this.bbox;
  }
  this.move = function() {
    if (this.dir == this.LEFT)
    {
      this.x -= this.vx;
      if (this.x-this.width/2 < this.bound1)
      {
        this.x += this.vx;
        this.vx = SeaBattle.rnd(3)+1;
        this.dir = this.RIGHT;
      }
    }
    else
    {
      this.x += this.vx;
      if (this.x+this.width/2 > this.bound2)
      {
        this.x -= this.vx;
        this.vx = SeaBattle.rnd(3)+1;
        this.dir = this.LEFT;
      }
    }
  }
}
```

**清单 1:**`move()`函数在潜艇通过左边缘或右边缘后自动切换方向。

清单 1 首先将其参数保存在 sub 潜艇对象属性中，然后引入了另外 11 个对象属性:

*   `bbox`引用一个矩形对象，作为碰撞检测的边界框。这个对象作为参数传递给`intersects(r1, r2)`函数。
*   `LEFT`是与`dir`属性一起使用的伪常数。
*   `RIGHT`是与`dir`属性一起使用的伪常数。
*   `dir`指定潜艇当前的方向。
*   `exploded`表示潜艇是否已经爆炸。
*   `height`以像素为单位指定海底图像的高度。
*   `vx`根据潜艇移动的像素数指定潜艇的水平速度。
*   `width`以像素为单位指定海底图像的宽度。
*   `draw()`绘制与潜艇的`x`和`y`属性一致的潜艇图像。
*   `getBBox()`返回更新后的`bbox`对象。该对象被更新以适应潜艇水平位置的变化。
*   向左或向右移动潜艇。

## 制造深水炸弹

当按下空格键时，`update()`试图创建一个深水炸弹物体(一次只能使用两个深水炸弹)。清单 2 的`makeDepthCharge(bound)`构造函数用于创建深水炸弹。

```
makeDepthCharge: function(bound) {
  this.bound = bound;
  this.bbox = { left: 0, top: 0, right: 0, bottom: 0 };
  this.height = SeaBattle.imgDC.width;
  this.width = SeaBattle.imgDC.height;
  this.draw = function() {
    SeaBattle.ctx.drawImage(SeaBattle.imgDC, this.x-this.width/2, this.y-this.height/2);
  }
  this.getBBox = function() {
    this.bbox.left = this.x-this.width/2;
    this.bbox.top = this.y-this.height/2;
    this.bbox.right = this.x+this.width/2;
    this.bbox.bottom = this.y+this.height/2;
    return this.bbox;
  }
  this.move = function move() {
    this.y++;
    if (this.y+this.height/2 > this.bound)
      return false;
    return true;
  }
  this.setLocation = function(x, y) {
    this.x = x;
    this.y = y;
  }
}
```

清单 2: 深水炸弹的当前位置与其图像的中心重合。

清单 2 首先将传递给它的参数`bound`保存在深水炸弹对象属性中，然后引入了另外七个对象属性:

*   `bbox`引用一个矩形对象，作为碰撞检测的边界框。
*   `height`以像素为单位指定深水炸弹图像的高度。
*   `width`以像素为单位指定深水炸弹图像的宽度。
*   `draw()`绘制深水炸弹图像。
*   `getBBox()`以对象的当前`x`和`y`值为中心返回更新后的`bbox`对象。
*   `move()`将深水炸弹向下推进一个像素，直到通过下限。
*   `setLocation(x, y)`指定深水炸弹的位置，它与深水炸弹图像的中心重合。

## 制造鱼雷

当潜艇的中心可见时，一个随机生成的整数等于某个值，并且少于 15 个鱼雷在使用，`update()`创建一个鱼雷对象。创建这个对象的实际工作由清单 3 的`makeTorpedo(bound)`构造函数执行。

```
makeTorpedo: function(bound) {
  this.bound = bound;
  this.bbox = { left: 0, top: 0, right: 0, bottom: 0 };
  this.height = SeaBattle.imgTorpedo.height;
  this.width = SeaBattle.imgTorpedo.width;
  this.draw = function() {
    SeaBattle.ctx.drawImage(SeaBattle.imgTorpedo, this.x-this.width/2, this.y);
  }
  this.getBBox = function() {
    this.bbox.left = this.x-this.width/2;
    this.bbox.top = this.y;
    this.bbox.right = this.x+this.width/2;
    this.bbox.bottom = this.y+this.height;
    return this.bbox;
  }
  this.move = function move() {
    this.y--;
    if (this.y < this.bound)
      return false;
    return true;
  }
  this.setLocation = function(x, y) {
    this.x = x;
    this.y = y;
  }
}
```

清单 3: 鱼雷的当前位置与其图像的顶部中心重合。

清单 3 首先将传递给它的`bound`参数的参数保存在一个同名的鱼雷对象属性中，然后引入了另外七个对象属性:

*   `bbox`引用一个矩形对象，作为碰撞检测的边界框。
*   `height`以像素为单位指定鱼雷图像的高度。
*   `width`以像素为单位指定鱼雷图像的宽度。
*   `draw()`绘制鱼雷图像。
*   `getBBox()`以对象的当前`x`值为中心返回更新后的`bbox`对象。
*   将鱼雷向上推进一个像素。这个函数返回 true，直到鱼雷图像的顶部超过它的上限，这时它返回 false。
*   `setLocation(x, y)`指定鱼雷的位置，该位置与鱼雷图像的顶部中心重合。它的参数存储在鱼雷对象的`x`和`y`属性中。

## 检测碰撞

第 3 部分的`update()`功能依赖于`intersects(r1, r2)`功能来确定鱼雷和舰船之间或者深水炸弹和潜艇之间是否发生了碰撞。清单 4 展示了这个函数的实现。

```
intersects: function(r1, r2) {
  return !(r2.left > r1.right ||
           r2.right < r1.left ||
           r2.top > r1.bottom ||
           r2.bottom < r1.top);
}
```

**清单 4:** 测试两个矩形的交集。

清单 4 确定它的两个矩形参数(从`getBBox()`调用返回)是否相交，首先确定第二个矩形(`r2`)是否完全位于第一个矩形(`r1`)的右边或左边、下面或上面，然后否定结果。

如果您回忆一下第 3 部分，船的边界框并没有完全垂直地以对象的当前 y 位置为中心。虽然顶部是垂直居中的，但底部不是，因为我将`this.y+2`而不是`this.y+this.height/2`分配给了`this.bbox.bottom`。

![](img/100198f46e0a0806fcf42906101f7db4.png "seabattle-ship-left")

图 1:船只图像用红色边框勾勒出来，清楚地显示了空白垂直空间的范围。

为什么不同？左侧和右侧的每幅船图像都显示了船下方的大量空白垂直空间。图 1 显示了船只面向左侧的图像。

如果我指定`this.y+this.height/2`为底部界限，交叉鱼雷会在离船底太远的地方爆炸，看起来不可信。潜艇不存在这个问题，它的图像没有过多的垂直空间。

## 制造爆炸

`update()`函数通过调用`makeExplosion(isShip)`构造函数创建一个爆炸对象来响应碰撞。当船爆炸时，传递的布尔参数为真，否则为假。清单 5 展示了这个构造函数是如何实现的。

```
makeExplosion: function(isShip) {
  this.isShip = isShip;
  this.counter = 0;
  this.height = SeaBattle.imgExplosion[0].height;
  this.imageIndex = 0;
  this.width = SeaBattle.imgExplosion[0].width;
  this.advance = function() {
    if (++this.counter < 4)
      return true;
    this.counter = 0;

    if (++this.imageIndex == 8)
    {
      if (this.isShip)
        SeaBattle.ship.exploded = true;
      else
        SeaBattle.sub.exploded = true;
    }
    else
      if (this.imageIndex > 16)
      {
        this.imageIndex = 0;
        return false;
      }
    return true;
  }
  this.draw = function() {
    SeaBattle.ctx.drawImage(SeaBattle.imgExplosion[this.imageIndex],
                            this.x-this.width/2, this.y-this.height/2);
  }
  this.setLocation = function(x, y) {
    this.x = x;
    this.y = y;
    try
    {
      SeaBattle.audBomb.play();
    }
    catch (e)
    {
      // Safari without QuickTime results in an exception
    }
  }
}
```

**清单 5:** 一旦指定了爆炸的位置，爆炸就开始播放它的音频。

清单 5 的`makeExplosion(isShip)`构造函数首先将传递给参数`isShip`的参数保存在爆炸对象的`isShip`属性中，然后引入七个额外的对象属性:

*   `counter`用来减缓爆炸的推进速度，使其不会消失得太快。
*   `height`以像素为单位指定每个爆炸图像的高度。
*   `imageIndex`指定要显示的下一个分解图像的从零开始的索引。
*   `width`以像素为单位指定每个爆炸图像的宽度。
*   每次`counter`等于 4 时，`advance()`推进爆炸。当`imageIndex`等于八时，差不多一半的爆炸就完成了，爆炸的船只或潜艇被移走。
*   `draw()`绘制下一张爆炸图像。
*   `setLocation(x, y)`指定爆炸的位置，它与每个爆炸图像的中心重合。它的参数存储在爆炸对象的`x`和`y`属性中。

设定爆炸位置后，通过`SeaBattle.audBomb.play();`播放爆炸音效。如果您使用的是没有 Quicktime 的 Safari 浏览器，此浏览器会抛出异常。异常处理程序可以显示一条消息或采取一些其他操作。目前，我们忽略例外。

## 结论

我们对 SeaBattle 建筑的探索即将完成。下周五， [Part 5](https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-5 "Gaming: Battle on the High Seas, Part 5") 将首先向你展示游戏场景是如何在画布上绘制的，从而完成这一探索。接下来，它简要回顾了 HTML5 的音频、画布和 Web 存储 API，以帮助这些 API 的新手更好地理解 SeaBattle。在提供了增强这个游戏的想法之后，第 5 部分通过将 SeaBattle 带到桌面之外来结束这个系列。

## 分享这篇文章