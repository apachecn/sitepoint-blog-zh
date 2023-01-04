# jQuery Ajax 错误处理函数

> 原文：<https://www.sitepoint.com/jquery-ajax-error-handling-function/>

***jQuery Ajax 错误处理函数***

```
$( document ).ajaxError(function( event, request, settings ) {
  $( "#msg" ).append( "*   请求页面“+ settings.url +”时出错" );
});
```

官方的。ajaxError() API

注:如果$的话。ajax()或$。调用 ajaxSetup()时，全局选项设置为 false。ajaxError()方法不会触发。

下面是一个旧的函数，你可以从 jqXHR 对象中得到它的状态。

```
$(function() {
    $.ajaxSetup({
        error: function(jqXHR, exception) {
            if (jqXHR.status === 0) {
                alert('Not connect.n Verify Network.');
            } else if (jqXHR.status == 404) {
                alert('Requested page not found. [404]');
            } else if (jqXHR.status == 500) {
                alert('Internal Server Error [500].');
            } else if (exception === 'parsererror') {
                alert('Requested JSON parse failed.');
            } else if (exception === 'timeout') {
                alert('Time out error.');
            } else if (exception === 'abort') {
                alert('Ajax request aborted.');
            } else {
                alert('Uncaught Error.n' + jqXHR.responseText);
            }
        }
    });
});
```

## 分享这篇文章