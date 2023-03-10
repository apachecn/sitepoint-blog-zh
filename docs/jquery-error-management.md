# jQuery 错误管理示例

> 原文：<https://www.sitepoint.com/jquery-error-management/>

![error-management2-basics](img/f3ff37a2663401305b792e6d697f2b5a.png "error-management2-basics")

好了，我假设你们都知道 Firebug 和 FireQuery。如果你不知道，这些帖子可能会帮助你。Firebug 有许多强大的工具，可以让以前的 JavaScript 调试噩梦变得可以忍受。

*   [如何设置 Firebug](http://www.jquery4u.com/utilities/live-jquery-debugging-firebug/)
*   [使用 FireQuery 更改谷歌标志](http://www.jquery4u.com/articles/firequery-change-google-logo/)

所以现在你想要更好地管理这些错误，不仅仅是为了让你的用户看不到任何错误，也是为了在你开发你的脚本时有所帮助。

## 警报()

![javascript-error-alert](img/3ab821f8b7dc454da447b9f5903ba51a.png "javascript-error-alert")

```
//alert() shows values in a popup window
alert("js is working");
```

Alert 可以用来查看您的代码是否真正被执行，因为如果您的 JavaScript 中有严重的语法错误，它根本不会执行。也可用于查看是否到达了某个代码块或代码段。

## Console.log()

![javascript-error-console-log](img/dd9271c793e14543211f7bd5f3fee333.png "javascript-error-console-log")

```
//console.log() shows values in the firebug console window
var x = ... etc
console.log(x);
```

Console.log()对于显示循环中执行的值和捕获事件非常有用。稍后会有更多关于这个的报道。日志记录的完整选项可以在 [Firebug 控制台 API wiki 页面](http://getfirebug.com/wiki/index.php/Console_API)中看到。

重要提示:确保你的[包含了你的 firebug 命令](http://www.jquery4u.com/error-management/jquery-code-runs-firebug-open/)，否则你的 jQuery 代码只有在控制台打开时才能运行。

## 尝试/抓住

```
//try catch example 1
try {
	$("#user").focus();
} catch(err){
	return false;
}

//try catch example 2
try {
var tmp = doSomething();
if (tmp == something.errorCondition)
throw new Error("Error condition in X");
} catch(err) {
//handle ((err && err.message) || err.toString())
} 

//try catch example 3
try {
  // code that may cause an error
} catch (e) {
  // deal with error (or not)
}
// code that runs whether or not error occurred
```

## 覆盖 Firebug 中显示的错误

您还可以覆盖 jQuery.error，以便在 Firebug 中显示，如下所示:

```
jQuery.error = console.error;
```

## jQuery 停止错误显示

如果使用 jQuery 事件处理程序，可以结合使用 window.onerror 和包装 jQuery 事件处理程序代码，以及带有错误处理函数的 on ready 函数。

*   window.onerror:捕捉 IE 中的所有错误(以及 Firefox 中的大多数错误)，但在 Safari 和 Opera 中不做任何事情。
*   jQuery 事件处理程序:捕获所有浏览器中的 jQuery 事件错误。
*   jQuery ready 函数:捕捉所有浏览器中的初始化错误。

## jQuery 错误的类型(常见错误)

一个常见的错误是 AJAX 不返回数据。这可以通过添加错误消息来处理，参见下面的 AJAX 联系人表单示例。

```
$("#createContact").click(function () { //Attach a click event handler to the button
    var form = $("form"); //Grab the form element from the DOM

    $.ajax({
        type: "POST", //The type of HTTP verb (post, get, etc)
        url: form.attr( "action" ), //The URL from the form element where the AJAX request will be sent
        data: form.serialize(), //All the data from the form serialized
        dataType: "json", //The type of response to expect from the server
        success: function ( data, statusCode, xhr ) { //Triggered after a successful response from server
            if ( data && data.Message ) {
            	alert( data.Message );
            }
        },
        error: function ( xhr, errorType, exception ) { //Triggered if an error communicating with server
            var errorMessage = exception || xhr.statusText; //If exception null, then default to xhr.statusText

            alert( "There was an error creating your contact: " + errorMessage );
        }
    });

    return false; //Ignore the default behavior of the button click
});
[code lang="php"]
```

在 firebug 中，有一个 StatusText 字段，可用于确定 jQuery 错误的类型。

![firebug-error-management](img/4b73f88179a90104b0da602ff64e13b7.png "firebug-error-management")

## 有用的 AJAX 捕捉错误函数

```
function ajaxError(request, type, errorThrown)
{
	var message = "There was an error with the AJAX request.n";
	switch (type) {
		case 'timeout':
			message += "The request timed out.";
			break;
		case 'notmodified':
			message += "The request was not modified but was not retrieved from the cache.";
			break;
		case 'parseerror':
			message += "XML/Json format is bad.";
			break;
		default:
			message += "HTTP Error (" + request.status + " " + request.statusText + ").";
	}
	message += "n";
	alert(message);
}
```

**延伸阅读:**

*   [http://www . benna del . com/blog/1392-Handling-AJAX-Errors-With-jquery . htm](http://www.bennadel.com/blog/1392-Handling-AJAX-Errors-With-jQuery.htm)

## 分享这篇文章