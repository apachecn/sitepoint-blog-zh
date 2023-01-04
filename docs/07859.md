# 构建你自己的 Chrome 扩展:谷歌文档字数统计工具

> 原文：<https://www.sitepoint.com/build-your-own-chrome-extension-a-google-documents-word-count-tool/>

像现在的许多人一样，我放弃了桌面文字处理器，转而使用免费简单的谷歌文档。然而，我怀念的一件事是一个持续字数统计的状态栏，它帮助我留意我正在写的文章的字数限制。为了增强我的谷歌文档体验，我决定建一个类似的，以下是方法。

欢迎来到构建和优化 Google Chrome 扩展系列的第一部分。为了理解本教程，推荐使用 HTML、CSS 和 jQuery 知识。另外，请确保你在开发者模式下(在工具->扩展下)有最新版本的谷歌 Chrome 屏幕顶部的复选框)。

在硬盘上的任意位置创建一个文件夹，并给它命名，例如 GDWC。每个扩展的第一步是建立它的清单文件:一个引导程序告诉 Chrome 它是哪种扩展，它在哪个上下文中操作，它需要什么，它做什么，以及它什么时候做。创建一个名为 manifest.json 的文件，包含以下内容:

```
{
	"name": "GDWC",
	"version": "0.1",
	"description": "Word count statusbar for Google Docs!",
	"background" : { "scripts": ["background.js"] },
	"page_action" :
	{
		"default_icon" : "icon.png",
		"default_title" : "GDWC statusbar is active"
	},
	"content_scripts": [
	{
		"matches": ["https://docs.google.com/document/*"],
		"js": ["jq.js", "main.js"],
		"run_at": "document_idle"
	}
	],
	"icons": {
		"48": "icon48.png",
		"128": "icon128.png"
	}
}
```

前几个值如**名称**、**版本**和**描述**是不言自明的。然后，有**后台**指示在后台加载哪个脚本。背景页面和脚本有一个特殊的用途(对它的解释不属于本文的范围)，但是我们的扩展只需要它来激活地址栏中的图标。在此过程中，创建一个 **background.js** 文件，并将以下内容放入其中:

```
chrome.extension.onRequest.addListener(
	function(request, sender, sendResponse) {
		chrome.pageAction.show(sender.tab.id);
		sendResponse({});
	}
);
```

这只是告诉扩展:“调用时显示图标”，仅此而已。

让我们继续来看看 manifest . JSON .**page _ action**块的存在告诉 Chrome 这个扩展属于特定的页面(或一些页面)，而不是整个浏览器。作为页面动作的扩展可以在浏览器的地址栏中有一个图标(default_icon 值)。相比之下，浏览器操作会在地址栏的右侧添加图标。扩展名可以是其中之一，不能同时是两者。既然我们已经谈到了图标，现在让我们添加一些图标，并把它(以及最后一个清单块，**图标**)去掉。我做了几个样品。他们的下载链接在这篇文章的末尾。两个较大的图标(图标 48 和图标 128)是用于 Chrome 网上商店的，所以当其他人在那里查看时，你的扩展看起来很漂亮。

现在让我们看看 **content_scripts** 块。

*   **matches** 用于激活某些网页上的扩展。在我们的例子中，我们的目标是每当谷歌文档中的一个文档打开时激活它，因此 URL https://docs.google.com/document/*
*   **js** 告诉扩展当“匹配”部分被触发时运行哪个脚本。在我们的例子中，我们需要 jq.js(它是 jQuery 的缩小版)和包含计数逻辑的 main.js 脚本。
*   **run_at** 告诉它何时加载这些脚本——在我们的例子中，当页面加载并且文档空闲时。

既然我们的引导已经完成，我们应该构建我们想要添加的 HTML。创建一个**statusbar.html**文件，并粘贴以下内容:

```
<html> 
<body> 
<style type="text/css"> 
div#GDWC_statusBar { 
  width: 100%; 
  height: 18px; 
  background-color: #ebebeb; 
  border-top: 1px solid silver; 
  color: black; 
  position: fixed; 
  bottom: 0; 
  z-index: 25000; 
  text-align: right; 
} 

span.GDWC_statusBarCounter { 
  padding: 0 10px 0 10px; 
  line-height: 18px; 
  font-family: verdana; 
  font-size: 10pt; 
} 

span.GDWC_statusBarCounter a { 
  text-decoration: none; 
  color: black; 
} 

span.GDWC_statusBarSeparator { 
  border-left: 1px solid silver; 
  border-right: 1px solid silver; 
  margin: 0 1px; 
  display: inline-block; 
  width: 1px; 
  height: 10px; 
} 
</style> 
<div id='GDWC_statusBar'> 
<span class='GDWC_statusBarCounter'>
<a href='http://about.me/bruno.skvorc'>GDWC</a>
</span> 
<span class='GDWC_statusBarSeparator'>
</span> 
<span class='GDWC_statusBarCounter' id='GDWC_wordsTotal'>Warming up...</span> 
</div> 
</body> 
</html>
```

正如你所看到的，如果你只是在浏览器中打开文件，它只不过是屏幕底部的一个灰色条，带有一个到 sitepoint.com 的 GDWC 链接和文本“正在热身…”。这个文本将通过 JavaScript 替换为单词 count，这就是我们下一个文件 **main.js** 出现的地方。

首先，将 **jq.js** 文件下载到扩展文件夹中(同样，下载的链接出现在文章的末尾)，或者直接去 jquery.com[下载一个新的。接下来，创建文件 **main.js** 并粘贴以下内容:](http://jquery.com/)

```
$.get(chrome.extension.getURL("statusbar.html"), {}, function(data) {$('body').append(data);}, 'html');
```

您可能会意识到这是对本地文件的 Ajax 调用。这一行告诉扩展获取 statusbar.html 文件的内容，并将其注入到文档的结束 body 标签之前。因为 CSS 在。html 文件规定了状态栏在页面上的位置(固定的，在所有东西的上面，在底部)，这基本上从视觉的角度完成了状态栏的实现。你现在可以试一试，看看它看起来怎么样，方法是进入工具->扩展，然后选择“加载一个未打包的扩展”。选择 GDWC 文件夹，点击“OK ”,刷新一个新的 Google 文档来看看它的运行。

现在让我们添加更多的代码。将以下内容追加到 main.js 中:

```
chrome.extension.sendRequest({}, function(response) {});
```

这一行调用 background.js 脚本并告诉它显示图标。在工具->扩展中点击加载的扩展下的“Reload ”,然后刷新你正在测试的标签。图标应该出现在地址栏中。

现在我们需要实际计算单词，所以添加以下代码:

```
$(document).ready(function(){
	countWords();
});

function countWords() {
	var number = 0;
	$('span.kix-lineview-text-block').each(function(i, obj){
		number += $(obj).text().split(/s+/).length;
	});
	$('span#GDWC_wordsTotal').text(number + ' total words');
	timeout = setTimeout('countWords()', 5000);
}
```

如您所见，我们已经告诉文档在 DOM 准备好之后运行 countWords 函数。我会使用按键事件来统计文档内容的每次变化的字数，但是 Google 劫持了所有默认事件(使用 event.preventDefault ),这是一种不容易规避的行为。因此，我选择了超时，告诉 countWords()基本上每 5 秒重新运行一次(参见 countWords()函数的最后一行)。

然后，我们初始化一个局部变量编号，并在 kix-lineview-text-block 范围内循环。如果您检查 Google Docs 站点的元素，您会注意到这些范围是文档中的实际行——每个范围都有文档的宽度，并且每个新行都包含在另一个 kix-lineview-text-block 范围内。我们将每个跨度中的单词数(我们使用正则表达式拆分来计算单词数)加到总数中。您可能想知道为什么我们不直接获取整个文档的文本并将其拆分。因为每一行文本都是一个新的跨度，所以获取整个文档的总文本()将会连接这些行。因此，如果一行以“word”结尾，而下一行以“count”开头，那么实际上得到的“wordcount”是一个单词，因此可以用整个文档中的行数来抵消正确的单词总数。

最后，我们用实际的字数来代替“正在热身…”的信息。

如果你现在重新加载扩展，你会注意到你已经构建了一个全功能的 Chrome 扩展。要将其分发给其他人，只需压缩文件夹并四处发送即可。要把它放在 Chrome 网上商店(请不要这么做)，请参考[官方指南中的收尾工作](https://developers.google.com/chrome/web-store/docs/get_started_simple#step5.)。

## 结论

如你所见，为 Chrome 创建扩展非常简单。我们在这里构建的东西既没有生产价值(循环计数会降低处理非常大的文件的工作流的速度)，也没有优化(没有 jQuery 也可以完成所有这些工作，减少一些文件大小，计数应该一页一页地进行，只从当前聚焦的页面开始重新计算)，但它是对使扩展成功的组件的可靠介绍。在本系列的下一部分，我们将研究一些必要的优化，并添加一些更多的功能。

完整的源代码和相关图片可以从 github 下载。

## 分享这篇文章