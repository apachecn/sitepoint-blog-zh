# 构建自己的 Chrome 扩展——第 2 部分

> 原文：<https://www.sitepoint.com/build-your-own-chrome-extension-a-google-documents-word-count-tool-part-2/>

你好，欢迎来到我们构建你自己的 Chrome 扩展教程的第 2 部分！

在本系列的上一期中，我们创建了一个简单的 Google Chrome 扩展，它为您打开的 Google 文档添加了一个持久单词计数器。我们的扩展检测它何时处于应该激活的上下文中，并定期运行它自己的部分，这有助于它计算文档中的字数。虽然不太准确，但这是一个不错的、可用的第一个版本，很好地满足了它最初的目的。

现在让我们来看看如何稍微改进一下。在本系列的这一部分中，我们将更新清单文件，添加页面计数器，增加单词计数器的精度，最后但同样重要的是，完全删除 jQuery 并用普通的 JavaScript 替换它。

我们开始吧！

## 更新清单文件

正如 [Michael](https://www.sitepoint.com/build-your-own-chrome-extension-a-google-documents-word-count-tool/#comment-1057751 "Michael") 在前一期的评论部分和 [Mohamed Mansour](https://plus.google.com/u/0/116805285176805120365/posts "Mohamed Mansour") 在我的 [Google+帖子](https://plus.google.com/u/0/104771776404197897488/posts/HEZew2r4y9q "Create your own Google Chrome Extension Google+ post")中指出的，Google 正在逐步[淘汰对 manifest v1 文件的支持](http://developer.chrome.com/extensions/manifestVersion.html "Chrome extension manifest files")。

如前所述，清单文件是向 Chrome 描述一个扩展做什么、如何做以及是否应该允许它做一些事情的文件。随着 web 的发展和 Google Chrome 添加新的开发人员特性，这些文件中的指令经常会随着新版本而改变，因此为了保持我们的扩展在 Chrome Web Store 上“适销”,我们需要在清单文件中的任意位置添加以下行:

```
 "manifest_version" : 2, 
```

我立马加在了“版本”下面。现在，让我们把版本值提高到 0.2。

然而，新版本的清单有一些额外的要求。我们现在需要在一个`web_accessible_resources`键中列出我们将要加载的所有资源。如果没有它，我们的`statusbar.html`将无法加载，并会抛出错误“拒绝加载 chrome-extension://…/status bar . html。资源必须在`web_accessible_resources`清单键中列出，以便由网页加载。”为了避免这种情况，我们只需将以下内容添加到清单文件中:

```
 "web_accessible_resources" : ["statusbar.html"] 
```

就是这样！如果你现在尝试重新加载扩展(按照[第一部分](https://www.sitepoint.com/build-your-own-chrome-extension-a-google-documents-word-count-tool "Build your own Chrome Extension"))，一切都应该如预期的那样进行，并且不应该显示任何警告。

## 添加页面计数器

我们上次了解到，Google 文档中的“row”元素是一个带有“kix-lineview-text-block”类的 span。通过进一步检查，我们了解到包含实际页面的元素是一个 div，其类为“kix-page”。因此，在单词计数器中添加一个页面计数器应该一点也不麻烦。

将 main.js 中 countWords()方法的内容更改为以下内容:

```
var pageCount = $('div.kix-page').length; 
var wordCount = 0; 
$('span.kix-lineview-text-block').each(function(i, obj){ 
  wordCount += $(obj).text().split(/s+/).length; 
}); 
$('span#GDWC_wordsTotal').text(pageCount + ' pages, ' + wordCount + ' total words'); 
timeout = setTimeout('countWords()', 5000); 
```

如您所见，我们添加了一个新变量`pageCount`。因为没有什么要分开的，而且元素已经被定义为页面，所以我们要做的就是使用 length 属性对它们进行计数。然后，我们只需将“页码”消息添加到“总字数”消息中，就万事俱备了。请随意重新加载扩展并尝试一下。

## 增加字数统计的精确度

您可能已经注意到，我们的单词计数器使用一个简单的空格来分隔字符串并计算字数。让我们通过改变`countWords()`函数的这一行来使它稍微精确一点:

```
wordCount += $(obj).text().split(/s+/).length; 
```

到

```
words = $(obj).text().match(/S+/g); 
wordCount += words && 
words.length || 0; 
```

除非文档以空格字符结尾，否则拆分会导致计数不准确，现在我们可以全局匹配每个非空格字符序列。这意味着每个不是空白字符的字符都被解释为一个单词，这也更接近人类对“单词”的定义。

需要注意的是，Google Docs 是动态加载内容的:也就是说，只在请求时加载。因此，当您第一次启动一个已经包含一些内容的文档时，首先滚动浏览所有内容并返回到顶部，这样浏览器就会接收到整个文档的数据。

但是，如果我们想排除标点符号和其他符号触发字数增加呢？所有这些“…”，逗号，句号和失控的撇号可能会抵消适当的计数，我们会更好地没有他们。让我们替换这条线

```
words = $(obj).text().match(/S+/g); 
```

随着

```
var words = $(obj).text().replace(/W+/g, ' ').match(/S+/g); 
```

我们所做的是用一个空格替换每组一个或多个非字母数字字符。这意味着“…”和“###”变成了一个空格，就像逗号、句号等符号一样，从而不能算作单词。虽然这确实增加了删除无用字符的精度，但在计算日期等字符串时却降低了一些精度。例如，1998.03.05 将变成 1998 03 05，从而算作三个单词。这引入了一些新的困难，我们可能会在下一期解决。现在，让我们就此打住。

## 删除 jQuery

虽然这对于 Chrome 扩展来说没有网站那么重要，因为所有文件都下载到客户端并保存在那里(每次运行扩展时都不会远程下载 jQuery)，但删除 jQuery 会稍微减少我们的文件大小和内存占用，并允许我们查看一些替代的 JavaScript 语法。此外，因为我们不需要 jQuery 的向后兼容性和跨浏览器可操作性，因为我们只为 Google Chrome 构建了这个扩展，所以拥有它的全部功能有点大材小用。

由于`main.js`是我们唯一包含 jQuery 的文件，现在让我们打开它，从第一个命令开始 ajax 调用我们的`statusbar.html`文件。变化

```
 $.get(chrome.extension.getURL("statusbar.html"), {}, function(data) {$('body').append(data);}, 'html'); 
```

到

```
var xhReq = new XMLHttpRequest(); 
xhReq.onreadystatechange = function onSumResponse() { 
  if (xhReq.readyState == 4) { 
    var serverResponse = xhReq.responseText; 
    var body = document.getElementsByTagName("body")[0]; 
    var div = document.createElement('div'); 
    div.innerHTML = serverResponse; 
    body.appendChild(div); 
  } 
} 
xhReq.open("GET", chrome.extension.getURL("statusbar.html"), true); 
xhReq.send(null);
```

嗯，我们把一行极其简单的代码弄得一团糟。或者我们有吗？这基本上就是我们之前的代码所做的——它只是作为与这个调用相同的调用的包装器。因此，虽然这增加了一点代码，但回过头来看，这实际上导致运行的*代码减少了*代码，因为没有调用 jQuery 的开销，而 jQuery 又需要决定接下来调用哪个 XHR 包装器，等等。那么这一团乱麻是做什么的呢？首先，它实例化了一个 XMLHttpRequest，这是一个 JS 对象[，用于将 HTTP 或 HTTPS 请求直接发送到 web 服务器，并将服务器响应数据直接加载回脚本](http://en.wikipedia.org/wiki/XMLHttpRequest "Wikipedia")。本质上，它是执行 Ajax 调用的东西。然后，我们确保当它的`readyState`属性变为 4 (ready)时，它获取我们响应的文本(我们的 statusbar)，将其注入一个空 div，并将这个 div 附加到“body”的末尾。最后，我们用`open()`和`send()`开始请求。

现在让我们把注意力转向检查文档是否可以使用了。替换

```
$(document).ready(function(){ 
  countWords(); 
});
```

随着

```
var readyStateCheckInterval = setInterval(function() { 
  if (document.readyState === "complete") { 
    countWords(); 
    clearInterval(readyStateCheckInterval); 
  } 
}, 10);
```

这个代码片段删除了 jQuery 检查文档是否准备好进行操作的方法，并创建了一个间隔检查，每隔 10ms 检查一次文档是否准备好。一旦检测到，它就调用`countWords()`，清除间隔，检查停止。

现在，让我们看看我们能对`pageCount`变量做些什么。替换

```
var pageCount = $('div.kix-page').length; 
```

随着

```
var pageCount = 0; 
var divs = document.getElementsByTagName('div'), i; 
for (i in divs) { 
  if((" " + divs[i].className + " ").indexOf(" kix-page ") > -1) { pageCount++; } 
}
```

这将获取网站中的所有 div，并查看它们的类属性是否包含我们的属性。

现在让我们用一个自制的 jQuery span 循环来替换这个单词计数的循环。替换

```
$('span.kix-lineview-text-block').each(function(i, obj){ 
  var words = $(obj).text().replace(/W+/g, ' ').match(/S+/g); 
  wordCount += words && 
  words.length || 0; 
});
```

随着

```
var spans = document.getElementsByTagName('span'), i; 
for (i in spans) { 
  if((" " + spans[i].className + " ").indexOf(" kix-lineview-text-block ") > -1) { 
    var words = spans[i].innerText.replace(/W+/g, ' ').match(/S+/g); 
    wordCount += words && 
    words.length || 0; 
  } 
}
```

最后，我们可以替换

```
$('span#GDWC_wordsTotal').text(pageCount + ' pages, ' + wordCount + ' total words');
```

随着

```
document.getElementById('GDWC_wordsTotal').innerText = pageCount + ' pages, ' + wordCount + ' total words';
```

…实际显示没有 jQuery 的消息。当然，我们还需要从扩展清单中删除 jQuery 的加载，所以更改

```
"js": ["jq.js","main.js"],
```

到…里面

```
"js": ["main.js"],
```

并且可以随意删除`jq.js`文件。

## 结论

这是创建 Google Chrome 扩展的三部分系列的第二部分，我们来看看如何稍微修改我们的扩展，以使它执行得更快，并使它达到 Google 的最新开发标准。我们为字数统计增加了一些精度，在字数统计的同时实现了一个页面计数器，用一些新的必需指令和版本声明更新了清单文件，并承担了将 jQuery 代码转换为普通 JavaScript 的庞大任务，从而提高了速度、减少了内存使用量并减小了文件大小。在本系列的下一期也是最后一期，我们将进一步升级扩展的性能，并为状态栏本身添加一些更有用的功能。敬请期待！

## 分享这篇文章