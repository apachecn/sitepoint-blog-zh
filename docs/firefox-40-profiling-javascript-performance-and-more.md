# Firefox 40:剖析 JavaScript 性能等等

> 原文：<https://www.sitepoint.com/firefox-40-profiling-javascript-performance-and-more/>

Firefox 40 带有一些强大的新工具和有趣的战略转变。请继续阅读火狐最大的变化以及它们对你的意义。

## 新的绩效工具

Firefox 40 具有新的分析工具，让开发人员从性能角度更好地了解他们的应用程序中发生了什么。开发人员可以使用这些工具来分析任何类型的网站、应用程序或游戏的性能。

“性能”标签包含新的时间线，包括三个视图:

*   一个[瀑布视图](https://developer.mozilla.org/en-US/docs/Tools/Performance/Waterfall)，让你深入了解你的应用在浏览器中的表现:![waterfall performance](img/f412c97fb762c6ae4cc66d4dafbbab42.png)

*   [调用树形视图](https://developer.mozilla.org/en-US/docs/Tools/Performance/Call_Tree)，突出显示任何 JavaScript 性能瓶颈:![call tree js performance](img/f83fcd917651d50d3e704b7184259d9f.png)

*   [火焰图视图](https://developer.mozilla.org/en-US/docs/Tools/Performance/Flame_Chart)，显示性能分析期间每毫秒 JavaScript 堆栈的状态:![flame chart view](img/b16cbe5eb59bb6b630ff1a6a5d14266f.png)

Mozilla Hacks 制作了一个介绍新性能工具的视频:

[youtube WBmttwfA_k8]

还有一个演示如何使用新的性能工具优化 HTML5 游戏中的 JavaScript。

请阅读 Mozilla Hacks 文章,了解新性能工具的更多细节。

## 关于 Windows 10 的一切

Mozilla 为微软的新操作系统整合了全新的优化。这些变化主要是视觉上的，但也有细微的性能增强。

Mozilla [称](https://blog.mozilla.org/blog/2015/08/11/firefox-brings-fresh-new-look-to-windows-10-and-makes-add-ons-safer/)已经做出了

> 精心调整界面，让火狐有一种流线型的感觉…[拥有]更大、更大胆的设计元素以及更多浏览网页的空间。

Windows 10 上的 Firefox 39 看起来是这样的:

![Firefox 39 on Windows 10](img/ebdb908a94447650247bda5e60b28094.png)

Windows 10 上的 Firefox 40 则截然不同:

![Firefox 40 on Windows 10](img/97c857e878d0c876e448fb8a715c9c49.png)

火狐 40 还提高了 Windows 10 的稳定性——Mozilla 测试表明，它在 Windows 8.1 上从每 2 天 1 次崩溃下降到 Windows 10 上 16 天 0 次崩溃。

### 默认浏览器和搜索

关于 Windows 10 的争议让用户很难放弃 Edge 作为默认浏览器，放弃 Bing 作为默认搜索引擎。

Firefox 40 让用户更容易选择自己喜欢的搜索引擎。为了利用这一点，导航到**设置(选项)** > **搜索**来改变 Firefox 和 Windows 中的搜索。但是，请注意，只有当 Firefox 是您的默认 web 浏览器时，您才能更改桌面搜索首选项。

![changing search preferences](img/de3fbff902af42518d647807e050aba2.png)

如果你想在 Windows 10 中恢复火狐浏览器为默认浏览器，Mozilla 已经提供了清晰的指示。

## 扩展的恶意软件防护

Firefox 40 现在扩展了恶意软件检测功能。谷歌安全浏览服务的最新发展使这成为可能——允许火狐识别其支持的平台上的恶意软件下载，并警告用户可能不想要的软件。

这是在 Firefox 39 中引入的，它包括将恶意文件下载的监控扩展到 Mac 和 Linux 版本的 Firefox。

> 当下载通常包含 Windows 或 Mac 可执行代码的文件类型时(例如，com，。exe，。微星，。app，。dmg)，Firefox 通过向 Google 的安全浏览服务发送一些下载的元数据(文件类型、名称、大小、哈希、URL、区域设置)来询问该文件是否安全。如果该文件被此服务标记为有害文件，下载管理器将阻止对该文件的访问，直到用户执行右键单击，然后手动解除阻止。

如果你不想向谷歌发送数据，你可以[关闭功能](https://support.mozilla.org/en-US/kb/how-does-phishing-and-malware-protection-work#w_how-do-i-use-the-phishing-and-malware-protection-features)。

除此之外，如果用户访问包含欺骗性软件的页面，Firefox 40 现在会发出警告，这些软件会对你的电脑进行不良更改。如果用户遇到这样的页面，将显示以下警告:

![warning message](img/29540d904b71c708229b104f14ef4f8d.png)

## 附加签名

有了 Firefox 40，以分散方式分发插件的时代就结束了。不幸的是，这种情况已经没有多少改变的余地了，因为安全和隐私似乎是 Mozilla 优先考虑的问题。

Mozilla 在二月份宣布将在 Firefox 中引入附加签名，为用户提供更安全的附加体验。Mozilla 的插件开发者关系主管 Jorge Villalobos 解释道:

> Mozilla 插件平台传统上对开发者非常开放。不仅扩展能够以激进和创新的方式改变 Firefox，而且开发者可以完全自由地在自己的网站上发布它们，不一定要通过 Mozilla 的附加网站 AMO。这给了开发者很大的权力和灵活性，但也给了坏演员太多的自由来利用我们的用户。

在 Firefox 40 中，如果你使用未签名的插件，你会看到一个警告。然而，在 Firefox 41 中，未签名的插件将被禁用，除非你转到`about:config`并禁用`xpinstall.signatures.required`，否则你将无法启用它们。该计划是在 Firefox 42 中禁用后者，之后将无法覆盖该设置。对此有很多批评，但似乎 Mozilla 已经做出了决定。

Mozilla 提供了一个 [FAQ](https://wiki.mozilla.org/Addons/Extension_Signing) ，其中包含了更多细节。此外，如果你是一个插件开发者，你可能想阅读 Mozilla 开发者网络上的[指南。](https://developer.mozilla.org/en-US/Add-ons/Add-on_guidelines)

## 火狐 40 的其他显著变化

*   Firefox Hello 现在允许向对话添加链接，以提供关于对话内容的上下文(基本上是指定一个主题)。
*   GNU/Linux 中的脱离主线程合成极大地提高了滚动、图形和视频播放性能。
*   多亏了 VSync，Windows 中的动画和滚动现在更流畅了。
*   现在有了一个新的页面标尺高亮工具，可以在页面上显示轻量级的水平和垂直标尺。
*   检查器允许搜索页面中的所有内容框架。

要获得更完整的变化列表，请查看 Firefox 40 发布说明。

## 包裹

你可以通过耐心等待自动更新、在浏览器菜单中选择**关于火狐**或者从 Firefox.com[手动下载新的安装程序来更新你的火狐安装。](https://firefox.com/)

你觉得火狐 40 怎么样？你对附加签名问题有什么看法？新功能是否提升了您的体验？尤其是如果你正在使用 Windows 10，请在评论中告诉我们你的想法！

## 分享这篇文章