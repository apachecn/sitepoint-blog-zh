# 使用 Ajax 执行命令

> 原文：<https://www.sitepoint.com/take-command-ajax/>

你想开发像 Gmail 和 Google Maps 这样更动态、更灵敏、更像桌面的网络应用吗？那这篇文章就送给你了！它指导您学习 Ajax 基础知识，并完成构建简单 Ajax 应用程序的过程。

该应用程序被命名为 WebConsole，这是一个用于执行系统命令的浏览器界面，您通常需要通过 shell 访问这些命令。还有一些使用两个流行的 JavaScript 库——jQuery 和 YUI 的 Ajax 功能的简短例子。

在这篇 2005 年首次发布并于最近更新的文章中，我将解释如何创建一个简单的、可重用的 JavaScript 函数来发出 HTTP 请求。然后，我将在创建一个简单的应用程序时应用该函数。

尽管有一些 YUI 和 jQuery 的例子，但本文并不是关于特定 Ajax 库的教程。相反，它旨在为您提供更多关于发出 HTTP 请求的实际信息，以便您在评估这类库或决定自己开发时处于更有利的位置。

##### 一个简单的 HTTP 请求示例

让我们首先修改用 JavaScript 发出 HTTP 请求并处理响应的流程。这只是一个快速的例子来提醒你。关于所有有趣的细节，请参阅 SitePoint 的介绍性文章[“Ajax:与远程脚本的可用交互性](https://www.sitepoint.com/article/remote-scripting-ajax)”

有三个基本步骤:

2.  创建一个`XMLHttpRequest`对象。

4.  分配一个回调函数来处理 HTTP 响应。

6.  提出(发出)请求。

让我们看一个例子，我们将请求一个简单的 HTML 文档，`test.html`，它只包含文本“我是一个测试。”我们接着将`alert()``test.html`文件的内容:

```
<button id="mybutton">Make a request</button> 

<script type="text/javascript"> 

var http_request = false; 

function makeRequest(url) { 

   if (window.XMLHttpRequest) { // Mozilla, Safari, IE7... 

       http_request = new XMLHttpRequest(); 

   } else if (window.ActiveXObject) { // IE6 and older 

       http_request = new ActiveXObject("Microsoft.XMLHTTP"); 

   } 

   http_request.onreadystatechange = alertContents; 

   http_request.open('GET', url, true); 

   http_request.send(null); 

} 

function alertContents() { 

   if (http_request.readyState == 4) { 

       if (http_request.status == 200) { 

           alert(http_request.responseText); 

       } else { 

           alert('There was a problem with the request.'); 

       } 

   } 

} 

document.getElementById('mybutton').onclick = function() { 

   makeRequest('test.html'); 

} 

</script>
```

这个例子是这样工作的:

2.  用户点击“提出请求”按钮。

4.  这将调用带有参数的`makeRequest()`函数:同一目录中 HTML 文件的名称。这种情况下是`test.html`。

6.  请求已发送。

8.  触发`onreadystatechange`事件，执行被传递给`alertContents()`。

10.  `alertContents()`检查是否收到响应，如果没问题，那么`alert()`检查 `test.html`文件的内容。

[自己测试示例](http://www.w3clubs.com/sp/ajax2/httprequest_example.html)，[查看测试文件](http://www.w3clubs.com/sp/ajax2/test.html)。

##### 问题是

上面的例子工作得很好，但是在我们准备好迎接黄金时间之前，有一件事我们需要改进。改进是编写一个可重用的请求函数，它处理所有无聊和重复的对象创建和请求/响应，同时将表示部分留给其他函数，这些函数与请求无关，只处理结果，而不考虑其来源。

在上面的例子中，我们需要一个全局变量`http_request`，它可以被`makeRequest()`和`alertContents()` 函数访问，这在可重用性方面并不好，也有命名冲突的风险。理想情况下，`makeRequest()`应该执行请求，`alertContents()`应该只呈现结果；这两种功能都不需要了解或要求对方。

下面是我们的可重用请求函数的代码:

```
function makeHttpRequest(url, callback_function, return_xml) 

{ 

  var http_request, response, i; 

  var activex_ids = [ 

    'MSXML2.XMLHTTP.3.0', 

    'MSXML2.XMLHTTP', 

    'Microsoft.XMLHTTP' 

  ]; 

  if (window.XMLHttpRequest) { // Mozilla, Safari, IE7+... 

    http_request = new XMLHttpRequest(); 

    if (http_request.overrideMimeType) { 

      http_request.overrideMimeType('text/xml'); 

    } 

  } else if (window.ActiveXObject) { // IE6 and older 

    for (i = 0; i < activex_ids.length; i++) { 

      try { 

        http_request = new ActiveXObject(activex_ids[i]); 

      } catch (e) {} 

    } 

  } 

  if (!http_request) { 

    alert('Unfortunately your browser doesn't support this feature.'); 

    return false; 

  } 

  http_request.onreadystatechange = function() { 

    if (http_request.readyState !== 4) { 

        // not ready yet 

        return; 

    } 

    if (http_request.status !== 200) { 

      // ready, but not OK 

      alert('There was a problem with the request.(Code: ' + http_request.status + ')'); 

      return; 

    } 

    if (return_xml) { 

      response = http_request.responseXML; 

    } else { 

      response = http_request.responseText; 

    } 

    // invoke the callback 

    callback_function(response); 

  }; 

  http_request.open('GET', url, true); 

  http_request.send(null); 

}
```

该函数接收三个参数:

*   要获取的 URL
*   收到响应时要调用的函数
*   如果回调函数期望 XML 文档(`true`)或纯文本(`false`，默认)，则为一个标志

这个函数依靠两个 JavaScript 功能来很好地包装和隔离请求对象。首先是动态定义新函数的能力(称为**匿名函数**，就像这样:
 `http_request.onreadystatechange = function() {...}`

另一个技巧是在事先不知道名字的情况下调用回调函数的能力；例如:

```
var callmeback = alert;

callmeback('test'); // alerts 'test'
```

请注意回调函数的名称是如何不带任何引号传递的。

通过允许将 HTTP 请求方法和任何查询字符串作为参数传递给函数，然后在对`open()`和`send()`方法的调用中使用，可以很容易地使函数更加可重用。这还将允许您在最初打算执行的 get 之外发出 POST 请求。

该函数的另一个功能是处理除 200 之外的响应代码，如果您希望更加具体，并根据返回的成功/错误代码的类型采取适当的措施，这将非常方便。

##### 重温这个简单的例子

现在让我们重复前面的例子，其中一个`test.html`文件的内容被`alert()`编辑。这一次，通过使用我们闪亮的新的可重用请求函数，所使用的两个函数的修订版将更加简单:

```
function alertContents(text) {  

   alert(text);  

}  

function makeRequest(url) {  

   makeHttpRequest(url, alertContents);  

}
```

正如您所看到的，alertContents()只是表示性的:没有状态、readyStates 或 HTTP 请求到处乱飞。

由于这些函数现在只是一行程序，我们实际上可以完全去掉它们，改为改变函数调用。所以整个例子会变成:

```
<button id="mybutton">Make a request</button> 

<script type="text/javascript"> 

  document.getElementById('mybutton').onclick = function() { 

    makeHttpRequest('test.html', alert); 

  } 

</script>
```

是的，就是这么简单！[查看示例和完整源代码](http://www.w3clubs.com/sp/ajax2/httprequest_test.html)(可通过我们的老朋友查看源代码获得)。

##### 我们的项目:web 控制台应用程序

了解了 Ajax 基础知识，并掌握了一种可重用的请求方式，让我们更深入地去创建一些实际上可以在现实生活中使用的东西。

我们将创建的应用程序将允许您在 web 服务器上执行任何 shell 命令，无论它是基于 Windows 还是基于 Linux 的。我们甚至会加入一点 CSS 努力，试图让应用程序感觉更像一个控制台窗口。

就界面而言，我们有一个可滚动的`<div>`包含了到目前为止执行的命令的结果，还有一个`<input>`用于输入要执行的命令。它们都有黑色的背景和灰色的 courier 字体。下面是截图。

![WebConsole interface screenshot](img/80df79a7abd5583af434129055fca42e.png)

***HTML***

下面是应用程序的 HTML 部分:

```
<form action="exec.php" method="get" id="console-form">  

  <div  

    class="console"  

    id="result">  

    Welcome to the WebConsole!  

    <br />  

    :-&gt;  

  </div>  

  <input  

    class="console"  

    name="command"  

    id="command"  

    type="text" />  

</form>
```

就是这样:一个`<div>`用执行命令的结果更新，一个`<input>`我们可以在其中输入命令。这是一个漂亮、干净的界面，没有`<iframe>`摔跤，没有页面重新加载——什么都没有！

***CSS***

样式表`webconsole.css`定义了结果`<div>`和命令`<input>`的样式:

```
.console {  

    margin: 0px;  

    font-family: courier;  

    color: gray;  

    background-color: black;  

}  

#result {  

    overflow: auto;  

    padding: 5px;  

    height: 400px;  

}  

#result pre {  

    display: inline;  

}  

#command {  

    width: 100%;  

    border: 1px solid white;  

}
```

我们通过将显示命令执行结果的`<div>`的`overflow`属性设置为`auto`来使其可滚动。我们还将`<pre>`标签显示属性更改为`inline` ( `block`是其默认值)。还有一个可重复使用的`.console`类，让一切看起来“舒适:”灰色等宽字体在黑色背景上。

***服务器端代码***

我们的应用程序将向服务器端脚本(`exec.php`)发出请求，该脚本通过 GET 参数`'command'`接收命令。这个脚本只是检查命令是否出现在允许列表中(您可以编辑这个列表以允许更多的命令)，执行命令并打印结果。该命令是在本地 PHP 函数 [`shell_exec()`](http://au2.php.net/shell_exec) 的帮助下执行的。这里使用的是 PHP，但是使用您喜欢的服务器端语言实现这个功能应该相对容易。

```
<?php   

if(strcmp(strtoupper(substr(PHP_OS, 0, 3)), "WIN") == 0) {   

   // Windows commands   

   $allowed_commands = array ('cd', 'dir', 'more webconsole.css', 'more test.html', 'copy test.html test.txt', 'more test.txt', 'del test.txt');   

} else {   

   // Linux, Mac OS X, etc. commands   

   $allowed_commands = array ('ls -la', 'ls', 'ls -l', 'less webconsole.css', 'less test.html', 'touch test.txt', 'cp test.html test.txt', 'less test.txt', 'rm test.txt');   

}   

if (!empty($_GET['command']) && in_array($_GET['command'], $allowed_commands)) {   

   echo shell_exec($_GET['command']);   

} else {   

   echo "This demo version lets you execute shell commands only from a predefined list:n";   

   echo implode("n", $allowed_commands);   

}   

?>
```

***警告！**
`$allowed_commands array`限制用户可以通过控制台执行的命令。您可以向数组中添加任意多的命令，但是要注意，任何额外的命令都将在您的 web 服务器上执行:例如，不建议添加`format c:: apachectl stop`或`rm â€“rf`！*

***JavaScript***

JavaScript 代码中的第一步是定义一个**名称空间**:一个本质上只不过是一个空对象的美化标签:

```
var WebConsole = {};
```

我们需要的所有其他变量和函数都将被定义为这个对象的属性。这允许我们保持全局名称空间的整洁和代码的自包含性。

应用程序中的 JavaScript 代码流程如下:

1.  `WebConsole.keyEvent()`函数附属于输入域的`onkeyup`事件，每次按下和释放一个键时调用。

3.  `WebConsole.keyEvent()`检查代码为 13 的键是否被按下(这是输入/返回键)。

5.  如果按下回车键，请求的 URL 的结构如下:`exec.php?command=the-command-entered-by-the-user`

7.  URL 被传递给我们可重用的`makeHttpRequest()`函数。同样，回调函数的名称——`WebConsole.printResult`——作为参数提供给`makeHttpRequest()`。

9.  在成功的服务器响应之后，调用`WebConsole.printResult()`。

11.  `WebConsole.printResult()`更新结果`<div>`，向下滚动`<div>`，清除命令文本框，为下一个要输入的命令腾出空间。

下面是`keyEvent()`函数体的样子:

```
WebConsole.keyEvent = function(event)   

{   

  switch(event.keyCode){   

    case 13:   

      var the_shell_command = document.getElementById('command').value;   

      if (the_shell_command) {   

        var the_url = 'exec.php?command=' + escape(the_shell_command);   

        makeHttpRequest(the_url, WebConsole.printResult);   

      }   

       break;   

     default:   

       break;   

   }   

}
```

因为我们没有将`true`作为第三个参数传递给`makeHttpRequest()`，所以文本响应(不是 XML)将被传递给`printResult()`。

接下来，我们来看看将更新结果的函数`<div>`。有一种快速更新`<div>`的方法，那就是使用元素的`innerHTML`属性，就像这样:

```
document.getElementById('result').innerHTML += 'the-result-goes-here';
```

但是不鼓励使用`innerHTML`来动态更新网页，因为它将 HTML 代码视为一个字符串，而现代 web 设计思维更喜欢将页面视为一个包含 XML 节点树的文档，可通过 DOM 方法和属性访问。为了更新我们的`<div>`，DOM 是我们现在要走的路径。

函数是这样的:下面是一些关于它如何工作的注释:

```
WebConsole.printResult = function(result_string)  

{  

  var result_div = document.getElementById('result');  

  var result_array = result_string.split('n');  

  var new_command = document.getElementById('command').value;  

  result_div.appendChild(document.createTextNode(new_command));  

  result_div.appendChild(document.createElement('br'));  

  var result_wrap, line_index, line;  

  for (line_index in result_array) {  

    result_wrap = document.createElement('pre');  

    line = document.createTextNode(result_array[line_index]);  

    result_wrap.appendChild(line);  

    result_div.appendChild(result_wrap);  

    result_div.appendChild(document.createElement('br'));  

  }  

  result_div.appendChild(document.createTextNode(':-> '));  

  result_div.scrollTop = result_div.scrollHeight;  

  document.getElementById('command').value = '';  

};
```

该功能:

*   通过创建一个新的文本节点并将其添加到文档树，将在`<input>`中输入的命令添加到结果`<div>`
*   显示命令执行的结果。这是通过将结果分成行并将每一行添加到文档树中来完成的，同时将这些行中的每一行包装在`<pre>`标签中以保留间距。我们需要分割结果，因为它可能包含几行(想象一下如果执行一个`'ls -la'`(或者 Windows 上的`'dir'`)的结果)
*   添加一个新的光标状文本节点(`:->`)
*   使用`scrollTop`和`scrollHeight`属性向下滚动`<div>`(非 W3C 标准，但现代浏览器支持)
*   清除命令`<input>`,以便输入下一个命令

JavaScript 中的最后一个任务是处理事件:

*   表单提交被简单地“静音”,因此没有页面刷新
*   方法`WebConsole.keyEvent()`被附加到输入的`keyup`事件，在此输入命令。

```
document.getElementById('console-form').onsubmit = function(){  

    return false;  

};  

document.getElementById('command').onkeyup = function(e){  

    if (!e && window.event) {  

        e = window.event;  

    }  

    WebConsole.keyEvent(e);  

};
```

这就对了。这是拼图的最后一块。我们现在拥有的是一个从零开始构建的工作 Ajax 应用程序。

##### 多一点

如果你有足够的好奇心去看前一个例子的源代码，你可能已经注意到这个应用程序比我们到目前为止讨论的要多一点。这一点额外的东西并不真正与 Ajax 相关，但它让应用程序感觉更像一个命令提示符。所讨论的功能包括使用向上和向下箭头键来访问会话中所用命令的历史记录。

假设你执行了`'ls -la'`，然后是`'ls'`。如果你点击向上箭头键，命令`<input>`将会被上一次使用的命令所填充；也就是`'ls'`。再次点击向上箭头键，命令输入将显示`'ls -la'`。点击向下箭头键。当您在命令历史中移动时，您再次到达`'ls'`。[自己试试](http://www.w3clubs.com/sp/ajax2/webconsole.html)。

这个功能的实现并不难。我们只需要一个数组来存储到目前为止执行的所有命令:
 `WebConsole.commands_history = [];`

…以及一个数组指针(一个整数),它会记住我们在哪里:

```
WebConsole.history_pointer = 0;
```

下面是`WebConsole.keyEvent()`函数的清单。处理历史功能的行以粗体显示。

```
WebConsole.keyEvent = function(event)  

{  

  var the_url, the_shell_command;  

  switch(event.keyCode){  

    case 13:  

      the_shell_command = document.getElementById('command').value;  

      if (the_shell_command) {  

        **this.commands_history[this.commands_history.length] = the_shell_command;  

        this.history_pointer = this.commands_history.length;**  

        the_url = 'exec.php?command=' + escape(the_shell_command);  

        makeHttpRequest(the_url, WebConsole.printResult);  

      }  

      break;  

    **case 38: // this is the arrow up  

      if (this.history_pointer > 0) {  

        this.history_pointer--;  

        document.getElementById('command').value = this.commands_history[this.history_pointer];  

      }  

      break;  

    case 40: // this is the arrow down  

      if (this.history_pointer < this.commands_history.length - 1 ) {  

        this.history_pointer++;  

        document.getElementById('command').value = this.commands_history[this.history_pointer];  

      }  

      break;**  

    default:  

      break;  

  }  

}; 
```

以下是关于该函数如何提供命令历史的一些说明:

*   当我们按 Enter(键代码 13)并发出请求时，执行的命令被添加到`commands_history`数组，数组指针被重置为数组的新长度。
*   当点击向上箭头(按键代码 38)时，这意味着“返回”，我们递减 history_pointer 并用历史列表中的前一个命令填充命令`<input>`。
*   点击向下箭头，指针加 1，我们看到下一个命令。

##### 使用 XML

到目前为止，我们还没有讨论如何请求和使用 XML 文档 Ajax 中的 X！我们使用了 XMLHTTP 对象的`responseText`属性。请求文档与我们已经看到的没有什么不同:我们只需要指示我们的可重用请求函数返回`responseXML`，而不是`responseText`。为此，我们将第三个参数设置为`true`:

```
makeHttpRequest(the_url, 'printResult', true);
```

然后，我们需要修改我们的`exec.php`脚本来返回有效的 XML，而不是纯文本。下面是新脚本的源代码(`exec_xml.php`):

```
<?php   

// $allowed_commands same as previous example   

header('Content-Type: text/xml');   

echo '<?xml version="1.0" ?>' . "n";   

echo '<exec>' . "n";   

echo '<command>' . htmlentities($_GET['command']) . '</command>' . "n";   

echo '<result>';   

if (!empty($_GET['command']) && in_array($_GET['command'], $allowed_commands)) {   

    $result = array();   

    exec($_GET['command'], $result);   

    if (!empty($result)) {   

        $result = array_map('htmlentities', $result);   

        echo '<line>';   

        echo implode("</line>n<line>", $result);   

        echo '</line>';   

    } else {   

        echo '<line>No output from this command. A syntax error?</line>';   

    }   

} else {   

    echo "<line>This demo version lets you execute shell commands only from a predefined list:</line>n";   

    echo '<line>';   

    echo implode("</line>n<line>", $allowed_commands);   

    echo '</line>';   

}   

echo '</result>' . "n";   

echo '</exec>';   

?> 
```

这样，如果我们执行命令`'ls test.html'`，新的服务器端脚本将返回以下内容:

```
<?xml version="1.0" ?>    

   <exec>    

       <command>ls test.html</command>    

       <result>    

           <line>test.html</line>    

       </result>    

   </exec> 
```

如果我们执行一个返回更多行的命令(比如`'ls -la'`，响应中的每一行都将被包装在`<line>`标签中。

我们将使用 JavaScript DOM 函数浏览上面的 XML 文档，以便处理`<result>`并在结果`<div>`中显示它。

下面是新的`WebConsole.printResult()`方法的主体:

```
WebConsole.printResult = function(xmldoc)   

{   

  var result_div = document.getElementById('result');   

  var result_collection = xmldoc.getElementsByTagName('line');   

  var new_command = xmldoc.getElementsByTagName('command')[0].firstChild.nodeValue;   

  result_div.appendChild(document.createTextNode(new_command));   

  result_div.appendChild(document.createElement('br'));   

  var number_of_items = result_collection.length;   

  var result_wrap, line;   

  for (var i = 0; i < number_of_items; i++) {   

    if (result_collection[i].hasChildNodes()) {   

      result_wrap = document.createElement('pre');   

      line = document.createTextNode(result_collection[i].firstChild.nodeValue);   

      result_wrap.appendChild(line);   

      result_div.appendChild(result_wrap);   

    }   

    result_div.appendChild(document.createElement('br'));   

  }   

  result_div.appendChild(document.createTextNode(':-> '));   

  result_div.scrollTop = result_div.scrollHeight;   

  document.getElementById('command').value = '';   

}; 
```

为了用 XML 文档中的数据更新结果`<div>`,我们遵循以下过程:

1.  从源 XML 访问节点。

3.  获取其价值。

5.  创建一个新节点。

7.  将其附加到`<div>`目标树。

正如您在代码中看到的，使用了`xmldoc.getElementsByTagName` ( `'command'`)，它返回所有`<command>`节点的集合(一个类似数组的列表对象)。在我们的例子中，只有一个这样的节点。我们通过以下方式获取它的值:

```
xmldoc.getElementsByTagName('command')[0].firstChild.nodeValue;
```

我们获取节点值，并创建一个新的文本节点添加到`<div>`中，如下所示:

```
var new_command = xmldoc.getElementsByTagName('command')[0].firstChild.nodeValue; 

result_div.appendChild(document.createTextNode(new_command));
```

我们对 XML 文档的`<result>`标签做了同样的处理。首先，我们得到所有的`<line>`:

```
var result_collection = xmldoc.getElementsByTagName('line');
```

然后，我们遍历`result_collection`中的每个元素。同样，我们将结果的每一行包装在`<pre>`标签中。

如您所见，使用 XMLDocument 并不比使用纯文本响应困难多少。你可以自己测试 web 控制台的 [XML 版本。](http://www.w3clubs.com/sp/ajax2/webconsole_xml.html)

##### 使用 jQuery

jQuery 是一个流行的 JavaScript 库。让我们尝试将它用于我们的 Ajax 功能，而不是可重用的`makeHttpRequest()`函数。

首先你需要从[这里](http://docs.jquery.com/Downloading_jQuery#Current_Release)下载库的最新版本(我建议是缩小版)并把它包含在页面中:

```
<script type="text/javascript" src="jquery-1.2.3.min.js"></script>
```

我们曾经这样称呼`makeHttpRequest()`:

```
the_url = 'exec.php?command=' + escape(the_shell_command);

makeHttpRequest(the_url, WebConsole.printResult);
```

使用 jQuery 的`Ajax()`方法，你现在可以做:
` 
var xhr = $.Ajax({  
 url: 'exec.php',  
 data: {'command': the_shell_command},  
 success: WebConsole.printResult  
});`

让我们看看这里有什么:

*   `$`是 jQuery 的快捷名称；你也可以这样做:`jQuery.Ajax`
*   我们调用`Ajax()`方法，并传递一个包含要请求的 URL 的对象、一个数据对象(将被 jQuery 转义并转换为查询字符串)以及一个响应到达时要调用的回调函数。

使用 jQuery 的 web 控制台的一个工作示例是这里的。

用 jQuery 实现 Ajax 还有更多方法，看看[文档](http://docs.jquery.com/Ajax)就知道了。例如，使用文件(`test.html`)的内容更新`<div>`(id 为`mydiv`)的一个经常重复的任务可能很简单:

```
$("#mydiv").load("test.html");
```

这里看一个例子[。](http://www.w3clubs.com/sp/ajax2/jsimple.html)

##### 使用 YUI

另一个流行的 JavaScript 库是 [YUI](http://developer.yahoo.com/yui/) (雅虎接口库)。让我们看看如何让我们的 web 控制台与 YUI 的 Ajax 功能一起工作。

我们不需要下载 YUI 的文件，因为它们已经由雅虎免费托管，可以在当前位置使用。Ajax 功能由[连接管理器实用程序](http://developer.yahoo.com/yui/connection/)提供，它包含在您的页面中，如下所示:

```
<script type="text/javascript" src="http://yui.yahooapis.com/2.5.1/build/yahoo/yahoo-min.js"></script>

<script type="text/javascript" src="http://yui.yahooapis.com/2.5.1/build/event/event-min.js"></script>

<script type="text/javascript" src="http://yui.yahooapis.com/2.5.1/build/connection/connection-min.js"></script>
```

现在，为了利用 YUI，我们将对`makeHttpRequest()`的调用替换为:
` 
// YUI's Ajax  
YAHOO.util.Connect.asyncRequest(  
   'GET',  
   'exec.php?command=' + escape(the_shell_command),  
   {  
     success: function(xhr){  
       WebConsole.printResult(xhr.responseText)  
     }  
   }  
);`

你可以看到 [`asyncRequest()`方法](http://developer.yahoo.com/yui/docs/YAHOO.util.Connect.html)采用:

*   请求方法(GET、POST、HEAD、DELETE 等)
*   统一资源定位器
*   包含处理成功和失败情况的函数的对象

YUI 将 XMLHttpRequest 对象传递给处理函数，所以在这种情况下，我们只需获取`responseText`的内容并将其转发给`printResult()`。

您可以看到 URL 是如何通过连接和转义字符串来创建的。在这种情况下，我们只想通过查询字符串传递一个值。但如果多了，就变得相当不方便了。YUI 通过提供一种`setForm()`方法来帮助你轻松处理这种情况。一旦设置好表单，YUI 将从表单中获取值，并负责转义和拼接查询字符串:
` 
YAHOO.util.Connect.setForm(document.forms[0]);  
YAHOO.util.Connect.asyncRequest(  
   'GET',  
   'exec.php',  
   {  
     success: function(xhr){  
       WebConsole.printResult(xhr.responseText)  
     }  
   }  
);`

这里有一个使用 YUI 的 web 控制台的[工作示例](http://www.w3clubs.com/sp/ajax2/yuiconsole.html)。

***使用 JSON***

JSON (JavaScript Object Notation)是一种流行的数据交换格式。在从服务器向浏览器传递数据时，它为您提供了纯文本或 XML 之外的另一种选择。JSON 极其简单；本质上就是 JavaScript 而已。

在 JavaScript 中，您可以像这样定义一个数组和一个对象:

```
var a = new Array(1,2,3);

var o = new Object();

o.property = 'value';

o.property2 = 2; 
```

您可以做同样的事情，但是使用数组和对象文字，就像这样:

```
var a = [1,2,3];

var o = {'property': 'value', 'property2': 2};
```

JSON 使用这种文字符号来传递数据。在 JavaScript 中，属性的引号在大多数情况下是不需要的，但是按照惯例，在 JSON 中是需要的。

让我们更改控制台，使其使用 JSON 格式传输数据。流程将是:

1.  服务器端的 PHP 用结果创建一个关联数组，然后使用 PHP5 专用的内置函数 [`json_encode()`](http://au2.php.net/json_encode) 将其转换为 JSON，但是即使手动编码响应也是微不足道的。返回 JSON 字符串。

3.  浏览器中的 JavaScript 接收 JSON 字符串，并将其转换为本地 JavaScript 对象。一种不安全的方法是使用`eval()`函数。更好的方法是使用免费的 JSON 库。

例如，如果我们执行的命令是`ls`，来自服务器的 JSON 响应将如下所示(为了可读性，进行了格式化和删节):

```
{   

    "command":"ls",   

    "result":[   

        "exec.php",   

        "exec_json.php",   

        "exec_xml.php",   

        "httprequest_example.html",   

        "httprequest_test.html"   

        // ... and so on   

    ]   

} 
```

如您所见，JSON 比 XML 更轻量级，因为没有结束标记、XML 文档标记或根节点。

更改我们的服务器端脚本以返回 JSON，结果如下:

```
exec_json.php:    

<?php   

// $allowed_commands same as before   

$return = array('command' => $_GET['command']);   

if (!empty($_GET['command']) && in_array($_GET['command'], $allowed_commands)) {   

    $result = array();   

    exec($_GET['command'], $result);   

    if (!empty($result)) {   

        $return['result'] = $result;   

    } else {   

        $return['result'] = array('No output from this command. A syntax error?');   

    }   

} else {   

    $return['result'] = $allowed_commands;   

    array_unshift(   

        $return['result'],   

        'This demo version lets you execute shell commands only from a predefined list:'   

    );   

}   

echo json_encode($return);   

?> 
```

在 JavaScript 中，`WebConsole.printResult`接受数据的部分将变成:

```
WebConsole.printResult = function(json_string)   

{   

  var data = eval('('+ json_string +')');   

  var result_array = data.result;   

  // ... same as before   

} 
```

您可以看到在`eval()`之后，数据如何变成一个普通的 JavaScript 对象，并且您可以访问它的属性，比如`data.result`和`data.command`。如前所述，`eval()`是一种不太安全的将 JSON 编码的字符串转换成对象的方法。更好的方法是使用 [JSON 库](http://json.org/json2.js)，它帮助我们用下面的代码替换`eval()`调用:

```
var data = JSON.parse(json_string);
```

一个有效的 JSON 例子是这里的。

***安全提醒**
为了对这个应用程序进行[演示，我只允许在我的 web 服务器上执行一组预定义的无害命令。如果您扩展命令列表或允许任何命令，不要忘记保护服务器上您将安装应用程序的目录。让陌生人访问这个应用程序可能会带来灾难性的后果。这是非常强大的:它将允许用户执行任何命令，包括，但不限于，删除你的网络服务器上的一切！](http://www.w3clubs.com/sp/ajax2/webconsole_xml.html)*

##### 结论

我们的示例 Ajax 应用程序已经接近尾声。你知道基本的，你看到了行动，你有足够的知识武装自己开始实验。您可以通过修改和试验本文的代码来热身——这些代码都包含在[可下载代码档案库](https://i2.sitepoint.com/examples/ajax2/ajax2-code.zip)中——然后转移到您自己的项目中。

这是激动人心的时刻:由于远程脚本，网络的面貌正在发生巨大的变化。我们已经过了早期采用者的阶段(Google、Amazon、Flickr、Yahoo ),现在使用 Ajax 的远程脚本在创建响应迅速且用户友好的网页时变得越来越普遍。你现在的访问者已经被 GMail 和 Flickr 惯坏了，你不能用静态的 Web 1.0 风格的页面来侮辱他们！

## 分享这篇文章