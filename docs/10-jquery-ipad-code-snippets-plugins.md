# 10 个有用的 jQuery iPad 代码片段和插件

> 原文：<https://www.sitepoint.com/10-jquery-ipad-code-snippets-plugins/>

我们已经为 iPad 设备整合了一些易于使用的技巧、代码片段和插件。请务必在评论中让我们知道你发现了哪些有用的代码片段和插件，以及你知道的任何其他有用的代码片段和插件。

## 1.使用 JavaScript 在 Safari 中检测 iPad 方向

设计您的网站或重新排列您的内容，以完全符合您的 iPad 的方向。这里有一个关于如何检测 iPad 设备的当前方向的例子，或者通过按一个按钮，或者当方向改变时，使用一个名为 onOrientationChange 的事件…

```
<button onclick="detectIPadOrientation();">What's my Orientation?</button> 
```

使用媒体定义，您也可以使用 CSS 样式表:

```
<link rel="stylesheet" media="all and (orientation:portrait)" href="portrait.css"/>
<link rel="stylesheet" media="all and (orientation:portrait)" href="landscape.css"/>
```

[来源](http://favo.asia/2010/07/detecting-ipad-orientation-using-javascript/)

## 2.jQuery 增加了对 iPad 的拖拽/触摸支持

为 iPad 和支持触摸的设备应用拖动/触摸支持的 jQuery 代码片段。

```
//iPAD Support
$.fn.addTouch = function(){
  this.each(function(i,el){
    $(el).bind('touchstart touchmove touchend touchcancel',function(){
      //we pass the original event object because the jQuery event
      //object is normalized to w3c specs and does not provide the TouchList
      handleTouch(event);
    });
  });

  var handleTouch = function(event)
  {
    var touches = event.changedTouches,
            first = touches[0],
            type = '';

    switch(event.type)
    {
      case 'touchstart':
        type = 'mousedown';
        break;

      case 'touchmove':
        type = 'mousemove';
        event.preventDefault();
        break;

      case 'touchend':
        type = 'mouseup';
        break;

      default:
        return;
    }

    var simulatedEvent = document.createEvent('MouseEvent');
    simulatedEvent.initMouseEvent(type, true, true, window, 1, first.screenX, first.screenY, first.clientX, first.clientY, false, false, false, false, 0/*left*/, null);
    first.target.dispatchEvent(simulatedEvent);
  };
};
```

[来源](http://www.jquery4u.com/mobile/jquery-add-dragtouch-support-ipad/#.UEAbXsHiY0U)

## 3.适用于 iPad、iPhone 和 Android 的 TouchSwipe jQuery 插件

一个 jquery 插件，用于 iPad、iPhone 等触摸输入设备上的 jQuery。
[![TouchSwipe](img/a65e215fa2a0a51b003f5b5c1849839f.png)](http://labs.skinkers.com/touchSwipe/) 
[来源](http://labs.skinkers.com/touchSwipe/) [演示](http://labs.skinkers.com/touchSwipe/demo/1_Basic_swipe.php)

## 4.jQuery iPad 一指卷轴

触摸设备(iPad，iPhone，Android 等)有非常奇怪的滚动溢出行为:自动元素。iPad 需要两个手指滚动，不要添加任何滚动条来使它变得明显。这个插件允许你用一个手指滚动一个溢出:自动元素。
[![jQuery iPad one finger scroll](img/e32f3d304989577939ad0d760ce0ff24.png)](http://forrst.com/posts/jQuery_iPad_one_finger_scroll-B30) 
[来源](http://forrst.com/posts/jQuery_iPad_one_finger_scroll-B30) [演示](https://jsfiddle.net/mfXMn/)

## 5.jQuery 检测移动设备–iPhone iPod iPad

jQuery 代码片段，用于检测用户是否正在使用移动设备(特别是 iPhone、iPod 或 iPad)查看网站。

```
jQuery(document).ready(function($){
	var deviceAgent = navigator.userAgent.toLowerCase();
	var agentID = deviceAgent.match(/(iphone|ipod|ipad)/);
	if (agentID) {

        // mobile code here

	}
});
```

[来源](http://www.jquery4u.com/mobile/jquery-detect-mobile-devices-iphone-ipod-ipad/#.UEKkycHiY0V)

## 6.适用于 iPad 和桌面浏览器的多选选项列表 jquery 插件

在桌面和 iPad 浏览器中看起来相似的多行/多选选项列表。

我们可以很容易地使用常用的 Visualforce 标记，即<selectlist>来实现此目的，如下所示:</selectlist>

[来源](http://www.tgerm.com/2012/01/ipad-multiselect-picklist-jquery-plugin.html)

## 7.IPAD 上的 JQUERY 点击事件

解决方法。这是 apple.com 开发者文档中给出的建议。这基本上是在 userAgent 字符串中搜索 iPad(不区分大小写)。如果用户使用 iPad，我们使用 touchstart，如果没有，我们默认返回标准点击。

您需要的代码是:

```
var ua = navigator.userAgent, 
    event = (ua.match(/iPad/i)) ? "touchstart" : "click";

$("#theElement").bind(event, function() {
     // jquery code
}
```

[来源](http://www.brightec.co.uk/blog/jquery-click-events-ipad)

## 8.使用 jQuery 在您的网站中轻松使用 iPad 手势

jQuery 让这变得如此容易集成和使用，以至于我忍不住去摆弄它。

首先，确保你的站点中包含了最新的 jQuery 库。像这样直接从站点中包含它: