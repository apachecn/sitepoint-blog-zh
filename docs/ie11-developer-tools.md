# IE11 的开发者工具有什么新特性

> 原文：<https://www.sitepoint.com/ie11-developer-tools/>

对于浏览器来说，这是重要的一周。 [Opera 15](https://www.sitepoint.com/opera-15-launched/) 推出， [Chrome 28](https://www.sitepoint.com//chrome-28-whats-new/) 发布， [Firefox 获奖](https://www.sitepoint.com/2013-best-browser-award/)和[放弃 JavaScript 选项](https://www.sitepoint.com/disable-javascript-option/)。所以是时候让宇宙恢复平衡，对 IE 表现出一点爱了！

IE11 的新特性已经广为人知。SPDY 和 WebGL:太好了。更好的触摸控制和 SkyDrive 同步:不错。改进的 tabbing 和 live tiles:都很好。但是我们真正需要的是更好的工具来帮助我们的日常开发… *微软会提供一些吗？*

我从来没有称赞过 IE 的开发者工具。IE6 的第一个版本是对 Firebug 的回应，Firebug 革新了浏览器内测试。工具栏有些仓促和笨拙，但总比没有好。它通过 IE 版本得到了小的改进，但仍然令人失望。开发 VisualStudio 的公司无法与竞争对手提供的浏览器工具相匹敌。

虽然 IE11 还有几个月才会出现，但最新的 F12 开发工具已经被彻底改造了。它们看起来很棒，有一些真正创新的功能，可以超越替代品。

你会注意到的第一个变化是难看的灰色对话框被一个漂亮的现代界面所取代:

[![IE11 F12 Developer Tools](img/96d0d1027c476f58f644081adf3f40bf.png)](https://i.msdn.microsoft.com/dynimg/IC666321.png)

左下方的图标指向:

*   DOM 浏览器
*   消息控制台(也可作为覆盖图使用)
*   JavaScript 调试器
*   网络工具
*   用户界面响应分析器
*   脚本分析器
*   一个内存分析工具
*   测试小屏幕和 GPS 的仿真工具

到目前为止，火虫。但是有一些你在别处不一定能找到的惊人特性…

**IntelliSense** :编辑动态 HTML 属性、CSS 属性和 JavaScript 对象时，自动完成建议可用。

**DOM drag & drop** :节点可以在活树中重新排列和重新定位。

**事件提示**:发现哪些事件处理程序已经应用于一个 DOM 节点。

[![IE11 F12 Developer Tools](img/5ed54518010afe71654c4d46353e2a51.png)](https://i.msdn.microsoft.com/dynimg/IC666326.png)

**代码缩减**:将压缩或缩减后的代码转换成可读性更好的格式。

**执行反转**:不太像时光机，但是可以把执行点后移！

**设置下一条语句**:跳过语句——有点像临时注释掉它们。

**跟踪点**:自动将消息记录到控制台——就像发布`console.log()`一样，而不需要弄乱您的代码。

**在新工作线程上中断**:当调用 web 工作线程时中断执行。

**性能分析**:发现处理器最密集的任务，以及它们如何影响页面帧率。

**内存分析**:获取内存使用的快照，与其他文件进行比较，并确定哪些 DOM 节点或 JavaScript 对象消耗了最多的资源。

也许唯一缺少的功能是一个分析 HTML5 本地存储的工具。但是微软有几周时间来解决这个问题…

尽管 IE 的历史声誉，微软正朝着正确的方向前进。你甚至可以要求 IE11 测试你的下一个 web 项目！

## 分享这篇文章