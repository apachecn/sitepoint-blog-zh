# jQuery 获取具有当前焦点的元素

> 原文：<https://www.sitepoint.com/jquery-element-current-focus/>

获取具有当前焦点的元素并在失去焦点时执行 AJAX 自动保存的 jQuery 代码片段。

```
//get active element of focus using js
$(document.activeElement)
```

```
//change focus when tabbed across into new item
$('.item :input').live('focus', function()
{
  var thisItem = $(this).parents('.item');
  var thisId = thisItem.attr('id');
  $('.item').removeClass('item-selected'); //remove all focus
  thisItem.addClass('item-selected');  //add focus to current item

  //save those items that have changed
  $.each($('.item-changed'), function(i,v)
  {
      var currItemId = $(this).attr('id');
      //exclude current item
      if (!_this.helpers.itemHasFocus(currItemId))
      {
         console.log('saving '+currItemId);
         _this.save.item(currItemId); /* AJAX auto save */
         $(this).removeClass('item-changed');
      }
  });
});
```

## 分享这篇文章