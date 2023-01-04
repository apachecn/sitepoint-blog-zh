# 通过预处理消除异步 Javascript 回调

> 原文：<https://www.sitepoint.com/eliminating-async-javascript-callbacks-by-preprocessing/>

AJAX 的第二十二条军规是，为了方便起见，大多数时候我们想写“同步代码”，但是异步是避免一些相当令人讨厌的可用性问题的唯一方法。这意味着我们不能像我们希望的那样编写简单的代码，比如；

```
 function doClick() {
    var xmlhttp = new XMLHttpRequest();
    xmlhttp.open("GET","http://example.com/products",false);

    # Execution blocks here, waiting for the response to complete...
    xmlhttp.send(null);
    alert(xmlhttp.responseText);
} 

```

…我们需要通过回调来处理这个问题，最简单的例子是…

```
 var xmlhttp = new XMLHttpRequest();

function doClick() {
    xmlhttp.open("GET","http://example.com/products",true);

    // Set the callback
    xmlhttp.onreadystatechange = handleResponse;
    xmlhttp.send(null);
}

function handleResponse() {
    if ( xmlhttp.readyState == 4 ) {
        alert (xmlhttp.responseText);
    }
} 

```

…但现在这又带来了一大堆潜在的问题。回调现在依赖于可用的全局 xmlhttp 对象(任何大型项目的全局对象通常都是邪恶的)。如果用户一直触发那个`doClick()`函数会怎么样？对于停下来喝咖啡，过了很久才意外返回(需要超时)的异步请求怎么办？这只是开始。

无论如何——两个有趣的项目正致力于给我们两个世界最好的东西——异步请求但是(看起来)阻塞代码。两者都是在扩展 Javascript 本身的基础上工作的，因此以前手工编码的巨大努力被巧妙地隐藏在新的 Javascript 操作符或关键字之后。

## 叙事 Javascript

第一个是[叙述 Javascript](http://neilmix.com/narrativejs/doc/index.html) ，它添加了一个新的“阻塞操作符”`->`，这样你的代码就变成了类似于:

```
 function doClick() {
    # Note the blocking operator...
    var response = doHttpRequest->("http://example.com/products");
    alert(response);
} 

```

叙述性的 JS [概述](http://neilmix.com/narrativejs/doc/overview.html)是一个很好的起点。也许叙事 JS 的一个优势是它是纯 Javascript——尽管[文档](http://neilmix.com/narrativejs/doc/compile.html)建议使用类似 [Rhino](http://www.mozilla.org/rhino/) 的东西进行离线预处理，但你可能(理论上)可以在浏览器中按需预处理你的代码(潜在的显著性能成本)，Javascript 解析器是 [narcissus](http://lxr.mozilla.org/mozilla/source/js/narcissus/) (也是纯 JS)。至少，全是 JS 可能会让人们更有信心使用它。

## jacs

第二个是[jwacs](http://chumsley.org/jwacs/)——支持高级[延续](http://en.wikipedia.org/wiki/Continuation)的 Javascript。这实际上比模拟阻塞代码更进一步，增加了四个[新关键字](http://chumsley.org/jwacs/documentation.html)(和一个导入语句)。前面的例子(借助捆绑的 jwacs 实用程序 API)变成了；

```
 function doClick() {
    var response = JwacsLib.fetchData("GET", "http://example.com/products"))
    alert(response);
} 

```

要看扩展的 Javascript，需要看上面的`fetchData`定义；

```
 fetchData: function(method, url)
  {
    var http = JwacsLib.getHttpObj();
    var k = function_continuation;

    http.onreadystatechange = function()
    {
      try
      {
        // Report results to the continuation on completion
        if(http.readyState == 4)
        {
          // Check for errors
          if(!(http.status == undefined ||
               http.status == 0 ||
               (http.status >= 200 && http.status < 300)))
          {
            var err = new Error("Server returned " + http.status);
            throw err;
          }

          // No errors, so resume the continuation with the raw results
          http.onreadystatechange = JwacsLib.emptyFunction;
          resume k <- http.responseText;
        }
      }
      catch(e)
      {
        // Errors are thrown as exceptions into the continuation
        http.onreadystatechange = null;
        throw e -> k;
      }
    };

    http.open(method, url);
    http.send(null);
    suspend;
  } 

```

注意上面的`function_continuation`、`suspend`、`resume`和延伸投掷:`throw e -> k;`。jwacs 预处理器是用 [LISP](http://en.wikipedia.org/wiki/Lisp_programming_language) 编写的…

那么这里的普遍感觉是什么？你会考虑用这些吗？

对许多人来说，用新语法扩展 Javascript 的想法可能是令人不快的。您还引入了一些重要的依赖性——计划的后续更改可能会导致大量的重写(当然，它们仍然是原型)。

与此同时，用 Javascript 编写任何涉及异步处理和回调的重要内容很快就会变成一场噩梦——为什么不用一些智能语法来消除人工劳动呢？这里的一般方法对我来说似乎不错。

## 网络星期二

当我在这里的时候——今晚 [webtuesday 的一个快速广告，由](http://webtuesday.ch/meetings/20060912)Patrice 讲述他用 [Selenium](http://en.wikipedia.org/wiki/Selenium_%28software%29) 进行 web 测试的经历(你知道，不仅仅是“[更多 Java](https://www.sitepoint.com/website-testing-with-testgen4web-and-firefox/)”；)在[tillate 的](http://tilllate.com/) [总部](http://webtuesday.ch/locations/tilllate)。

## 分享这篇文章