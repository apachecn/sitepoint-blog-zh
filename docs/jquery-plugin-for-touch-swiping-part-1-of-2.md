# 用于触摸滑动的 jQuery 插件——第 1 部分，共 2 部分

> 原文：<https://www.sitepoint.com/jquery-plugin-for-touch-swiping-part-1-of-2/>

本文将解释创建一个 jQuery 插件的步骤，该插件可以检测 iPhone 和基于 Android 的设备等触摸设备上的水平滑动动作。本文是两部分系列的第一部分。在本文中，我们将创建一个图像转盘，它可以响应用户的输入并相应地改变转盘的位置。[第二篇](https://www.sitepoint.com/jquery-plugin-for-touch-swiping-part-2-of-2/)将通过增加刷卡检测来扩展插件。


### HTML 和 CSS

在我们继续 JavaScript 之前，让我们看一下图片轮播的 HTML 和 CSS，它将用于演示 Swiper 插件。HTML 如下所示。

```
<div style="width: 330px; height: 200px;">
  <div id="target">
    <div>
      <div><img alt="" src="rexy.jpg" /></div>
      <div><img alt="" src="xena.jpg" /></div>
      <div><img alt="" src="xenaagain.jpg" /></div>
      <div><img alt="" src="rexyagain.jpg" /></div>
    </div>
  </div>
</div>
```

类似地，转盘的 CSS 如下所示。

```
img { /*100% width to scale the height proportionately*/
  width: 100%;
  margin: 0;
}

.frame {
  width: 100%;
  height: 100%;
  border: 1px solid #ccc;
  overflow: hidden;
  position: relative;
}

.pictures {
  position: absolute;
  width: 400%; /*change accordingly*/
  left: 0%;
}

.pictures:after {
  content: "\0020";
  display: none;
  height: 0;
}

.pictures .pic {
  width: 25%; /*change with respect to .pictures*/
  float: left;
}
```

内部容器(`.pictures`)被设置为 400%以包含四个图像。每个图像的容器(`.pic`)被设置为 25%，这样图像的宽度最终为 330 像素。如果您改变图像的数量或者使用绝对值而不是百分比，那么您需要相应地改变`.pictures`和`.pic`元素的宽度值。

通过向左浮动，使图像水平对齐。框架(`.frame`)一次只显示一幅图像。有了这个设置，我们就可以通过改变`.pictures` `<div>`元素的`left`属性来“滑动”转盘。

### Java Script 语言

这是插件的框架:

```
(function ($) {
  'use strict';

  var Swiper = function (el, callbacks) {
  }

  $.fn.swiper = function (callbacks) {
    if (typeof callbacks.swiping !== 'function') {
      throw '"swiping" callback must be defined.';
    }

    this.each(function () {
      var tis = $(this),
        swiper = tis.data('swiper');

      if (!swiper) { //i.e. plugin not invoked on the element yet
        tis.data('swiper', (swiper = new Swiper(this, callbacks)));
      }
    });
  };
}(jQuery));
```

这个清单是创建 jQuery 插件的样板代码。大部分复杂性由内部类`Swiper`处理，其方法尚未定义。`Swiper`负责读取浏览器产生的事件并调用回调。插件被定义在一个闭包里，这样`Swiper`类就不会被外部代码错误地覆盖。通过将实例化的`Swiper`类与`swiper`数据属性相关联，插件还可以避免多次绑定到一个元素。

```
var Swiper = function (el, callbacks) {
  var tis = this;
  this.el = el;
  this.cbs = callbacks;
  this.points = [0, 0];

  //perform binding
  this.el.addEventListener('touchstart', function (evt) {
    tis.start(evt);
  });
  this.el.addEventListener('touchmove', function (evt) {
    evt.preventDefault();
    tis.move(evt);
  });
};
```

在上面的清单中，`Swiper`构造函数实例化了对象的属性和事件处理程序。`points`属性是一个两单元格数组，存储手指在第一个单元格中的起始位置和在第二个单元格中的结束位置。我们将在后面的清单中看到这个数组的用法。它的值最初都为零。

构造函数绑定`touchstart`和`touchmove`事件，并将事件代理到`Swiper`类中相应的方法。`touchstart`绑定用手指的初始位置初始化`points`数组。`touchmove`绑定给了我们手指的移动，我们将把它传递给回调函数来相应地偏移转盘。

```
Swiper.prototype.start = function (evt) {
  if (evt.targetTouches && evt.targetTouches.length === 1) {
    if (evt.targetTouches[0].offsetX) {
      this.points[0] = evt.targetTouches[0].offsetX;
    } else if (evt.targetTouches[0].layerX) {
      this.points[0] = evt.targetTouches[0].layerX;
    } else {
      this.points[0] = evt.targetTouches[0].pageX;
    }
    //make initial contact with 0 difference
    this.points[1] = this.points[0];
  }
};
```

上面的清单显示了`start()`方法，它获取事件并读取屏幕上生成的一组触摸。在具有多点触摸功能的设备中，这意味着几乎所有的现代智能手机和平板电脑，该属性是一个存储屏幕上所有接触点位置的数组。在这个实现中，我们跟踪一个接触点，因为我们跟踪使用一个手指完成的单个滑动手势。

我们正在检查触摸事件的不同属性，以适应不同设备上触摸行为的不同实现。这曾经被要求使[为不同的设备](http://www.nogginbox.co.uk/blog/canvas-and-multi-touch)工作。然而今天，我测试过的设备都生成了`pageX`属性。

因为我们只检查水平滑动手势，所以忽略了`pageY`属性。我们还将`points`属性的单元格设置为相同的值，这样起点和终点之间的初始差值为零。

下面列出了`touchmove`事件和其他助手方法的函数绑定。

```
Swiper.prototype.diff = function () {
  return this.points[1] - this.points[0];
};

Swiper.prototype.move = function (evt) {
  if (evt.targetTouches && evt.targetTouches.length === 1) {
    if (evt.targetTouches[0].offsetX) {
      this.points[1] = evt.targetTouches[0].offsetX;
    } else if (evt.targetTouches[0].layerX) {
      this.points[1] = evt.targetTouches[0].layerX;
    } else {
      this.points[1] = evt.targetTouches[0].pageX;
    }
    this.cbs.swiping(this.diff());
    this.points[0] = this.points[1];
  }
};
```

`diff()`方法只是计算最后一个点(随着用户移动手指而改变)和前一个点之间的差异。下图对此进行了说明。

[![image1](img/c4b37bc483dd3c96bcc6edcf9496a167.png)](https://www.sitepoint.com/wp-content/uploads/2013/02/image1.png)

手指“拖动”距离的图示。

`move()`方法还检查不同的属性，以获得正确的属性，并存储到`points`属性的第二个单元格中。在存储该值之后，用手指的前一位置和新位置之间的差值调用回调函数。回调函数负责改变转盘的位置。这一点解释如下。

调用回调后，前一个位置的值被当前位置的值替换。下次调用回调时，差异将是当前位置和前一个位置之间的位移，而不是起始位置。如果我们希望转盘的运动与手指的运动一致，这是必需的。没有这条线，转盘的移动累积了差异，并且结果是响应于手指的小移动的图像的大位移，这对于平滑的用户体验显然是不期望的。

下面的清单调用了这个插件。

```
var target = $('#target'),
  pictures = $('.pictures', target),
  MAX_LEFT = -990,
  MAX_RIGHT = 0,
  currPos = 0,
  cb = {
    swiping: function (displacement) {
      currPos += displacement;
      if (currPos > MAX_RIGHT) {
        currPos = MAX_RIGHT;
      } else if (currPos < MAX_LEFT) {
        currPos = MAX_LEFT;
      }
      pictures.css('left', currPos + 'px');
    }
  };

target.swiper(cb);
```

我们使用元素的`id`来获取元素。我们还需要目标中的`.pictures`元素的句柄，因为通过改变这个元素的`left` CSS 属性来改变转盘的位置。

我们用`MAX_LEFT`和`MAX_RIGHT`变量设置转盘位置的左右极限。这些值必须根据转盘的大小进行更改。使用它们使得用户不会将转盘滚动到空白空间。`MAX_RIGHT`变量决定了手指可以向右拖动旋转木马多远来点击最左边的图像。自然这个值就是`0`。`MAX_LEFT`变量限制手指可以向左移动转盘多远。因为有四个图像，为了显示最右边的一个，左边的三个图像必须被移动。这些值是这样导出的:

`330 (width of one image) * 3 = 990`

我们还有一个变量`currPos`，它存储转盘的当前位置。或者，我们可以像这样得到转盘的位置:

```
currPos = parseInt(pictures.css('left'));
```

首选方法是使用变量。唯一的原因是性能——检索元素的`left`属性并将其转换为整数肯定比访问变量值消耗更多的处理能力。这就是我们在浏览器界面上添加行为的事实，所以保持插件的精简性是很重要的。

回调被指定为 JSON 文本中的一个属性。为什么不简单地把它作为函数传递呢？好了，这是为这个系列的第二部分做准备，在那里我们将解释如何给插件添加滑动手势检测。

最后一点:在 iOS 设备(iPhones 和 iPads)上，当你滚动旋转木马时，浏览器窗口会有反弹效果。如果轮播靠近页面的底部或顶部(如这里的情况),这是很明显的。为了防止这种情况发生，我们在`touchmove`事件上调用`preventDefault()`方法。顺便提一下，通过调用`preventDefault()`方法，它可以防止事件在 <abbr title="Document Object Model">DOM</abbr> 层次结构中冒泡，从而提高性能，这在 Nexus One 等较慢的设备上尤为明显。我已经在 iPad 2 (iOS 6.0.1)、Nexus One (Android 2.3.6)和 Galaxy Note II (Android 4.1.2)上测试了该插件。如果你使用过其他设备/操作系统，请在评论中告诉我们！

*   [在线演示](http://jspro.rojakcoder.com/touchswipe1)
*   [下载源代码](https://github.com/jsprodotcom/source/blob/master/TouchSwipe.zip)

## 分享本文