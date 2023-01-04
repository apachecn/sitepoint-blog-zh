# jQuery 1.9+。Ajax()新的承诺接口优势

> 原文：<https://www.sitepoint.com/jquery-1-9-ajax-promise-interface-advantages/>

继 [5 新 JQUERY 之后。AJAX() EXAMPLES JQUERY 1.9+](http://www.jquery4u.com/ajax/5-jquery-ajax-examples/) ，我想记录使用 promise 接口进行 JQUERY 的变化(有些可以归类为优点)。Ajax() 1.9+。

*   **命名**——显然名字已经从成功- >完成，错误- >失败，完成- >一直变了。
*   **延期**–延期承诺可以绑定到应用程序中的任何地方，具有灵活性和可重用性。
*   **回调的顺序**–它们按这个顺序被调用:1 失败，2 完成，3 总是。标准。
*   **multiples**–您可以指定相同类型的任意数量的回调。即。总是()，。总是()，。一旦 ajax 请求返回完成，always()将被触发。
*   **自变量**
*   从 jQuery 1.8 开始，您可以使用。then()函数。例如，见下文。
*   **结合**——可以结合。done()和。错误()进入。然后()。例如，见下文。

```
//old complete function
complete Function( jqXHR jqXHR, String textStatus )

//new done function
jqXHR.done(function(data, textStatus, jqXHR) {});
```

来源: [jQuery。Ajax API](http://api.jquery.com/jQuery.ajax/)

如果你发现我错过了什么，请随时留下评论。

### 结合。done()和。使()失败。然后()

您可以将 done()和 fail()函数组合在一个 then()函数中。上面的代码可以重写为:

```
var promise = $.ajax({
  url: "/myServerScript"});

promise.then(mySuccessFunction, myErrorFunction);
```

来源:[jQuery](http://www.bitstorm.org/weblog/2012-1/Deferred_and_promise_in_jQuery.html)中的延期和承诺

## 链接 Ajax 请求

从 jQuery 1.8 开始，可以按顺序链接 then()函数。在下面的代码中，首先运行 promise1，当解析成功时，运行 getStuff，返回一个 promise，当解析成功时，执行匿名函数。

```
var promise1 = $.ajax("/myServerScript1");

function getStuff() {
    return $.ajax("/myServerScript2");}

promise1.then(getStuff).then(function(myServerScript2Data){
  // Both promises are resolved});
Every callback function receives the result of the previous asynchronous function, in the case of Ajax, that would be the returned data.
```

### 使用。当()作为承诺

你可以用。when()即分配一个承诺回调函数。完成()。

```
var container = $("#mycontainer");
$.when(
    function () {
        return $.Deferred(function (dfd) {
            container.fadeOut('slow', dfd.resolve);
        }).promise();
    }(),
    $.ajax({
        url: 'Path/To/My/Url/1',
        type: 'POST',
        dataType: 'json'
    }),
    $.ajax({
        url: 'Path/To/My/Url/2',
        type: 'POST',
        dataType: 'json'
    })
).done(function (x, data) {
    container.html('Your request has been processed!');
    container.fadeIn('slow');
});
```

正如你所看到的，我们通过了三个承诺。一个用于淡出动画，两个用于 ajax 操作。

*第一个参数是一个自执行匿名函数，它创建延迟处理程序并返回承诺。以美元计算。Deferred 的回调 dfd.resolve 函数被传递给 fadeOut()的回调参数，这意味着一旦动画完成，deferred 将被解析。
*关于我们传递给$的其他两个参数。当，自结果$起。ajax 是一个 jqXHR 对象，它实现了我们只传递原样返回的值的承诺。

来源:[永远信守你的(jQuery)承诺](http://www.ryanmwright.com/2011/09/06/always-keep-your-jquery-promises/)

## 分享这篇文章