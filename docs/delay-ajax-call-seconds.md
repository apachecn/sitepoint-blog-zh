# 延迟 AJAX 调用 X 秒

> 原文：<https://www.sitepoint.com/delay-ajax-call-seconds/>

将 AJAX 调用延迟 X 秒的 jQuery 代码片段。可以用于在通过 AJAX 从服务器端获取数据之前需要操作的事件。

更新:使用 setTimeout()函数有一个更简单的方法——见下面的例子。

尽情享受吧！:)

```
var pendingCall = { timeStamp: null, procID: null };

$('li a').click(function (e) {
    e.preventDefault();
    var getUrl = $(this).attr("href");
	var timeStamp = Date.now();

    var printCall = function () {
        $.ajax({
            url: getUrl,
            type: "GET",
            beforeSend: function () { },
            error: function (request) { alert(request) },
            success: function (data) {
                if (pendingCall.timeStamp != timeStamp) { return false; }
                $('#contentdiv').html(data);
                pendingCall.procID = null;
            }
        });
    };

    if (pendingCall.procID) {
        clearTimeout(pendingCall.procID)
    };
	//set the time before call 3000 = 3 seconds
    pendingCall = { timeStamp: timeStamp, procID: setTimeout(printCall, 3000) };
});
```

## 设置超时选项

这是一个在网站加载一秒钟后，从你的页面中移除加载掩码的代码片段。你可以设置一个加载蒙版来覆盖整个网站，防止人们看到加载的图片。

```
jQuery(document).ready(function () {
	setTimeout( "jQuery('#loading_mask').hide();", 1000 );
});
```

## 分享这篇文章