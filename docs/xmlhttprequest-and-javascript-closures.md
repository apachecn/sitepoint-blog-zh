# XMLHttpRequest 和 Javascript 闭包

> 原文：<https://www.sitepoint.com/xmlhttprequest-and-javascript-closures/>

根据 Simon 关于[闭包和在页面加载时执行 JavaScript 的精彩提示](https://www.sitepoint.com/blog/)收到了来自 [Scott Raymond](http://scottraymond.net/) 的邮件，内容是关于如何将这一点应用到 [XMLHttpRequest](http://www.xulplanet.com/references/objref/XMLHttpRequest.html) 中，所以我想分享一些我使用 [ScriptServer](https://www.sitepoint.com/blog/) 的经验。

XMLHttpRequest 的 Mozilla 实现提供了“onload”和“onerror”属性，您可以将自己的回调分配给这两个属性，回调会自动传递给一个事件对象，通过该事件对象您可以返回到您的调用对象——这一点可以通过查看[moz blog](http://mozblog.mozdev.org/)[nsxmlrpclient . js](http://mozblog.mozdev.org/nsXmlRpcClient.js)来更好地了解——查看它们如何处理 _onload 函数。

不幸的是，微软的 ActiveX 实现不支持，它要求你使用一个名为 onreadystatechange 的属性，幸好 Mozilla 也支持这个属性(旁注——Mozilla 的 XMLHttpRequest 是不是第一次有人把微软最喜欢的“拥抱和扩展”用到微软身上？).问题是 get 不会自动传递给 onreadystatechange 的回调函数，但这正是闭包派上用场的地方。

剩下的我将留给一段很长的代码

```

 <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head>
<script type="text/javascript">
<!--
// Mozilla only implementation!

// Constructor for generic HTTP client
function HTTPClient() {};

// Add methods and properties as array
HTTPClient.prototype = {
    url: null,

    // Instance of XMLHttpRequest
    xmlhttp: null,

    // Used to make sure multiple calls are not placed
    // with the same client object while another in progress
    callinprogress: false,

    // The user defined handler - see MyHandler below
    userhandler: null,

    init: function(url) {
        this.url = url;
        this.xmlhttp = new XMLHttpRequest();
    },

    // handler argument is a user defined object to be called
    asyncGET: function (handler) {

        // Prevent multiple calls
        if (this.callinprogress) {
            throw "Call in progress";
        };

        this.userhandler = handler;

        // Open an async request - third argument makes it async
        this.xmlhttp.open('GET',this.url,true);

        // Have to assign "this" to a variable - not sure why can't use directly
        var self = this;

        // Assign a closure to the onreadystatechange callback
        this.xmlhttp.onreadystatechange = function() {
            self.stateChangeCallback(self);
        }

        // Send the request
        this.xmlhttp.send(null);
    },

    stateChangeCallback: function(client) {
        switch (client.xmlhttp.readyState) {

            // Request not yet made
            case 1:
                try {
                    client.userhandler.onInit();
                } catch (e) { /* Handler method not defined */ }
            break;

            // Contact established with server but nothing downloaded yet
            case 2:
                try {
                    // Check for HTTP status 200
                    if ( client.xmlhttp.status != 200 ) {
                        client.userhandler.onError(
                            client.xmlhttp.status,
                            client.xmlhttp.statusText
                            );

                        // Abort the request
                        client.xmlhttp.abort();

                        // Call no longer in progress
                        client.callinprogress = false;
                    }
                } catch (e) {
                    /* Handler method not defined */
                }
            break;

            // Called multiple while downloading in progress
            case 3:
                // Notify user handler of download progress
                try {
                    // Get the total content length
                    // -useful to work out how much has been downloaded
                    try {
                        var contentLength = 
                            client.xmlhttp.getResponseHeader("Content-Length");
                    } catch (e) {
                        var contentLength = NaN;
                    } 

                    // Call the progress handler with what we've got
                    client.userhandler.onProgress(
                        client.xmlhttp.responseText,
                        contentLength
                    );

                } catch (e) { /* Handler method not defined */ }
            break;

            // Download complete
            case 4:
                try {
                    client.userhandler.onLoad(client.xmlhttp.responseText);
                } catch (e) {
                    /* Handler method not defined */
                } finally {
                    // Call no longer in progress
                    client.xmlhttp.callinprogress = false;
                }
            break;
        }
    }
}

// A user defined handler to response to the XMLHTTPRequest
var MyHandler = {
    onInit: function() {
        echo("About to send request<br>");
    },
    onError: function(status,statusText) {
        echo("Error: "+status+": "+statusText+"<br>");
    },
    onProgress: function(responseText,length) {
        echo("Downloaded "+responseText.length+" of "+length+"<br>");
    },
    onLoad: function(result) {
        echo("<pre>"+result+"</pre>");
    },
}

// Just a function to help display results
function echo(string) {
    document.getElementById("results").innerHTML += string;
}

// Invoke the client
function getPage() {
    // Modify this to some page
    var url = "http://localhost/test/test.txt";
    var client = new HTTPClient();
    client.init(url);

    try {
        client.asyncGET(MyHandler);
    } catch (e) {
        alert(e);
    }
    echo("Async request so still able to do stuff here<br>");
}
</script>
</head>
<body>
<a href="javascript:getPage();">getPage</a>
<div id="results">
</div>
</body>
</html>

注意，我在这里也使用了异步请求——我听到有人抱怨 XMLHttpRequest 太慢，锁死了浏览器——异步请求可以帮助你避免这种情况。

请注意，这也是 Mozilla 目前特有的。自从 ActiveX XMLHttpRequest 的 3.x 版本以来，由于某种我不明白的原因，他们似乎已经停止填充像“status”这样的属性，直到我们点击 ready state 4——这意味着在它完全完成之前，您不能响应错误或获得关于请求的信息。对于类似的跨浏览器实现，请参见这里的(尽管还没有实现闭包，因此使用了大量的全局变量)——例如这里的。 
```

## 分享这篇文章