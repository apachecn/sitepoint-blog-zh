# 创建文本到语音的 Chrome 扩展

> 原文：<https://www.sitepoint.com/create-text-to-speech-chrome-extension/>

这篇文章由马克·托勒同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

将文本转换为语音，也称为语音合成或 TTS(文本到语音)，是一种产生人类语音的人工方法。这并不是什么新鲜事，根据维基百科的说法，至少一千年前人们就已经试图创造出能产生人类语言的机器。

今天，TTS 在我们的生活中越来越普遍，每个人都可以利用它。我们将通过创建一个将文本转换为语音的 Chrome 扩展来演示这一点。HTML5 为我们带来了[语音合成 API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html) ，它允许任何 Web 应用程序将任意文本字符串转换成语音，并免费播放给用户。

Chrome 的扩展通常包括以下内容:

1.  清单(包含元数据的必需文件)
2.  图像(如扩展的图标)
3.  HTML 文件(比如当用户点击扩展图标时显示的弹出窗口)
4.  JavaScript 文件(比如内容和/或后台脚本，稍后会解释)
5.  您的应用程序可能使用的任何其他资产(如样式表)

## 关于页面到语音扩展

由于 Chrome 的流行和 TTS 的兴起，我们将创建一个 Chrome 扩展，将文本转换为语音。该扩展将一直等待，直到用户点击它的图标或按下一个特殊的热键(`shift + Y`)，然后它将试图找到用户在当前查看的页面上高亮显示的内容，或者试图找到复制到他们剪贴板上的内容。如果有任何东西，它将通过首先尝试使用 HTML5 语音合成 API 将其转换为语音，如果不可用，则通过调用第三方 API。

## Chrome 扩展基础

每个 Chrome 扩展都需要有一个名为 [manifest.json](https://developer.chrome.com/apps/manifest) 的文件。清单是一个 JSON 格式的文件，其中包含对应用程序至关重要的数据，从扩展的名称、描述、图标和作者到定义扩展需求的数据——扩展应该能够在哪些网站上运行(这些是用户必须授予的权限)或者当用户浏览特定网站时应该运行哪些文件。

```
{
  "manifest_version": 2,

  "name": "Page to Speech",
  "description": "This extension will produce English speech to whatever text you highlight on a webpage.Highlight text and click the extension's icon",
  "author": "Ivan Dimov",
  "version": "1.0",
  "icons": { 
    "16": "icon16.png",
    "48": "icon48.png",
    "128": "icon128.png"
  }, 
```

我们的清单从记录扩展的名称、描述、作者、版本和图标开始。您可以在`icons`对象中提供许多不同大小的图标。

```
 "background": {
    "scripts": ["background.min.js"]
  },
  "content_scripts": [
    {
      "matches": ["http://*/*", "https://*/*"],
      "js": [ "polyfill.min.js", "ext.min.js"],
      "run_at": "document_end"
    }], 
```

然后，我们在`background`对象中定义了一个名为`background.min.js`(注意，我们使用了缩小的文件)的后台脚本。[后台脚本](https://developer.chrome.com/extensions/background_pages)是长期运行的脚本，将持续运行直到用户的浏览器被关闭或扩展被禁用。

之后，由于通配符`"http://*/*"`和`"https://*/*"`，我们有一个`content_scripts`数组指示 Chrome 在每个网站请求上加载两个 JavaScript 文件。[与后台脚本不同，内容脚本](https://developer.chrome.com/extensions/content_scripts)可以访问用户正在访问的实际网站的 DOM。内容脚本可以读取和修改它们所嵌入的任何网页的 DOM。因此，我们的`polyfill.min.js`和`ext.min.js`将能够读取和修改每个网页上的所有数据。

```
 "browser_action": {
    "default_icon": "speech.png"
  },
   "permissions": [
     "activeTab",
     "clipboardRead"
    ]
} 
```

没那么快！我们有另一个名为`permissions`的数组，我们用它请求只访问用户当前打开的网页(活动标签)。我们还请求另一个名为`clipboardRead`的许可，它将允许我们读取用户的剪贴板(这样我们就可以将其内容转换成语音)。

## 编码页面到语音的 Chrome 扩展

首先，我们创建了唯一的后台脚本，它连接了一个事件侦听器，当用户单击扩展的图标时，该事件侦听器将被触发。当这种情况发生时，我们调用`sendMessage`函数，该函数在`chrome.tabs.sendMessage(tabId, message, callback)`方法的帮助下向我们的内容脚本发送一条消息(内容脚本可以读取 DOM 并找出用户高亮显示的内容或/和用户放在剪贴板上的内容)。我们将消息发送到当前打开的选项卡——因为这是我们感兴趣的，也是我们可以访问的——借助于`chrome.tabs.query`方法，该方法的参数涉及一个回调函数，调用该函数的参数包含与查询匹配的选项卡。

```
chrome.browserAction.onClicked.addListener(function (tab) {
    //fired when the user clicks on the ext's icon
    sendMessage();
});
function sendMessage() {
  chrome.tabs.query({active: true, currentWindow: true}, function(tabs){
    chrome.tabs.sendMessage(tabs[0].id, {action: "pageToSpeech"}, function(response) {});
  });
} 
```

现在，较长的部分是我们的内容脚本。我们创建一个对象，它将保存扩展所涉及的一些数据，然后定义我们的初始化方法。

```
initialize: function() {
    if (!pageToSpeech.hasText()) { return;}
    if (!pageToSpeech.trySpeechSynthesizer()) {
        pageToSpeech.trySpeechApi();
    }
}, 
```

该方法检查用户是否没有突出显示文本，或者他们的剪贴板上是否没有任何内容，并在这种情况下返回。否则，它会尝试使用 HTML5 语音合成 API 生成语音。如果还是失败，它最终会尝试使用第三方 API。

检查文本的方法做了几件事。它试图借助内置的`getSelection()`方法获得一个带有高亮文本的对象，并用`toString()`将其转换成一个文本字符串。然后，如果没有文本突出显示，它会尝试在用户的剪贴板上查找文本。它通过向页面添加一个 input 元素，聚焦它，在`execCommand('paste')`的帮助下触发一个粘贴事件，然后将粘贴的文本保存在一个属性中。然后清空输入。在这两种情况下，它都会返回找到的任何内容。

```
 hasText: function() {
    this.data.highlightedText = window.getSelection().toString();
    if (!this.data.highlightedText) {
      var input = document.createElement("input");
      input.setAttribute("type", "text");
      input.id = "sandbox";
      document.getElementsByTagName("body")[0].appendChild(input);
      var sandbox = document.getElementById("sandbox");
      sandbox.value = "";
      sandbox.style.opacity = 0;
      sandbox.focus();
      if (document.execCommand('paste')) {
        this.data.highlightedText = sandbox.value;
      }
      sandbox.value = "";
    }
    return this.data.highlightedText;

  }, 
```

为了让用户能够通过热键(硬编码为`shift + Y`)运行文本到语音的转换，我们初始化了一个数组，并为`onkeydown`和`onkeyup`事件设置了一个事件监听器。在监听器中，我们存储了一个与被按下的键的`keyCode`相对应的索引，该键的值来自事件类型`e.type`与`keydown`的比较，并且是一个布尔值。因此，每当一个键被按下时，相应的键索引值将被设置为`true`，每当一个键被释放时，索引值将被更改为`false`。因此，如果索引 16 和 84 都持有真值，我们知道用户正在使用我们的热键，所以我们初始化文本到语音的转换。

```
 addHotkeys: function() {
    var activeKeys = [];
    onkeydown = onkeyup = function(evt) {
      var e = evt || event;
      activeKeys[e.keyCode] = e.type == 'keydown';
      if (activeKeys[16] && activeKeys[84]) {
        pageToSpeech.initialize();
      }
    };
  } 
```

为了将文本转换成语音，我们依赖于`trySpeechSynthesizer()`方法。如果 HTML5 语音合成存在于用户的浏览器中(`window.speechSynthesis`)，我们知道用户能够使用它，因此我们检查语音当前是否正在运行(我们通过`pageToSpeech.data.speechInProgress`布尔值知道它是否正在运行)。如果正在进行中，我们停止当前的讲话(因为`trySpeechSynthesizer`将开始一个新的讲话，我们不希望两个声音同时出现)。然后，我们将`speechInProgress`设置为`true`，每当语音结束时，再次将属性设置为 falsy 值。

现在，我不想详细说明我们为什么使用`speechUtteranceChunker`，但[是一个与 Chrome 在发出 200-300 个单词后停止语音合成相关的错误修复](http://stackoverflow.com/questions/21947730/chrome-speech-synthesis-with-longer-texts)。基本上，它把我们的文本串分成许多小块(在我们的例子中是 120 个单词),然后用一个接一个的块调用语音合成 API。

```
 trySpeechSynthesizer: function() {
   if (window.speechSynthesis ) {
       //new speech is about to get started
       if (this.data.speechInProgress) {
         polyfills.speechUtteranceChunker.cancel = true;
       }
     this.data.speechInProgress = true;
       var msg = new SpeechSynthesisUtterance(this.data.highlightedText);
       //speechSynthesis.speak(msg);
       // Chrome Implementation BUG: http://stackoverflow.com/questions/21947730/chrome-speech-synthesis-with-longer-texts
       polyfills.speechUtteranceChunker(msg, {
         chunkLength: 120
       },function() {
         //speech has finished
         pageToSpeech.data.speechInProgress = false;
       }); 
```

最后，如果 HTML5 语音合成 API 不可用，我们尝试一个 API。我们有相同的属性，用来知道是否有必要停止一个已经在运行的音频。然后，我们直接创建一个新的`Audio`对象，并将 URL 传递给所需的 API 端点，因为我们选择用于演示的 API 直接传输音频。我们只是把 API 键和要转换的文本传递给它。我们还检查音频是否触发了错误。在这种情况下，我们只需向用户显示一个`alert`，说明此时我们无法提供帮助(这个特定的 API， [Voice RSS](http://www.voicerss.org/) ，允许我们测试代码的自由层上有 300 个请求)。

```
trySpeechApi: function() {
    if (this.data.speechInProgress) {
      this.data.fallbackAudio.pause();
    }
    this.data.speechInProgress = true;
    this.data.fallbackAudio = new Audio("http://api.voicerss.org/?key=your_api_key&src=" + this.data.highlightedText);
    this.data.fallbackAudio.addEventListener("error", function(evt) {
      alert("Sorry, we cannot produce speech right now. Try upgrading your Chrome browser!");
    })
    this.data.fallbackAudio.play();
    this.data.fallbackAudio.onended = function() {
      pageToSpeech.data.speechInProgress = false;
    }

  }, 
```

最后，在任何本地范围之外，我们调用`addHotkeys`方法，它将开始等待用户按下正确的热键，我们设置一个监听器，它将等待直到从后台脚本接收到消息。如果收到正确的消息( *speakHighlight* )或热键被按下，我们将初始化我们的文本到语音转换对象。

```
chrome.extension.onMessage.addListener(function(msg, sender, sendResponse) {
  if (msg.action == 'pageToSpeech') {
    pageToSpeech.initialize();
  }
});
pageToSpeech.addHotkeys(); 
```

## 结论

瞧，我们有一个很好的 Chrome 扩展，可以将文本转换为语音。这里的概念可以用来创建不同用途的 Chrome 扩展。你已经建立了什么有趣的 Chrome 扩展或者你想建立一个吗？请在评论中告诉我！

如果你喜欢这个想法，并想进一步开发它，你可以在我们的 [GitHub 库](https://github.com/sitepoint-editors/page-to-speech)中找到完整的代码。如果你想测试的话，可以在 [Chrome 网上商店](https://chrome.google.com/webstore/detail/page-to-speech/ncillngfchljkeoiaefmncdcgkpogbhh)找到该扩展的生产版本。

参考资料:
https://en.wikipedia.org/wiki/Speech_synthesis#History
http://stack overflow . com/questions/21947730/chrome-speech-synthesis-with-long-texts

## 分享这篇文章