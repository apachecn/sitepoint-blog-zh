# 带有 jQuery 的简单 Ajax 文章

> 原文：<https://www.sitepoint.com/ajax-jquery-3/>

***客户端代码(jQuery)***

 *现在，转到 jQuery 前端。首先，我们需要将当前消息的时间戳声明为 0，并调用从服务器加载消息的函数:

```
timestamp = 0;   
updateMsg();
```

接下来，我们将编写表单提交的代码。jQuery 允许我们为表单的 submit 事件添加一个事件挂钩，就像我们在 HTML 本身中添加一个`onSubmit`事件一样，只是我们不需要接触 HTML。下面是提交事件:

```
$("form#chatform").submit(function(){ /* Code */ });
```

这里我们使用 CSS 选择器语法来引用带有`'chatform'`的`id`的表单元素。一旦进入表单提交代码，我们就可以使用 jQuery 的`$.post`向服务器发出 POST 请求。在对`$.post`的调用中，我们可以根据它们的`id`来选择页面上表单元素的值，就像我们之前确定的那样。考虑到这一点，我们可以开始 Ajax 调用了:

```
$.post("backend.php",{ message: $("#msg").val(),   
name: $("#author").val(), action: "postmsg", time: timestamp }, function(xml) {
```

注意，请求中要传递的参数数组用花括号括起来。如果您有更多的参数，只需用逗号分隔它们，并使用这里给出的格式， [JSON-style](http://www.json.org/) 。您还可以使用 jQuery 的 Ajax 函数发送一个期望 JSON 样式响应的 GET 请求，并让 jQuery 将响应文本转换成易于使用的格式。但是要记住，这个功能只适用于 GET 请求类型，不适用于我们聊天系统中使用的 POST 请求类型。因此，目前我们将坚持使用纯 XML。

现在，让我们看看如何处理 XML 响应。因为我们都支持代码重用，所以现在我们将创建一个处理 XML 的函数，并将其命名为:

```
addMessages(xml);
```

我们稍后将编写这个函数，这样我们就可以完成表单提交事件代码。到目前为止，我们编写的代码是我们的`$.post`回调函数所需要的，所以我们可以关闭它并添加一个返回 false 线。使用标准浏览器表单提交错误代码时，此行失败。浏览器不会将用户发送到另一个页面来提交表单——我们已经处理了表单提交，所以浏览器不需要这样做。以下是完整的事件代码:

```
$("form#chatform").submit(function(){   
  $.post("backend.php",{   
        message: $("#msg").val(),   
        name: $("#author").val(),   
        action: "postmsg",   
        time: timestamp   
      }, function(xml) {   
    addMessages(xml);   
  });   
  return false;   
});
```

现在，让我们回到处理响应 xml 的`addMessages()`函数。这很简单，利用 jQuery 的 DOM 操作和遍历功能。还记得我之前提到的状态码吗？现在是处理的时候了:

```
if($("status",xml).text() == "2") return;
```

我还没有提到 jQuery 中的上下文。这个函数调用中的 XML 告诉 jQuery 不要查看文档 HTML 内部，而是查看服务器发送给我们的 XML。

这一行代码检查状态代码 2，表示请求成功，导致我们没有新消息添加到窗口。关键字`'return'`终止函数调用。接下来，我们将时间戳设置为 XML 中的时间戳:

```
timestamp = $("time",xml).text();
```

同样，这会获取 XML 中的`<time>`标签的文本值。

现在我们可以转到 jQuery 的数组迭代函数`each()`。jQuery 处理数组迭代的方式很有趣。我们使用一个标准的选择器语句，并且向`each()`函数传递一个参数——一个处理匹配元素的每个实例的函数。在这种情况下，元素是服务器响应中的`<message>`标记的实例，每个实例代表一条要显示的消息。一个参数——实例的`id`—被传递给函数。我们可以利用 jQuery 的`get()`函数来获取一个新的上下文，即`<message>`标签的实际 XML。我们是这样选择的:

```
 $("message",xml).each(function(id) {   
  message = $("message",xml).get(id);
```

然后，我们可以通过将上下文`'message'`传递给 jQuery `/ $`函数来选择元素。现在我们已经有了所有需要的数据，我们必须将它添加到页面上消息窗口的顶部。消息窗口的 id 是`'messagewindow'`，所以我们使用`$("#messagewindow")`选择它，并使用`prepend()`功能添加我们的数据:

```
$("#messagewindow").prepend("<b>"+$("author",message).text()+   
          "</b>: "+$("text",message).text()+   
          "<br />");
```

这就是全部了！综上所述，下面是该函数的代码:

```
function addMessages(xml) {   
  if($("status",xml).text() == "2") return;   
  timestamp = $("time",xml).text();   
  $("message",xml).each(function(id) {   
    message = $("message",xml).get(id);   
    $("#messagewindow").prepend("<b>"+$("author",message).text()+   
              "</b>: "+$("text",message).text()+   
              "<br />");   
  });   
}
```

最后，我们需要在代码开始时调用的`updateMsg`函数。这个函数必须向服务器查询新消息，并使用响应调用上面的`addMessages`函数。它还必须设置一个超时，以便在一段时间后调用自己，这使得聊天窗口自动更新。首先，除了时间戳之外，我们不需要向服务器提交任何东西，所以这是我们的`$.post`调用:

```
$.post("backend.php",{ time: timestamp }, function(xml) {
```

正如我前面提到的，此时我们还需要删除加载消息，因此我们在 span:

```
$("#loading").remove();
```

然后，我们在对象`'xml'`中收到了 xml 响应，所以我们将它传递给我们的`addMessages`函数:

```
addMessages(xml);
```

我们通过调用 JavaScript `setTimeout()`函数来完成它，该函数在指定的时间间隔后执行指定的代码。下面是整个函数的组合:

```
function updateMsg() {   
  $.post("backend.php",{ time: timestamp }, function(xml) {   
    $("#loading").remove();   
    addMessages(xml);   
  });   
  setTimeout('updateMsg()', 4000);   
}
```

 *现在我们可以把所有的拼图拼在一起了。正如我提到的，代码可以在这个可下载的 zip 文件中找到。不过，你可以在这里仔细阅读，我在这里添加了一些 HTML 和 CSS 的布局:

```
<html>   
<head>   
  <title>Ajax with jQuery Example</title>   
  <script type="text/JavaScript" src="jquery.js"></script>   
  <script type="text/JavaScript">   
    $(document).ready(function(){   
      timestamp = 0;   
      updateMsg();   
      $("form#chatform").submit(function(){   
        $.post("backend.php",{   
              message: $("#msg").val(),   
              name: $("#author").val(),   
              action: "postmsg",   
              time: timestamp   
            }, function(xml) {   
          $("#msg").empty();   
          addMessages(xml);   
        });   
        return false;   
      });   
    });   
    function addMessages(xml) {   
      if($("status",xml).text() == "2") return;   
      timestamp = $("time",xml).text();   
      $("message",xml).each(function(id) {   
        message = $("message",xml).get(id);   
        $("#messagewindow").prepend("<b>"+$("author",message).text()+   
                      "</b>: "+$("text",message).text()+   
                      "<br />");   
      });   
    }   
    function updateMsg() {   
      $.post("backend.php",{ time: timestamp }, function(xml) {   
        $("#loading").remove();   
        addMessages(xml);   
      });   
      setTimeout('updateMsg()', 4000);   
    }   
  </script>   
  <style type="text/css">   
    #messagewindow {   
      height: 250px;   
      border: 1px solid;   
      padding: 5px;   
      overflow: auto;   
    }   
    #wrapper {   
      margin: auto;   
      width: 438px;   
    }   
  </style>   
</head>   
<body>   
  <div id="wrapper">   
  <p id="messagewindow"><span id="loading">Loading...</span></p>   
  <form id="chatform">   
  Name: <input type="text" id="author" />   
  Message: <input type="text" id="msg" />       
  <input type="submit" value="ok" /><br />   
  </form>   
  </div>   
</body>   
</html>
```

因此，用 22 行 JavaScript、8 行 HTML 和大约 50 行 PHP，我们现在有了一个全功能的 Ajax web 应用程序。尝试一下，并将其集成到您自己的站点中。使用这些技术和您自己的想法构建您自己的 Ajax 应用程序。取这段代码，修改它来构建新的东西。如果您不习惯于生成和处理 XML，请坚持使用您的 web 应用程序来生成 HTML，并使用`load()`将其提交给客户端。然后，一旦掌握了窍门，就可以尝试一个使用标签属性和 jQuery 的`attr()`函数充分利用 XML 的应用程序——您会惊讶于 Ajax 和 jQuery 的强大功能。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [jQuery Fundamentals](https://learnable.com/courses/jquery-fundamentals-1132) 。

**Go to page:** [1](/ajax-jquery) | [2](/ajax-jquery-2/) | [3](/ajax-jquery-3/)**

## **分享这篇文章**