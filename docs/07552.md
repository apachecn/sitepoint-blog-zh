# 游戏:公海之战，第三部分

> 原文：<https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-3/>

[上周五](https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-2 "Gaming: Battle on the High Seas, Part 2")，在我们的游戏系列中，我开始通过关注`SeaBattle`对象的`init(width, height)`函数以及相关的`rnd(limit)`和`supports_html5_storage()`助手函数来探索 SeaBattle 的架构。本文是五篇文章中的第三篇，通过关注`update()`函数和`makeShip(x, y, bound1, bound2)`构造函数继续探索游戏架构。


## 更新 SeaBattle

清单 1 展示了`update()`函数的实现。

```
update: function() {
  if (SeaBattle.state == SeaBattle.STATE_INIT)
    return;

  if ((SeaBattle.state == SeaBattle.STATE_TITLE ||
       SeaBattle.state == SeaBattle.STATE_WINLOSE ||
       SeaBattle.state == SeaBattle.STATE_RESTART) && keydown.return)
  {
    if (SeaBattle.state == SeaBattle.STATE_RESTART)
    {
      SeaBattle.score = 0;
      SeaBattle.lives = 4;
    }
    SeaBattle.ship = new SeaBattle.makeShip(SeaBattle.width/2, SeaBattle.height/3, 0, SeaBattle.width-1);
    SeaBattle.sub = new SeaBattle.makeSub(SeaBattle.rnd(2) == 0
                          ? -50+SeaBattle.rnd(30)
                          : SeaBattle.width+SeaBattle.rnd(100),
                            2*SeaBattle.height/3-
                            SeaBattle.rnd(SeaBattle.height/6),
                            -100, SeaBattle.width+100);
    SeaBattle.state = SeaBattle.STATE_PLAY;
  }

  if (SeaBattle.state != SeaBattle.STATE_PLAY)
    return;

  if (SeaBattle.explosion != null)
  {
    if (SeaBattle.explosion.isShip)
      SeaBattle.sub.move();

    for (var i = 0; i < SeaBattle.MAX_DC; i++)
      if (SeaBattle.dc[i] != null)
        if (!SeaBattle.dc[i].move())
          SeaBattle.dc[i] = null;

    for (var i = 0; i < SeaBattle.MAX_TORP; i++)
      if (SeaBattle.torp[i] != null)
        if (!SeaBattle.torp[i].move())
          SeaBattle.torp[i] = null;

    if (!SeaBattle.explosion.advance())
    {
      SeaBattle.ship = null;
      SeaBattle.sub = null;
      for (var i = 0; i < SeaBattle.MAX_DC; i++)
        SeaBattle.dc[i] = null;
        for (var i = 0; i < SeaBattle.MAX_TORP; i++)
          SeaBattle.torp[i] = null;
        SeaBattle.state = SeaBattle.STATE_WINLOSE;
        if (SeaBattle.explosion.isShip)
        {
          SeaBattle.lives--;
          if (SeaBattle.lives == 0)
          {
            SeaBattle.state = SeaBattle.STATE_RESTART;
            SeaBattle.msg = "Game Over! Press RETURN to play "+"again!";
          }
        }
        else
        {
          SeaBattle.score += 100;
          if (SeaBattle.score > SeaBattle.hiScore)
          {
            SeaBattle.hiScore = SeaBattle.score;
            if (SeaBattle.supports_html5_storage())
              localStorage.setItem("hiScore", SeaBattle.hiScore);
          }
        }
        SeaBattle.explosion = null;
      }
    return;
  }

  if (keydown.left)
    SeaBattle.ship.moveLeft();

  if (keydown.right)
    SeaBattle.ship.moveRight();

  if (keydown.space)
  {
    for (var i = 0; i < SeaBattle.MAX_DC; i++)
      if (SeaBattle.dc[i] == null)
      {
        var bound = SeaBattle.hillTops[SeaBattle.ship.x];
        SeaBattle.dc[i] = new SeaBattle.makeDepthCharge(bound);
        SeaBattle.dc[i].setLocation(SeaBattle.ship.x, SeaBattle.ship.y);
        break;
      }
      keydown.space = false;
  }

  SeaBattle.sub.move();
  if (SeaBattle.sub.x > 0 && SeaBattle.sub.x < SeaBattle.width && SeaBattle.rnd(15) == 1)
    for (var i = 0; i < SeaBattle.MAX_TORP; i++)
      if (SeaBattle.torp[i] == null)
      {
        SeaBattle.torp[i] = new SeaBattle.makeTorpedo(SeaBattle.height/3);
        SeaBattle.torp[i].setLocation(SeaBattle.sub.x, SeaBattle.sub.y-SeaBattle.imgTorpedo.height);
        break;
      }

  for (var i = 0; i < SeaBattle.MAX_DC; i++)
    if (SeaBattle.dc[i] != null)
      if (!SeaBattle.dc[i].move())
        SeaBattle.dc[i] = null;
      else
      {
        if (SeaBattle.intersects(SeaBattle.dc[i].getBBox(), SeaBattle.sub.getBBox()))
        {
          SeaBattle.explosion = new SeaBattle.makeExplosion(false);
          SeaBattle.explosion.setLocation(SeaBattle.dc[i].x, SeaBattle.dc[i].y);
          SeaBattle.msg = "You win! Press RETURN to keep playing!";
          SeaBattle.dc[i] = null;
          return;
        }
      }

  for (var i = 0; i < SeaBattle.MAX_TORP; i++)
    if (SeaBattle.torp[i] != null)
      if (!SeaBattle.torp[i].move())
        SeaBattle.torp[i] = null;
      else
      {
        if (SeaBattle.intersects(SeaBattle.torp[i].getBBox(), SeaBattle.ship.getBBox()))
        {
          SeaBattle.explosion = new SeaBattle.makeExplosion(true);
          SeaBattle.explosion.setLocation(SeaBattle.torp[i].x, SeaBattle.torp[i].y);
          SeaBattle.msg = "You lose! Press RETURN to keep playing!";
          SeaBattle.torp[i] = null;
          return;
        }
      }
}
```

**清单 1:** SeaBattle 在初始化状态下不更新游戏。

清单 1 首先检查了`state`属性，以了解它是否等于`STATE_INIT`。如果是，则`update()`函数返回。当游戏资源还在加载的时候，继续执行`update()`是没有意义的。

接下来，将`state`与`STATE_TITLE`、`STATE_WINLOSE`和`STATE_RESTART`进行比较。在这种状态下，游戏不进行。要让它发挥作用，用户需要按回车键(`keydown.return`存在且为真)。

如果游戏正在重新开始(`state`等于`STATE_RESTART`)，则分数被重置为零，船只生命数被设置为四。无论重启，输赢，还是冠军状态，`ship`和`sub`对象被创建，`STATE_PLAY`被分配给`state`。

调用`makeShip(x, y, bound1, bound2)`构造函数来创建船。该对象水平居中，垂直位于画布顶部下方画布高度的三分之一处。界限防止船被移动超过帆布界限。

一个相似的建造者创造了潜水艇。该对象水平随机放置在左侧或右侧画布边缘之外。它也垂直随机地位于画布的中间三分之一。边界的选择是为了让潜艇能够超越帆布的限制。

此时，将`state`与`STATE_PLAY`进行比较，以确定 SeaBattle 是否处于玩游戏状态。之前与`STATE_TITLE`、`STATE_WINLOSE`、`STATE_RESTART`的比较可能因为`keydown.return`评估为假而落空。

在清单 1 检查控制船只的用户输入之前，必须测试船只或潜艇爆炸的可能性。从一艘爆炸的船上移动或发射深水炸弹，或者从一艘爆炸的潜艇上移动或发射鱼雷是没有意义的。

当分解正在进行时，`explosion`属性引用一个分解对象。当船爆炸时，这个对象的`isShip`属性被设置为 true。在这种情况下，潜艇仍然可以移动；这个任务通过调用`sub`对象的`move()`函数来处理。

在船只或潜艇开始爆炸之前，任何深水炸弹或鱼雷都可以通过调用每个对象的`move()`功能来移动。当深水炸弹或鱼雷不能再移动时，`move()`返回 false，物体作废。

`explosion`对象的`advance()`函数返回 true，表示爆炸正在进行。当返回 false 时，爆炸结束，相关游戏对象作废，`state`设置为`STATE_WINLOSE`。

如果飞船爆炸了，生命的数量就会减少。当该值达到零时，游戏结束，并准备合适的消息。但是，如果潜艇已经爆炸，分数增加 100 分，并修改和保存高分(必要时)。

在没有爆炸的情况下，清单 1 的下一个任务是检查左箭头键、右箭头键或空格键的按下。按下左箭头键或右箭头键会调用`ship`的`moveLeft()`或`moveRight()`功能。

相比之下，按空格键会导致尝试发射深水炸弹，但只有在深水炸弹的最大数量没有发挥作用的情况下。深水炸弹的初始位置是船的中心，其下限是与船的 x 坐标重合的山顶。

潜艇现在正在移动，发射一枚鱼雷，如果没有离开屏幕，一个随机选择的整数等于一个特定的值，鱼雷的最大数量不在游戏中。一枚鱼雷的初始位置是潜艇的中心，减去鱼雷高度，其上限是水线。

清单 1 最后检查深水炸弹和潜艇之间的碰撞，或者鱼雷和船只之间的碰撞。碰撞导致一个爆炸物体被创建，其位置被设置为深水炸弹/鱼雷坐标，一个合适的信息被分配给`msg`。

## 制造一艘船

`update()`函数负责创建毁灭者船和其他游戏对象。它在`makeShip(x, y, bound1, bound2)`构造器的帮助下完成船只的创建。清单 2 展示了这个构造函数的实现。

```
makeShip: function(x, y, bound1, bound2) {
  this.x = x;
  this.y = y;
  this.bound1 = bound1;
  this.bound2 = bound2;
  this.bbox = { left: 0, top: 0, right: 0, bottom: 0 };
  this.LEFT = 0;
  this.RIGHT = 1;
  this.dir = this.LEFT,
  this.exploded = false;
  this.height = SeaBattle.imgShipLeft.height;
  this.vx = 2;
  this.width = SeaBattle.imgShipLeft.width;
  this.draw = function() {
    SeaBattle.ctx.drawImage((this.dir == this.LEFT)?
                            SeaBattle.imgShipLeft :
                            SeaBattle.imgShipRight,
                            this.x-this.width/2,
                            this.y-this.height/2);
    return;
  }

  this.getBBox = function() {
    this.bbox.left = this.x-this.width/2;
    this.bbox.top = this.y-this.height/2;
    this.bbox.right = this.x+this.width/2;
    this.bbox.bottom = this.y+2;
    return this.bbox;
  }

  this.moveLeft = function() {
    this.dir = this.LEFT;
    this.x -= this.vx;
    if (this.x-this.width/2 < this.bound1)
    {
      this.x += this.vx;
      this.vx = SeaBattle.rnd(4)+1;
    }
  }

  this.moveRight = function() {
    this.dir = this.RIGHT;
    this.x += this.vx;
    if (this.x+this.width/2 > this.bound2)
    {
      this.x -= this.vx;
      this.vx = SeaBattle.rnd(4)+1;
    }
  }
}
```

**清单 2:** 船只边界框的底部被抬高，这样鱼雷会在靠近船底的地方爆炸。

清单 2 首先将其参数保存在同名的 ship 对象属性中，然后引入了另外 12 个对象属性:

*   `bbox`引用一个矩形对象，作为碰撞检测的边界框。这个对象作为参数传递给`intersects(r1, r2)`函数。
*   `LEFT`是与`dir`属性一起使用的伪常数。
*   `RIGHT`是与`dir`属性一起使用的伪常数。
*   `dir`指定船只当前的方向(朝左或朝右)。船最初是朝左的。
*   `exploded`表示船只是否已经爆炸(当赋值为真时)或是否已经爆炸(当赋值为假时)。
*   `height`以像素为单位指定船只图像的高度。
*   `vx`根据船只移动的像素数指定船只的水平速度。默认值是 2。
*   `width`以像素为单位指定船只图像的宽度。
*   `draw()`绘制船只的左侧或右侧图像。绘制图像时，其中心位置与船只对象的`x`和`y`属性值一致。
*   `getBBox()`返回更新后的`bbox`对象。该对象被更新以适应船的水平位置的变化。
*   `moveLeft()`将船向左移动`vx`指定的像素数。当船到达画布的左边缘时，它被阻止进一步向左移动，并且它的速度改变。
*   `moveRight()`将船向右移动`vx`指定的像素数。当船到达画布的右边缘时，它被阻止进一步向右移动，并且它的速度改变。

## 结论

`update()`函数依赖于`makeShip(x, y, bound1, bound2)`以及其他以`make`为前缀的构造函数来创建各种游戏对象。该游戏系列的[第四部分](https://www.sitepoint.com/gaming-battle-on-the-high-seas-part-4 "Gaming: Battle on the High Seas, Part 4")继续探索 SeaBattle 的架构，重点关注这些其他构造函数以及`intersects(r1, r2)`函数，该函数支持碰撞检测。下周五见！

## 分享这篇文章