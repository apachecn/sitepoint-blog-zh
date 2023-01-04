# jQuery 1.8 之前和之后的 AJAX

> 原文：<https://www.sitepoint.com/ajax-jquery-1-8/>

这里简单介绍一下 jQuery 1.8 和更新版本之前的 AJAX。别忘了[。成功()和。jQuery 1.9.1](http://www.jquery4u.com/?p=17011) 仍然支持 error()，因此它不会破坏您使用的旧代码和插件。我还起草了一些[新的 jQuery.ajax()例子 jQuery 1.9+](http://www.jquery4u.com/?p=17012) 来看看吧！

> **弃用声明:**jqxhr . success()、jqXHR.error()和 jqXHR.complete()回调从 jQuery 1.8 开始弃用。若要为最终移除它们准备代码，请改用 jqXHR.done()、jqXHR.fail()和 jqXHR.always()。

### 在 jQuery 1.8 之前使用 AJAX 获取 HTML

```
$.ajax({
    url: 'test.html',
    dataType: 'html',
    success: function (data, textStatus, xhr)
    {
        console.log(data);
    },
    error: function (xhr, textStatus, errorThrown)
    {
        console.log('error: '+textStatus);
    }
});
```

### 使用 AJAX jQuery 1.8+获取 HTML

```
// cache: false is used to fetch the latest version

$.ajax({
    url: "test.html",
    cache: false
})
.done(function(data, textStatus, jqXHR)
{
    console.log(data);
})
.fail(function(jqXHR, textStatus, errorThrown)
{
    console.log('error: '+textStatus);
});
```

可以为一个$指定多个回调。ajax()请求。回调方法。done()，fail()，always()，然后。()都是 jqXHR 对象的 promise 方法。所有这些回调方法都会在$。ajax()进程终止。承诺回调按照注册的顺序被调用。

## 分享这篇文章