# 当浏览器糟糕的时候:PHP 来拯救！

> 原文：<https://www.sitepoint.com/browsers-suck-php-rescue/>

在这篇短文中，我将演示如何用 PHP 编写的服务器端代码替换冗长的客户端 JavaScript 浏览器检测代码。

在每个网页设计师的职业生涯中，都有这样的时候，浏览器之间的差异使得一段代码不可能在所有的浏览器上工作。例如，在撰写本文时，标准的 SitePoint 页面标题将一些表单字段压缩成相当小的空间。实现这一点的技术因浏览器而异。

在这种情况下，开发人员通常求助于浏览器检测代码，如下所示:

```
<script language="JavaScript" type="text/javascript"> 

if (navigator.appName == "Netscape" && 

    parseInt(navigator.appVersion) > 4) { 

  document.writeln("<!-- Netscape 6 specific code -->"); 

} else { 

  document.writeln("<!-- Code for other browsers -->"); 

} 

</script>
```

这不仅很难阅读和维护，而且当每个浏览器所需的代码片段超过一两行时，所有浏览器都必须下载的额外代码负担会开始减缓页面加载过程。记住，JavaScript 是在用户的浏览器中运行的，所以所有*浏览器的代码都是下载的，尽管实际上只使用了其中一个代码段。*

当代码的大小成为一个问题时，通常最好在服务器端进行浏览器检测。下面是 PHP 中上述 JavaScript 示例的等效示例:

```
<?php 

if (!(strpos($HTTP_USER_AGENT,'Mozilla/5') === false)) { 

  echo("<!-- Netscape 6 specific code -->"); 

} else { 

  echo("<!-- Code for other browsers -->"); 

} 

?>
```

变量`$HTTP_USER_AGENT` 在 Web 服务器运行的所有 PHP 脚本中自动设置。该变量中包含的字符串可用于标识负责页面请求的浏览器。例如，在 Windows 上运行的 Netscape 6.2 产生的`$HTTP_USER_AGENT` 字符串如下:

```
Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:0.9.4) Gecko/20011019 Netscape6/6.2
```

然后我们使用 PHP `strpos`函数来检测`$HTTP_USER_AGENT`变量中的字符串`'Mozilla/5'`。为了区分`'Mozilla/5'`出现在`$HTTP_USER_AGENT`开始(位置 0)的情况(当`strpos`返回`0`时)和根本不出现的情况(当`strpos`返回`false`时)，我使用`===`来检测后一种情况。

这解决了客户端大量代码的问题，但是在服务器端仍然很混乱，代码可能有点难记。解决方案是自己编写一个脚本来执行浏览器检测，并将结果存储在一个变量中，然后可以在整个站点中需要的地方使用该变量。

以下是我们在 SitePoint.com 使用的`browserdetect.php`脚本:

```
<?php 

/** 

 *  $browser will contain one of the following values: 

 *      'iewin' : IE 4+ for Windows 

 *      'iemac' : IE 4 for Macintosh 

 *     'ie5mac' : IE 5 Macintosh 

 *      'nswin' : Netscape 4.x Windows 

 *     'nsunix' : Netscape 4.x Unix 

 *      'nsmac' : Netscape 4.x Mac 

 *        'ns6' : Netscape 6 / Mozilla 

 */ 

function inAgent($agent) { 

    global $HTTP_USER_AGENT; 

    $notAgent = strpos($HTTP_USER_AGENT,$agent) === false; 

    return !$notAgent; 

} 

if ( inAgent('MSIE 4') or inAgent('MSIE 5') ) { 

  if ( inAgent('Mac') ) 

    $browser = inAgent('MSIE 5') ? 'ie5mac' : 'ie4mac'; 

  elseif ( inAgent('Win') ) $browser = 'iewin'; 

} elseif ( !inAgent('MSIE') ) { 

  if ( inAgent('Mozilla/5') or inAgent('Mozilla/6') ) { 

    $browser = 'ns6';  

  } elseif ( inAgent('Mozilla/4') ) { 

    if ( inAgent('Mac') ) $browser = 'nsmac'; 

    elseif ( inAgent('Win') ) $browser = 'nswin'; 

    else $browser = 'nsunix'; 

  } 

} else $browser = "unknown"; 

?>
```

有了这个脚本，服务器端浏览器检测变得非常简单。这里有一个例子:

```
<?php include(browserdetect.php); ?> 

<?php if ($browser == 'ns6'): ?> 

  <!-- Netscape 6 specific code --> 

<?php else: ?> 

  <!-- Code for other browsers --> 

<?php endif; ?>
```

这段代码要整洁得多，特别是如果不是将 HTML 代码直接包含在一个文件中，而是为页面的每个依赖于浏览器的部分使用包含文件。

## 分享这篇文章