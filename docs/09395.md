# jQuery 检查开关是否打开/关闭

> 原文：<https://www.sitepoint.com/jquery-check-toggle-open/>

检查开关是打开还是关闭的简单 jQuery 代码片段。基本上，当前状态可以通过使用这个测试来确定:

```
$(this).is(":hidden").
```

另一种方法是使用 data 属性将“打开”或“关闭”状态附加到切换按钮，如下所示:

```
if (this.data('state') === 'closed') {
    $('.' + toggleBtnClass).innerText(moreText);
    _this.data('state', 'open'); /*add data to store state*/
} else {
    $('.' + toggleBtnClass).innerText(lessText);
    _this.data('state', 'closed'); /*add data to store state*/
}
```

要了解这一点，请查看 jQuery.autoToggles 插件。

## 分享这篇文章