# Chrome 25 有什么新功能

> 原文：<https://www.sitepoint.com/chrome-25-whats-new/>

每六周更新一次的 Chrome 和 Firefox 的更新似乎在几天内就同步了。但你没注意到也情有可原。也就是说，除了 22 个被堵住的安全漏洞，Chrome 25 中还有许多新功能会让各地的开发者兴奋不已…

## 网络语音 API

有没有一种似曾相识的感觉？我并不感到惊讶— [Webkit 在差不多两年前添加了语音识别功能](https://www.sitepoint.com/html5-speech-input-fields/)，它出现在 Chrome 11 中。然而，它相当基础，没有实现 [W3C 语音 API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html) 。这提供了更多的控制，因此您可以对语音事件做出反应，并相应地处理短语，例如

```
if (webkitSpeechRecognition) {
	var recognition = new webkitSpeechRecognition();
	recognition.continuous = true;
	recognition.interimResults = true;

	// speech events
	recognition.onstart = function() { ... };
	recognition.onresult = function(event) { ... };
	recognition.onerror = function(event) { ... };
	recognition.onend = function() { ... };
} 
```

谷歌已经创建了一个演示页面,展示了一个基本的文本输入框，但预计很快就会看到用于控制网络应用和游戏的语音驱动技术。更多信息，请参考 [W3C 语音 API 规范](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html)和 HTML5Rocks 的[介绍教程。](http://updates.html5rocks.com/2013/01/Voice-Driven-Web-Apps-Introduction-to-the-Web-Speech-API)

## 邪恶延伸根除

你有忘记安装的 Chrome 扩展吗？谷歌*有益地*提供了[设施和文档](http://developer.chrome.com/extensions/external_extensions.html)，允许任何人在没有你的同意下悄悄地安装一个扩展。这种令人讨厌的行为在 Chrome 25 中已经被删除，浏览器现在实现了类似 Firefox 的插件检测。

## CSS 媒体覆盖

现在，您可以在开发人员工具中模拟不同的媒体设备—单击“设置”按钮，然后单击“覆盖”。该工具允许您使用打印预览或其他复杂的方法测试打印和其他样式表，而无需更改代码:

![Chrome Overrides](img/ad8f926981250f33603118b6dd4c48dc.png)

当您在开发工具中时，尝试使用新的`console.clear()`方法来清理日志。

## Windows 应用启动器

谷歌已经将这一 Chrome OS 功能移植到了 Windows 上。该启动器是实验性的，必须通过在 [chrome://flags/](//flags/) 中设置 **Show Chrome Apps Launcher** 来启用。重启 Chrome，你会在任务栏上看到一个新的——相当难看的——图标:

![Chrome App Launcher](img/23c0c627c379d8ac0a910c6b6c53ddae.png)

我并不完全相信 App Launcher 启动 Chrome 并从应用程序屏幕中选择一个图标同样简单。不过，某个地方的某个人会喜欢它。

## 完整历史同步

现在可以在设备之间同步您的整个浏览历史，而不仅仅是在地址栏中输入的 URL。这是另一个实验性的功能——在 [chrome://flags/](//flags/) 中设置**启用全历史**同步。

Chrome 已经走过了漫长的道路，很难相信这款浏览器还不到五年。版本 25 中有一些不错的新特性，但是也有一些可疑的新特性潜入其中。保持干净，谷歌！

## 分享这篇文章