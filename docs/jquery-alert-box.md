# jQuery 警告框是或否

> 原文：<https://www.sitepoint.com/jquery-alert-box/>

jQuery 中有几个选项可以为警报和对话框捕获事件，包括[插件和对话框](http://www.jquery4u.com/windows/14-jquery-modal-dialog-boxes/)。jQuery UI 也为我们提供了一些[自定义对话框](http://jqueryui.com/demos/dialog/#modal-confirmation)的选项。这个页面也提供了一些不同的[类型的模态窗口](http://www.ericmmartin.com/projects/simplemodal-demos/)。

你也可以考虑使用普通的 JavaScript，你可以这样做，又快又简单！

```
var answer = confirm('Are you sure you want to delete this?');
if (answer)
{
  console.log('yes');
}
else
{
  console.log('cancel');
}
```

## 分享这篇文章