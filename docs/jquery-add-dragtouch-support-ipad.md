# jQuery 增加了对 iPad 的拖拽/触摸支持

> 原文：<https://www.sitepoint.com/jquery-add-dragtouch-support-ipad/>

为 iPad 和支持触摸的设备应用拖动/触摸支持的 jQuery 代码片段。对于 jQuery UI 可拖动对象和浮动对象可能特别有用。

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

## 分享这篇文章