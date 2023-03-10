# 用 PHP 和 JavaScript 跟踪上传进度

> 原文：<https://www.sitepoint.com/tracking-upload-progress-with-php-and-javascript/>

多年来一直困扰 web 开发人员的一个问题是如何在他们的应用程序中添加实时信息，例如文件上传的进度条。用户不耐烦；他们不想在浏览器运行的时候坐着等待，想知道它是不是死机了，或者他们的连接速度很慢。提供进度指示器可以给用户提供有用的信息，让他们确切地知道正在发生什么。

乍一看，您可能认为通过首先从用户的计算机上获取文件的大小，然后对文件上传到的服务器上的目录执行一些简单的计算，就可以很容易地实现这一点。仔细想想，你会发现事情没那么简单。

JavaScript 可以访问文件的名称、类型，甚至本地图像的宽度和高度，但直到 [HTML5，它才可以访问文件的大小](http://stackoverflow.com/q/4112575/322819)。不幸的是，HTML5 仍然不是一个完整的标准，也没有得到所有浏览器的统一支持。另一种解决方案是依靠 Flash、Java 或 ActiveX 插件；不，谢谢，我不去了。还有一个解决方案是安装[替代 PHP 缓存](http://php.net/apc)扩展，但这可能不可用，这取决于您的托管环境，对于这样的小任务来说，这似乎是大材小用。

似乎所有的选择都充满了麻烦，任务很快变得令人头痛。但是用尤达的话说，“不…还有一个。”

我喜欢 PHP 的原因之一是它让看似困难的任务变得简单。从 PHP 5.4 开始，他们用一组新的配置指令`session.upload_progress`再次做到了这一点。

在本文中，我将向您展示如何使用该功能创建一个简单的上传进度条，而无需任何外部库或浏览器依赖。我将首先讨论它是如何工作的，然后带您创建完成任务所需的四个文件(一个上传表单、一些 JavaScript、一点 CSS 和一个返回上传状态的文件)。

## 会话上传进度

除了允许文件上传的通常要求之外，还有两个跟踪进度的要求。必须启用`session.upload_progress.enabled`指令，并且 web 表单中必须有一个隐藏字段，其名称由`session.upload_progress.name`指令指定。当`session.upload_progress.enabled`为真(在 PHP 5.4 及更高版本中默认为真)并且`$_POST[*session.upload_progress.name*]`在上传过程中被发送时，关于文件传输的信息在`$_SESSION`超全局数组中可用。

在文件传输期间，`$_SESSION`数组的`print_r()`输出将类似于以下内容:

```
Array
(
    [upload_progress_myForm] => Array
        (
            [start_time] => 1323733740
            [content_length] => 721127769
            [bytes_processed] => 263178326
            [done] => 
            [files] => Array
                (
                    [0] => Array
                        (
                            [field_name] => userfile
                            [name] => ubuntu-10.04.3-desktop-i386.iso
                            [tmp_name] => 
                            [error] => 0
                            [done] => 
                            [start_time] => 1323733740
                            [bytes_processed] => 263178026
                        )
                )
        )
)
```

当你在本地或高速网络上开发并上传小文件时，你不能直观地观察进度，因为传输发生得太快了。在这种情况下，您可能想尝试传输一个大文件。确保您的`php.ini`文件中的设置允许大量上传，特别是`post_max_size`和`upload_max_filesize`指令，然后在您进入生产时验证它们是合理的值。

### 创建表单

我要展示的第一个文件是上传表单。为了使事情尽可能简单，该示例将向自身发送，并且一次只处理一个文件上传。另外，我不会在文件上传后保存它。

以下是`form.php`的代码:

```
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST" && !empty($_FILES["userfile"])) {
    // move_uploaded_file()
}
?>
<html>
 <head>
  <title>File Upload Progress Bar</title>
  <link rel="stylesheet" type="text/css" href="style.css">
 </head>
 <body>
  <div id="bar_blank">
   <div id="bar_color"></div>
  </div>
  <div id="status"></div>
  <form action="<?php echo $_SERVER["PHP_SELF"]; ?>" method="POST" 
   id="myForm" enctype="multipart/form-data" target="hidden_iframe">
   <input type="hidden" value="myForm"
    name="<?php echo ini_get("session.upload_progress.name"); ?>">
   <input type="file" name="userfile"><br>
   <input type="submit" value="Start Upload">
  </form>
  <iframe id="hidden_iframe" name="hidden_iframe" src="about:blank"></iframe>
  <script type="text/javascript" src="script.js"></script>
 </body>
</html>
```

在这个例子中，为了简单起见，省略了实际处理文件的代码。如果你对这样的代码应该是什么样子感兴趣，可以看看 Timothy Boronczyk 的文章[用 PHP](https://www.sitepoint.com/file-uploads-with-php/) 上传文件。

在提供页面标题并包含样式表的 head 部分之后，您会注意到一小部分 div 元素。ID 为“bar_blank”的 div 是进度条的容器。ID 为“bar_color”的 div 将随着文件上传的进行而动态更新。“状态”div 将显示上传百分比的数值。

表单被设置为提交到同一个 URL，它的目标属性指向一个隐藏的 iframe 元素。向隐藏框架提交表单可以让访问者在后台工作的同时保持在同一页面上。事实上，这是进行“Ajax 文件上传”时的常见做法，因为不可能使用 JavaScript 的`XmlHttpRequest`对象直接发送文件内容。

在表单中，出现了填充`$_SESSION`数组所需的特殊隐藏字段，随后是文件上传输入和提交按钮。提交表单将触发一个名为`startUpload()`的 JavaScript 函数，该函数将由包含的 JavaScript 文件定义。

页面底部是表单将发布到的隐藏框架和`script.js`文件的导入。

### 添加一些风格

下一个文件`style.css`，非常简单。我已经定义了进度条容器的大小，并给它一个 1px 的黑色边框，进度条加载时的颜色，iframe 和进度条都是隐藏的。

```
#bar_blank {
  border: solid 1px #000;
  height: 20px;
  width: 300px;
}

#bar_color {
  background-color: #006666;
  height: 20px;
  width: 0px;
}

#bar_blank, #hidden_iframe {
  display: none;
}
```

### 客户端功能

`script.js`文件是该组文件中最大的。它包含六个功能，我将在下面讨论。许多人喜欢使用 jQuery 来提供一些功能，如果您愿意，当然可以这样做，但是我个人更喜欢老式的方法。就像日本人更看重手工制品一样，如果是我自己的代码，我会对它更有热情。

```
function toggleBarVisibility() {
    var e = document.getElementById("bar_blank");
    e.style.display = (e.style.display == "block") ? "none" : "block";
}

function createRequestObject() {
    var http;
    if (navigator.appName == "Microsoft Internet Explorer") {
        http = new ActiveXObject("Microsoft.XMLHTTP");
    }
    else {
        http = new XMLHttpRequest();
    }
    return http;
}

function sendRequest() {
    var http = createRequestObject();
    http.open("GET", "progress.php");
    http.onreadystatechange = function () { handleResponse(http); };
    http.send(null);
}

function handleResponse(http) {
    var response;
    if (http.readyState == 4) {
        response = http.responseText;
        document.getElementById("bar_color").style.width = response + "%";
        document.getElementById("status").innerHTML = response + "%";

        if (response < 100) {
            setTimeout("sendRequest()", 1000);
        }
        else {
            toggleBarVisibility();
            document.getElementById("status").innerHTML = "Done.";
        }
    }
}

function startUpload() {
    toggleBarVisibility();
    setTimeout("sendRequest()", 1000);
}

(function () {
    document.getElementById("myForm").onsubmit = startUpload;
})();
```

`toggleBarVisibility()`函数在“bar _ blank”div 上设置适当的样式，根据需要显示或隐藏进度条。最初它是隐藏的，但是一旦上传开始就会显示，上传完成后又会隐藏起来。

`createRequestObject()`函数根据用户的浏览器创建一个`XMLHttpRequest`或`ActiveXObject`对象。这可能是大多数人希望 jQuery 或其他 JavaScript 框架提供的功能。

`sendRequest()`函数用 GET 请求请求`progress.php`文件，然后调用`handleResponse()`函数处理返回的数据。

`handleResponse()`函数处理来自`progress.php`的响应，该响应将是一个 1-100 之间的数字，取决于文件上传进度。我还用适当的值更新了“status”div。如果当前百分比低于 100%，那么我调用 JavaScript 的本机`setTimeout()`函数，在 1 秒钟后发送另一个更新请求(您可能希望适当地调整这个值)，否则我再次隐藏进度条，并将状态设置为“完成”

`startUpload()`功能使上传栏可见，并在延迟 1 秒后发送更新请求。这种小延迟是必要的，以便给上传时间开始。

最后一个函数是一个自动执行的匿名函数，它用表单的提交事件注册了`startUpload()`。

### 实时进度

最后一个文件是`progress.php`文件:

```
<?php
session_start();

$key = ini_get("session.upload_progress.prefix") . "myForm";
if (!empty($_SESSION[$key])) {
    $current = $_SESSION[$key]["bytes_processed"];
    $total = $_SESSION[$key]["content_length"];
    echo $current < $total ? ceil($current / $total * 100) : 100;
}
else {
    echo 100;
}
```

该脚本执行一些简单的数学运算，将当前传输的字节数除以总文件大小，再乘以 100，最后四舍五入得到一个百分比。

有关传输的信息由 session.upload_progress.prefix 指令的值和隐藏的`session.upload_progress.name`字段的值连接而成。因为我的表单通过了“my form”，所以会话的键是用`ini_get("session.upload_progress.prefix") . "myForm"`确定的。

以下是进度条的屏幕截图:

![upload progress bar](img/61a6ddc69d3319e73cdcdd74e71f59cc.png)

## 微调行为

PHP 提供了一些额外的指令来帮助微调您应该知道的会话上传的行为。例如，`session.upload_progress.cleanup`默认设置为 1，在上传完成后立即清除额外的会话数据。您需要小心避免潜在的竞态条件。

再看一下`progress.php`中的代码，你会注意到我在继续之前检查了一下`$_SESSION[$key]`是否为空。只要从`progress.php`返回的结果小于 100，我的 JavaScript 函数每秒都会触发一次。如果`session.upload_progress.cleanup`被启用，并且我的脚本获取了 99%的上传，1/2 秒后上传完成，`$_SESSION[$key]`在下一次检查时将不存在。如果我没有考虑到这一点，那么我的 JavaScript 函数可能会继续运行，甚至在上传完成之后。

另外两个指令是`session.upload_progress.freq`和`session.upload_progress.min_freq`,它们都决定了会话应该多久更新一次。freq 的值可以以字节(即 100)或总字节的百分比(即 2%)给出。`min_freq`的值以秒为单位给出，表示两次更新之间的最小秒数。显然，如果`min_freq`被设置为每 1 秒更新一次，那么您的 JavaScript 每 100 毫秒检查一次是没有意义的。

## 摘要

现在，您应该已经掌握了如何使用会话上传进度功能为文件上传创建进度条。接下来，我鼓励你尝试上传多个文件，使用`$_SESSION[$key]["cancel_upload"]`或者你能想到的任何其他方法来取消正在进行的上传。请在评论中分享你的经历和改进，让其他人也能从中受益和享受！

图像通过[文件 404](http://www.shutterstock.com/gallery-587221p1.html) / [快门](http://www.shutterstock.com)

## 分享这篇文章