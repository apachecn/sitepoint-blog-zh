# 新的 W3C 网站上线…使用无效的 CSS！

> 原文：<https://www.sitepoint.com/new-w3c-website-invalid/>

继四月份的[反馈请求之后，](https://www.sitepoint.com/w3c-redesign-feedback/) [W3C 终于在 10 月 13 日发布了他们闪亮的新网站](https://www.w3.org/)。

万维网联盟(W3C)是一个由组织、全职员工和公众成员组成的国际社区，他们共同开发 web 标准和技术规范(HTML、XML、CSS、DOM、RDFa、SOAP、SVG、语义 Web 等)。).直到最近，那些有礼貌的人还会把网站描述为*功能性*，而不是可用性或吸引力。W3C 希望重新设计能使网站更容易导航，使用起来更舒适。

新网站无疑是一个进步。这个设计更清晰，更有吸引力，可以在所有现代浏览器上运行(包括 IE6)。使用灵活的布局，当浏览器视窗宽度减少到 481 像素以下时，该布局切换到“移动视图”单列。这是一个很好的尝试，尽管我不相信很多手机用户有访问这个网站的迫切需要？

内容一如既往地全面，尽管很明显有些页面不完整。我访问的第一个部分是[浏览器](https://www.w3.org/standards/agents/browsers)和[创作工具](https://www.w3.org/standards/agents/authoring.html)——开发者对这些非常感兴趣，但是没有可用的内容？可以理解的是，网站和规范会不断发展，但是为什么要提供一个指向缺失内容的主页链接呢？

在幕后，该网站始终使用 XHTML 1.0 strict，我测试的每个页面都通过了验证。具有讽刺意味的是，[CSS 不验证](http://jigsaw.w3.org/css-validator/validator?profile=css21&warning=0&uri=http%3A%2F%2Fwww.w3.org%2F)主要是因为大量的 IE6 攻击。该网站也没有通过自动辅助功能验证:表单控件缺少标签，存在链接问题，并且没有使用描述性元标记。

页面顶部的“view: desktop，mobile，print”控件由 JavaScript 支持。不幸的是，它缺少[渐进式增强](https://www.sitepoint.com/progressive-enhancement-1-html/):如果 JavaScript 在你的浏览器中被禁用或不可用，该控件永远不会出现。这让我觉得有点奇怪:一个有益于移动用户的控件可能会在许多移动设备上失效？

视图控件是我唯一能找到的 JavaScript 功能。那么，为什么网站需要完整的 jQuery 库和一个 cookie 插件呢？我并不反对 jQuery，但是可以开发一个更好的独立小部件代码，它只是下载大小的一小部分，并且支持更多的浏览器。

也许我有点过于挑剔了，但是 W3C 不应该以身作则吗？我喜欢他们的新网站，但如果开发人员遵循他们发布的指南，情况会更好。

你认为新的 W3C 网站怎么样？他们应该解决验证和可访问性问题吗？或者他们只是在使用许多 web 开发人员日常使用的技巧和快捷方式？

链接:

*   [https://www.w3.org/](https://www.w3.org/)
*   [W3C 主页 XHTML 1.0 验证报告](http://validator.w3.org/check?verbose=1&uri=http%3A%2F%2Fwww.w3.org%2F)
*   [W3C 主页 CSS 验证报告](http://jigsaw.w3.org/css-validator/validator?profile=css21&warning=0&uri=http%3A%2F%2Fwww.w3.org%2F)
*   渐进式增强技术: [HTML](https://www.sitepoint.com/progressive-enhancement-1-html/) 、 [CSS](https://www.sitepoint.com/progressive-enhancement-2-css/) 和 [JavaScript](https://www.sitepoint.com/progressive-enhancement-3-javascript/)
*   [W3C 想要你的反馈](https://www.sitepoint.com/w3c-redesign-feedback/)

## 分享这篇文章