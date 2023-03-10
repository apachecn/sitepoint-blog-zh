# 构建自己的 Chrome 扩展——第 3 部分

> 原文：<https://www.sitepoint.com/build-your-own-chrome-extension-a-google-documents-word-count-tool-part-3/>

大家好，欢迎来到从头开始构建完整的 Google Chrome 扩展的第三部分，也是最后一部分。概括地说，在本系列的第一部分中，我们构建了一个 Chrome 扩展，粗略地统计了当前处于焦点中的 Google 文档中的单词，并且是通过 jQuery 完成的。它在屏幕底部增加了一个状态栏，并在那里显示计数。

在[第二部分](https://www.sitepoint.com/build-your-own-chrome-extension-a-google-documents-word-count-tool-part-2/ "Second part")中，我们显著升级了扩展——我们增加了单词计数器的精度，在单词计数器旁边增加了页面计数器，根据 Chrome 的要求将清单更新到最新版本，最重要的是，移除了 jQuery，支持普通 JavaScript，以在性能和资源消耗方面获利。

在这一部分中，我们将通过添加一个选项页来完成我们的扩展，这个选项页可以通过 Extensions 选项卡来访问，并且可以用来配置我们希望我们的扩展拥有的能力。

## 添加选项页面

扩展的选项页面基本上只是一个 HTML 文档，其中(通常)包含一些表单元素。现在，我们可以手动构建一个选项页面，并加入一些元素，但我从来都不是一个重新发明轮子的粉丝，已经有一个惊人的包做了我们想要的事情——它提供了一个默认的设置页面，看起来和感觉都像 Chrome 的原生设置页面。请下载[Fancy settings](https://github.com/frankkohlhepp/fancy-settings/wiki/ "Second part")并解压到你的扩展文件夹中，命名为“fancy-settings”。

为了让我们的扩展加载一个选项页面，我们需要在清单文件中添加以下内容:

```
 "options_page": "fancy-settings/source/index.html" 
```

这告诉扩展选项页面将从`fancy-settings/source/index.html`加载，这是我们解压下载的花式设置代码的地方。当我们编辑`manifest.json`文件时，让我们把版本提升到 0.3。

要查看这个选项页面的运行情况(尽管它现在没有任何用处)，请重新加载扩展(按照[第一部分](https://www.sitepoint.com/build-your-own-chrome-extension-a-google-documents-word-count-tool/ "First part")中的说明)并单击它旁边的“选项”(它应该在“允许隐姓埋名”旁边)。这将打开一个新的选项卡，其中包含一些非常漂亮的表单元素，供我们编辑和调整。

## 编辑选项页面

我们将添加以下简单的选项:我们将让用户选择三个视觉主题中的一个，我们将让用户关闭单词和页面计数器，我们将让用户从状态栏中删除 GDWC 链接。

对于第一个，我们需要一组有三个值的单选按钮。对于第二个，我们需要一组复选框。对于第三个，我们需要一个单独的复选框。这些都可以通过奇特的设置来构建，所以我们将使用它们的语法。要找到更多你可以使用的选项，请阅读他们的文档页面。

为了添加我们需要的元素，我们必须编辑选项页面本身的清单。用以下内容替换`fancy-settings/source/manifest.js`的内容:

```
 window.manifest = {
"name": "GDWC",
"icon": "../../icon48.png",
"settings": [
{
"tab": "Main settings",
"group": "Visual Settings",
"name": "themepicker",
"type": "radioGroup",
"label": "Choose theme (default is light):",
"options": [
["light", "Light"],
["dark", "Dark"],
["terminal", "Terminal"],
],
"default": "light"
},
{
"tab": "Main settings",
"group": "Counter settings",
"name": "pagecounter",
"type": "checkbox",
"label": "Enable Page Counter",
"default": true
},
{
"tab": "Main settings",
"group": "Counter settings",
"name": "wordcounter",
"type": "checkbox",
"label": "Enable Word Counter",
"default": true
},
{
"tab": "Main settings",
"group": "Other options",
"name": "gdwclink",
"type": "checkbox",
"label": "Show GDWC Sitepoint link?",
"default": true
}
]
}; 
```

就这么简单。花式设置选项页面现在将自动构建所有需要的元素。请重新加载扩展并单击选项来查看它的运行。

## 读取默认和保存的设置

花式设置自带`store.js`，帮助你读取一个扩展的设置。为此，我们需要做一些改变。第一个变化是我们的清单文件。变化

```
 "background" : { "scripts": ["background.js"] } 
```

到

```
 "background" : { "page": "background.html" } 
```

接下来，在扩展的文件夹中创建一个`background.html`页面，并粘贴以下内容:

```
 <script type="text/javascript" src="fancy-settings/source/lib/store.js"></script>
<script type="text/javascript" src="background.js"></script> 
```

我们所做的是将我们的背景页面从 JS 改为 HTML，这样我们也可以在后台加载 HTML 内容和其他脚本。在这个 HTML 页面中，我们简单地包含了我们希望在扩展启动时加载的所有 JS 脚本(在我们的例子中，来自 fancy-settings 的`store.js`和来自之前的我们自己的`background.js`)。既然`store.js`已经激活并加载，我们可以在背景页面中使用它了。

打开`background.js`，在文件的最顶端，粘贴以下内容:

```
 var settings = new Store("settings", {
"themepicker": 'light',
"pagecounter": true,
"wordcounter": true,
"gdwclink": true
}); 
```

这将从客户端计算机的本地存储中获取设置，如果还没有设置，则设置默认值。但是，我们需要能够通过我们的内容脚本`main.js`访问这些设置。还记得第一部分中对后台脚本的调用吗？

```
 chrome.extension.sendRequest({}, function(response) {}); 
```

只是为了显示扩展图标？让我们把它用在更好的地方。

在`background.js`中，更改:

```
 sendResponse({}); 
```

到

```
 sendResponse(settings.toObject()); 
```

这将把检索到的设置发送回`main.js`脚本。现在打开`main.js`，改变:

```
 chrome.extension.sendRequest({}, function(response) {}); 
```

到

```
 var options;
chrome.extension.sendRequest({}, function(response) {options = response;}); 
```

这意味着 sendRequest 调用不仅显示图标，现在还获取设置并用接收到的响应填充新添加的 options 变量。我们现在在`main.js`脚本中有了可用的选项。然而，如果我们试图在 sendRequest 回调之外执行我们的主脚本，并且仍然在回调之内设置`options = response`,那么外部代码的执行速度将比回调中的代码稍快，因此抱怨“options”变量尚未设置。JavaScript 的异步特性在这里需要仔细考虑，因此为了避免这些问题，我们将现有代码:

```
 var readyStateCheckInterval = setInterval(function() {
if (document.readyState === "complete") {
countWords();
clearInterval(readyStateCheckInterval);
}
}, 10); 
```

在...之后

```
 options = response; 
```

但仍在回调内部，因此整个 sendRequest 回调如下所示:

```
 var options;
chrome.extension.sendRequest({}, function(response) {
options = response;
var readyStateCheckInterval = setInterval(function() {
if (document.readyState === "complete") {
countWords();
clearInterval(readyStateCheckInterval);
}
}, 10);
}); 
```

这一改变确保了我们的扩展调用后台脚本并在之前获取选项**，从而确保选项对扩展的所有后续部分可用。从积极的方面来看，所有这些也通过`Store.js`库自动处理了我们的选项保存。**

## 使用设置操作状态栏

现在，我们的设置可以保存和加载，我们已经拥有了所有必要的元素，让我们实际添加一些功能。在`main.js`中 sendRequest 回调的末尾，添加以下代码:

```
 var gdwclink = document.getElementById('gdwclink');
if (options.gdwclink) {
gdwclink.style.display = 'inline-block';
} else {
gdwclink.style.display = 'none';
} 
```

这段代码获取 GDWC SitePoint 链接，检查它是否应该可见的选项，并相应地采取行动。我们在 statusbar 中实际上没有 gdwclink ID，所以让我们用这样的 ID 将链接和它后面的分隔符包装在一个 span 中。打开`statusbar.html`并更改:

```
 <div id='GDWC_statusBar'>
<span class='GDWC_statusBarCounter'><a href='http://www.sitepoint.com'>GDWC</a></span>
<span class='GDWC_statusBarSeparator'></span>
<span class='GDWC_statusBarCounter' id='GDWC_wordsTotal'>Warming up...</span>
</div> 
```

对此:

```
 <div id='GDWC_statusBar'>
<span id="gdwclink" style='inline-block'>
<span class='GDWC_statusBarCounter'><a href='http://www.sitepoint.com'>GDWC</a></span>
<span class='GDWC_statusBarSeparator'></span>
</span>
<span class='GDWC_statusBarCounter' id='GDWC_wordsTotal'>Warming up...</span>
</div> 
```

这应该可以。让我们现在做柜台选项。用以下内容替换`main.js`中的整个`countwords()`功能:

```
 var wordCount = 0;
var pageCount = 0;

var message = '';

if (options.pagecounter) {
var divs = document.getElementsByTagName('div'), i;
for (i in divs) {
if((" " + divs[i].className + " ").indexOf(" kix-page ") > -1) {
pageCount++;
}
}
message += pageCount + ' pages';
}

if (options.wordcounter) {
var spans = document.getElementsByTagName('span'), i;
for (i in spans) {
if((" " + spans[i].className + " ").indexOf(" kix-lineview-text-block ") > -1) {
var words = spans[i].innerText.replace(/W+/g, ' ').match(/S+/g);
wordCount += words && words.length || 0;
}
}
if (message != '') {message += ', ';}
message += wordCount + ' total words';
}

document.getElementById('GDWC_wordsTotal').innerText = message;

timeout = setTimeout('countWords()', 5000); 
```

如您所见，我们在每个计数器前添加了一些 if 检查，并创建了一个变量来存储状态栏中显示的消息。此消息的内容取决于我们选择的选项，即我们是否希望看到页数、字数、无或两者都要。

最后，让我们关注主题。我们选择了“光”、“暗”和“终端”作为我们的选项。假设在 CSS 格式中，它们的意思如下:

```
 div#GDWC_statusBar.light {
background-color: #ebebeb;
border-top: 1px solid silver;
color: black;
}

div#GDWC_statusBar.dark {
background-color: darkgrey;
border-top: 1px solid #2d2d2d;
color: orange;
}

div#GDWC_statusBar.terminal {
background-color: #000;
border-top: 1px solid #2d2d2d;
color: green;
} 
```

您已经可以看到我们正在进行的事情——我们将强制选择的选项成为状态栏的类。用以下代码替换`statusbar.html`的整个样式块:

```
 <style type="text/css">
div#GDWC_statusBar.light {
background-color: #ebebeb;
border-top: 1px solid silver;
color: black;
}
div#GDWC_statusBar.light span.GDWC_statusBarCounter a {
color: black;
}

div#GDWC_statusBar.dark {
background-color: #333;
border-top: 1px solid #2d2d2d;
color: orange;
}
div#GDWC_statusBar.dark span.GDWC_statusBarCounter a {
color: orange;
}

div#GDWC_statusBar.terminal {
background-color: #000;
border-top: 1px solid #2d2d2d;
color: green;
}
div#GDWC_statusBar.terminal span.GDWC_statusBarCounter a {
color: green;
}

div#GDWC_statusBar {
width: 100%;
height: 18px;
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
```

如果你现在重新加载，状态栏将会是透明的，看起来很别扭。我们需要首先在它上面强制使用默认主题。在 sendRequest 回调的末尾，紧接着 gdwc 显示/隐藏逻辑之后，添加以下内容:

```
 var statusbar = document.getElementById('GDWC_statusBar');
statusbar.className = options.themepicker; 
```

就是这样。现在重新加载扩展，以及测试它的 Google 文档。没有变化，对吗？现在进入我们的扩展选项，尝试选择一个选项，然后刷新您正在测试的文档，以查看更改是否生效。如果你愿意的话，试着添加一些你自己的选项。

不幸的是，我们需要重新加载扩展来使我们选择的选项生效。虽然根据新设置动态更新扩展并不太复杂，但由于其长度和级别，这超出了本文的范围。无论如何，如果您自己想这么做，请允许我为您指出正确的方向——所有有趣的设置元素都可以有事件侦听器，因此为了做到这一点，您必须侦听我们使用的元素上的更改事件，并访问扩展的所有实例以编辑状态栏的内容。更多信息可以在上面链接的 Fancy Settings wiki 页面和谷歌开发者指南中找到。

## 结论

在这个系列的最后一部分，我们进一步升级了我们的扩展，增加了一个选项页面，类似于 Chrome 的原生设置页面，我们增加了选择主题、停用某些功能以及隐藏状态栏上的一些元素的能力。我希望对选项页的介绍足以吊起你的胃口，并鼓励你自己去尝试。Chrome 扩展的世界是一个很大的世界，有一个高质量、创新的扩展来增强工作流程。谁知道呢？经过一些实践，你甚至可能最终做出一个商业上可行的产品。

你可以从 github 下载这个 [Chrome 扩展教程](https://github.com/spposts/chrome_wordcount_extension_part3/zipball/master)的源代码。祝你的 Chrome 实验好运！

## 分享这篇文章