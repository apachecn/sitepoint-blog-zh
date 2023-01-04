# jQuery 模拟了一个切换事件

> 原文：<https://www.sitepoint.com/jquery-simulate-toggle-event/>

使用 jQuery 来“触发”模拟一个切换事件，你可以简单地触发一个点击事件，就像这样:

```
.trigger('click');
```

然后说，如果你有两个按钮做完全相同的事情，你可以模拟一个点击按钮的事件处理程序，以促进代码重用。

```
//simulate an event
$(this).parents('.parent').find('.controls cancel').trigger('click');
```

```
 ## HTML 
```

## 分享这篇文章