# 使用生成的数据 URIs 减少 HTTP 请求

> 原文：<https://www.sitepoint.com/reducing-http-requests-with-generated-data-uris/>

我不是一个真正的服务器端的家伙，但我确实涉足这里和那里，前几天我有这个非常好的想法，我想分享。当然，对于有经验的 PHP 程序员来说，这可能有点过时了！但是我希望你会对我的实现感兴趣，谁知道呢——也许我会让某人高兴一天！

想法是这样的:你可以**通过预处理源代码并把它们转换成`data`来减少页面对其图像的 <abbr title="HyperText Transfer Protocol">HTTP</abbr> 请求的数量。**其实只要涉及的数据总量不威胁到 <abbr title="Hypertext Pre-Processor's">PHP 的</abbr>内存限制，就可以**把数字减为零！**

[数据 URI 方案](https://www.sitepoint.com/why-the-data-uri-scheme-could-help-save-your-slow-site/)是一种将数据包含在网页中的方法，就好像它是一种外部资源一样。它可以用于任何类型的数据，包括图像、脚本和样式表，并且在所有现代浏览器中都得到支持:Gecko 浏览器，如 Firefox 和 CaminoWebkit 浏览器像 Safari、Konqueror、Chrome 当然是歌剧；和 <abbr title="Internet Explorer 8">IE8</abbr> (但不是 <abbr title="Internet Explorer 7">IE7</abbr> 或更早)。

正如谷歌很快测试的那样，我不是第一个想到用它们来优化页面的人。但是我看到的实现都是围绕着手动重写图像路径，将它们指向脚本，就像这样:

```
 <img src="<?php echo data_uri('images/darwinfish.png'); ?>" alt="Darwin Fish" /> 
```

**我提议的是一个*回顾*过程，它会为你转换所有的图像路径**，这样你就不必在最初创作页面时做任何特殊的事情。

## 代码是心脏所在

下面的例子是一个完整的演示页面，原始的 <abbr title="HyperText Markup Language">HTML</abbr> 和 <abbr title="Cascading Style Sheets">CSS</abbr> ，周围是 <abbr title="Hypertext Pre-Processor">PHP</abbr> 。

*   [在线查看这个例子](http://www.brothercake.com/reference/datagen/demo.php)(并使用 Firebug 检查 <abbr title="HyperText Markup Language">HTTP</abbr> 请求)
*   [下载演示压缩文件](http://www.brothercake.com/download.php?name=datagen.zip&path=reference/datagen/datagen.zip)

该页面包含五个`<img>`元素和一个 <abbr title="Cascading Style Sheets">CSS</abbr> `background-image`，然而在受支持的浏览器中，它使得**根本没有额外的 <abbr title="HyperText Transfer Protocol">HTTP</abbr> 请求**:

```
<?php 
if($datauri_supported = preg_match("/(Opera|Gecko|MSIE 8)/", $_SERVER['HTTP_USER_AGENT'])) 
{ 
   ob_start(); 
}
?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html  lang="en" xml:lang="en">
<head>

   <title>Data URI Generator</title>
   <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

   <style type="text/css">

      body
      {
         background:url(images/texture.jpeg) #e2e2dc repeat;
         color:#554;
      }   

   </style>

</head>
<body>

   <p>
      <img src="images/dropcap.jpg" alt="dropcap.jpg" />
      <img src="images/firefox.png" alt="firefox.png" />
      <img src='images/specificity.jpg' alt='specificity.jpg' />
      <img src='images/darwinfish.png' alt='darwinfish.png' />
      <img src="images/rolleyes.gif" alt="rolleyes.gif" />
   </p>

</body>
</html>
<?php

if($datauri_supported)
{
   function create_data_uri($matches)
   {
      $filetype = explode('.', $matches[2]);
      $filetype = strtolower($filetype[count($filetype) - 1]);

      if(!preg_match('/^(gif|png|jp[e]?g|bmp)$/i', $filetype))
      {
         return $matches[0];
      }

      if(preg_match('/^//', $matches[2]))
      {
         $matches[2] = $_SERVER['DOCUMENT_ROOT'] . $matches[2];
      }

      @$data = base64_encode(file_get_contents($matches[2]));

      return $matches[1] . "data:image/$filetype;base64,$data" . $matches[3];
   }

   $html = ob_get_contents();
   ob_end_clean();

   $html = preg_split("/r?n|r/", $html);
   while(count($html) > 0)
   {
      $html[0] = preg_replace_callback("/(src=["'])([^"']+)(["'])/", 'create_data_uri', $html[0]);
      $html[0] = preg_replace_callback("/(url(['"]?)([^"')]+)(["']?))/", 'create_data_uri', $html[0]);

      echo $html[0] . "rn";

      array_shift($html);
   }
}

?>
```

## 这一切是如何运作的

这一切的核心是使用 base64 编码的图像数据构建`data` <abbr title="Uniform Resource Identifiers">URIs</abbr> 的能力。

但除此之外，我们还需要几个关键的技巧来让这一切都工作起来。首先，使用[输出缓冲区](http://www.php.net/manual/en/intro.outcontrol.php)预编译输出源，这样我们就有机会在将它发送到浏览器之前再次解析它。

您将看到，在代码的最顶端，我设置了一个浏览器条件来决定是否启动输出缓冲区。然后，围绕底部的主要代码再次使用相同的条件，所以对于不支持这种技术的浏览器，所有的脚本都被绕过，它只是正常输出页面。然后我所做的是通过换行来分割 HTML ,这样我们可以处理、输出然后立即删除每一行，这避免了必须在内存中保存整个源代码。

其次，为了实现实际的解析，我使用了 [`preg_replace_callback`](http://php.net/manual/en/function.preg-replace-callback.php) 函数，它用一对正则表达式来标识 <abbr title="HyperText Markup Language">HTML</abbr> 和 <abbr title="Cascading Style Sheets">CSS</abbr> 路径，并通过一个过于复杂的过程对它们进行简单的替换。(我们必须分别寻找`src`属性和`url`属性，因为语法差别太大，单个<abbr title="regular-expression">正则表达式</abbr>无法生成相同的匹配数组。)

在回调函数中，我们首先必须计算出文件类型，这是输出数据所需要的，也是允许类型的条件，因此我们可以拒绝任何不是图像的东西(比如脚本`src`)。传递给函数的`$matches`数组总是包含整个子串匹配作为它的第一个成员(后面跟着来自`[1]`的反向引用)，所以如果我们发现一个我们不想要的文件，我们可以不加修改地返回第一个匹配，这样就完成了。

接下来要做的唯一一件事就是检查 web 根路径，这需要在前面加上`DOCUMENT_ROOT`来创建一个可用的文件路径。一旦我们有了所有这些，我们就可以抓取并编码图像(在原始路径被破坏的情况下使用错误抑制)，然后编译并返回`data`。太容易了！

## 什么时候优化不是优化？

当成本大于节约的时候！当然，像这样的过程不是免费的——我们必须考虑几个潜在的成本。

作为数据 URIs 的图像比原始图像大了三分之一。这样的图像也不会被缓存——或者至少，它们不会将*作为图像*进行缓存，而是将**作为源代码的一部分**进行缓存。这种方式的缓存更多的是一种粗线条，而不是精细点，但它至少允许离线查看。

首先还存在每次转换的处理开销，随着文件变得越来越大。只要它在同一台服务器上，加载它不会有很大的延迟，但是 **base64 编码是一个相对昂贵的过程**。

因此，使用这种技术的最佳方式可能是，不要对所有的图像使用这种技术，而只对大量的小图像使用这种技术，比如图标和背景切片。额外的代码量很小，处理量也很小，但是删除几十个请求的好处是很大的，可以创建一个加载速度更快的页面。

比如说，你只想处理 <abbr title="Graphics Interchange Format">GIF</abbr> 图像，只需修改允许的文件类型 <abbr title="regular expression">regex</abbr> 就可以轻松完成:

```
if(!preg_match('/^(gif)$/i', $filetype))
{
   return $matches[0];
}
```

或者，您可以使用 [`filesize`](http://php.net/manual/en/function.filesize.php) 函数按大小过滤，只对低于特定阈值的*进行转换:*

```
if(filesize($matches[2]) > 1024)
{
   return $matches[0];
}
```

就*大*图片而言，我了解到浏览器对`data`<abbr title="Uniform Resource Identifiers">【URIs】</abbr>的尺寸有限制；然而，我在实践中没有观察到任何这样的限制。Firefox、Opera、Safari，甚至是 IE8 和 T6 都非常乐意显示超过 1MB 的图像数据。加速测试后，我发现自己达到了 PHP 的内存极限，却没有收到浏览器的任何抱怨！**要么是我完全没有抓住要点，要么是没有尺寸限制。**

## 向西走。

在试验过程中，我也尝试了 JavaScript 和 CSS 然而，这在 Internet Explorer 中不起作用，所以我没有进一步追求它。

但是一个真正有趣的发展是，看看是否有可能开发某种*算法*来计算在不同情况下实施这项技术的成本<abbr title="versus">与</abbr>收益。考虑到页面本身的大小和复杂性，每个图像的数量和大小，重复的 <abbr title="Cascading Style Sheets">CSS</abbr> 图像与特定内容图像的比率，以及转换和编码所需的时间，与平均网络请求相比。然后将所有这些整合在一起，计算出哪些图像将从转换中受益，哪些最好保持原样。如果我们能以一种连贯而自动的方式做这些事情，我们就会有一个非常漂亮的 WordPress 插件，嘿！

但是说实话，我真的不知道你会从哪里着手解决这样的事情！有几个不可量化的，许多判断调用。不过，这的确是值得思考的事情；或许你——我亲爱的读者——可以提供一些新的见解？

*缩略图鸣谢:[斯蒂芬](http://www.flickr.com/photos/st3f4n/4327938147/)*

## 分享这篇文章