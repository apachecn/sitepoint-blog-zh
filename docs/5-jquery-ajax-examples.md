# 5 新的 jQuery。Ajax()jQuery 1.9+示例

> 原文：<https://www.sitepoint.com/5-jquery-ajax-examples/>

进入 2013 年…以及我们使用 [jQuery 的方式。Ajax()](http://api.jquery.com/jQuery.ajax/) 函数在最近的版本中有所改变。考虑到这一点，旧的例子已经有点过时了，我编写了 **5 个新的 jQuery . ajax()Examples jQuery 1.9+**来展示如何在新版本的 jQuery 1.9.x 和 2.0 中使用 Ajax。

新方法比旧方法有一些优点。

我会试着用框架 Ajax 片段更新这篇文章，以供参考。一如既往，欢迎评论。

A quick reminder for those learning:

*   的。done()方法替换了不推荐使用的 jqXHR.success()方法。
*   的。fail()方法替换了不推荐使用的。error()方法。
*   的。always()方法替换了不推荐使用的。complete()方法。

## jQuery 1.9+ Ajax 示例 1–订阅时事通讯

这个例子展示了一个基本的 Ajax 请求，用于订阅时事通讯，向后端脚本发送姓名和电子邮件。

```
var subscribeRequest = $.ajax({
     type: "POST",
     url: "subscribe.php",
     data: { subscriberName: $('#name').val(), emailAddress: $('#email').val() }
});

subscribeRequest.done(function(msg)
{
     alert( "You have successfully subscribed to our mailing list." );
});

subscribeRequest.fail(function(jqXHR, textStatus)
{
     alert( "We could not subscribe you please try again or contact us if the problem persists (" + textStatus + ")." );
});
```

## jQuery 1.9+ Ajax 示例 2–请求超时

这个例子展示了如何捕捉错误和失败，比如 ajax 请求超时。

> 为请求设置超时时间(以毫秒为单位)。超时时间从$开始。进行了 ajax 调用；如果其他几个请求正在进行，而浏览器没有可用的连接，则请求可能会在发送之前超时。仅在 Firefox 3.0+中，脚本和 JSONP 请求不能被超时取消；即使脚本在超时期限后到达，它也会运行。

```
var newDataRequest = $.ajax({
     url: "getNewData.php",
     timeout: 30000, // timeout after 30 seconds
     data: { timestamp: new Date().getTime() }
});

newDataRequest.done(function(data)
{
     console.log(data);
});

newDataRequest.fail(function(jqXHR, textStatus)
{
    if (jqXHR.status === 0)
    {
        alert('Not connect.n Verify Network.');
    }
    else if (jqXHR.status == 404)
    {
        alert('Requested page not found. [404]');
    }
    else if (jqXHR.status == 500)
    {
        alert('Internal Server Error [500].');
    }
    else if (exception === 'parsererror')
    {
        alert('Requested JSON parse failed.');
    }
    else if (exception === 'timeout')
    {
        alert('Time out error.');
    }
    else if (exception === 'abort')
    {
        alert('Ajax request aborted.');
    }
    else
    {
        alert('Uncaught Error.n' + jqXHR.responseText);
    }
});
```

## jQuery 1.9+ Ajax 示例 3–数据过滤器

这个例子展示了如何使用 dataFilter 函数处理 Ajax 请求返回的原始数据。

```
var filterDataRequest = $.ajax({
     url: "getData.php",
     dataFilter: function (data, type)
     {
          //include any conditions to filter data here...
          //some examples below...

          //eg1 - remove all commas from returned data
          return data.replace(",", "");

          //eg2 - if data is json process it in some way
          if (type === 'json')
          {
              var parsed_data = JSON.parse(data);
              $.each(parsed_data, function(i, item)
              {
                  //process the json data
              });
              return JSON.stringify(parsed_data);
          }

     }
});

filterDataRequest.done(function(data)
{
     console.log(data);
});

filterDataRequest.fail(function(jqXHR, textStatus)
{
     console.log( "Ajax request failed... (" + textStatus + ' - ' + jqXHR.responseText ")." );
});
```

## jQuery 1.9+ Ajax 示例 4–MIME 类型

这个例子展示了如何指定 XMLHttpRequest 上可用的[内容响应头类型](http://webdesign.about.com/od/multimedia/a/mime-types-by-file-extension.htm)。

If you explicitly pass in a content-type to $.ajax(), then it’ll always be sent to the server (even if no data is sent). If no charset is specified, data will be transmitted to the server using the server’s default charset; you must decode this appropriately on the server side.

```
var contentTypeRequest = $.ajax({
     url: "getData.php",
     contentType: 'application/x-www-form-urlencoded; charset=UTF-8' //default

     //or choose from one below
     contentType: 'application/atom+xml' //Atom
     contentType: 'text/css' //CSS
     contentType: 'text/javascript' //JavaScript
     contentType: 'image/jpeg' //JPEG Image
     contentType: 'application/json' //JSON
     contentType: 'application/pdf' //PDF
     contentType: 'application/rss+xml; charset=ISO-8859-1' //RSS
     contentType: 'text/plain' //Text (Plain)
     contentType: 'text/xml' //XML
});

contentTypeRequest.done(function(data)
{
     console.log(data);
});

contentTypeRequest.fail(function(jqXHR, textStatus)
{
     console.log( "Ajax request failed... (" + textStatus + ' - ' + jqXHR.responseText ")." );
});

//set specific accept headers (should work cross browser)
$.ajax({
    headers: {
        Accept : "text/plain; charset=utf-8",
        "Content-Type": "text/plain; charset=utf-8"
    }
})

//an alternative to the above
$.ajax({
    beforeSend: function(xhrObj)
    {
        xhrObj.setRequestHeader("Content-Type","application/json");
        xhrObj.setRequestHeader("Accept","application/json");
    }
});
```

## jQuery 1.9+ Ajax 示例 5——解析 XML

我从 jQuery 文档[中抓取的这个例子指定了 ajax 请求](http://docs.jquery.com/Specifying_the_Data_Type_for_AJAX_Requests)的数据类型。它显示了加载从脚本返回的 XML 并将数据解析为 XML(如果 Internet Explorer 以纯文本而不是 text/xml 的形式接收)。

By specify the $.ajax dataType option to be “xml”, make sure your server sends content with the “text/xml” MIME type. Sending the wrong MIME type will prohibit jQuery from correctly managing the data returned in the response, and could cause unexpected problems in your script.

```
var getXMLRequest = $.ajax({
     url: "data.xml.php",
     contentType: "text/xml"
});

getXMLRequest.done(function(data)
{
    console.log(data);
    var xml;
    if (typeof data == "string")
    {
        xml = new ActiveXObject("Microsoft.XMLDOM");
        xml.async = false;
        xml.loadXML(data);
    }
    else
    {
        xml = data;
    }
    // Returned data available in object "xml"
});

getXMLRequest.fail(function(jqXHR, textStatus)
{
     console.log( "Ajax request failed... (" + textStatus + ' - ' + jqXHR.responseText ")." );
});
```

## 分享这篇文章