# 谷歌用 Chrome 框架修复 IE6

> 原文：<https://www.sitepoint.com/google-chrome-frame-fixes-ie/>

Chrome Frame 是谷歌的一款新的开源产品，有望满足网络开发者的梦想。这是一个用于 IE6、IE7 和 IE8 的免费插件，可以将 Internet Explorer 变成谷歌的 Chrome 浏览器！IE6 在很多年前就应该退出公共服务了，但我们仍然在添加愚蠢的 CSS 变通方法和测试 XmlHttpRequest 对象，以使 pages *工作*。Web 开发人员可能会歇斯底里地抱怨 IE 的缺点，但浏览器已经根深蒂固地融入了许多依赖传统 web 应用程序的企业环境中。甚至家庭用户也避免升级和替代浏览器，以保持他们更简单、无标签的网络存在。谷歌分担我们的痛苦。他们致力于 HTML5，而 Google Wave 等产品很可能在 IE 中遭遇惨败。他们的解决方案很优雅——如果不是有点偷偷摸摸的话:

1.  安装 Chrome Frame 的 IE 用户会下载一个 500KB 的小插件。该插件将在后台下载另外 10MB 的 Chrome 代码。
2.  IE 将显示为未被触及。界面没有改变，默认情况下仍然使用微软的 Trident 引擎。
3.  However, any page including the following meta tag will magically transform IE so it uses Chrome’s rendering engine:

    ```
    <meta http-equiv="X-UA-Compatible" content="chrome=1">
    ```

    注意，安装了 chromeframe 的浏览器会将“Chrome Frame”添加到 HTTP 用户代理字符串中。因此，可以检测服务器端的插件，然后插入 meta 标签或重定向到 Chrome 框架下载页面。谷歌还提供了一种基于 JavaScript 的替代方案，提示用户安装该插件。

谷歌软件工程师亚历克斯·罗素进一步解释…

<object width="425" height="344"><param name="movie" value="https://www.youtube.com/v/sjW0Bchdj-w&amp;color1=0xb1b1b1&amp;color2=0xcfcfcf&amp;hl=en&amp;feature=player_embedded&amp;fs=1"><param name="allowFullScreen" value="true"><param name="allowScriptAccess" value="always"><embed src="https://www.youtube.com/v/sjW0Bchdj-w&amp;color1=0xb1b1b1&amp;color2=0xcfcfcf&amp;hl=en&amp;feature=player_embedded&amp;fs=1" type="application/x-shockwave-flash" allowfullscreen="" allowscriptaccess="always" width="425" height="344"></object>

私人用户和公司可以采用 Chrome Frame，而不用废弃或改变他们的任何遗留应用程序。用户不会意识到他们在使用 Chrome，因为 IE 界面保持不变。但是……这行得通吗？Chrome Frame 目前处于测试阶段，所以现在下结论还为时过早。添加一个插件应该比升级或安装一个替代浏览器更容易，但用户会这样做吗？有安全意识的公司可能已经在屏蔽 IE 插件了。Chrome Frame 无疑是一个新颖的解决方案，将有助于浏览器迁移。业界将关注微软的反应 Chrome Frame 是你的网络开发祈祷的答案吗？谷歌惠及的是终端用户还是仅仅是他们自己？下一次 IE 更新会怀疑屏蔽某些插件吗？**链接:**

*   [谷歌 Chrome 框架](https://code.google.com/chrome/chromeframe/)
*   [开发者指南](https://code.google.com/chrome/chromeframe/developers_guide.html)

**相关阅读:**

*   [谷歌 Chrome 框架:技术细节](https://www.sitepoint.com/google-chrome-frame-technical-details/)
*   [为什么企业不升级 IE6，Chrome Frame 能帮上什么忙](https://www.sitepoint.com/ie6-upgrades-google-chrome-frame/)
*   [微软通过慈善捐赠鼓励 IE6 升级](https://www.sitepoint.com/microsoft-ie6-charity-donations/)
*   索尼将在新电脑上提供谷歌浏览器
*   [谷歌揭示 Chrome 3 测试版](https://www.sitepoint.com/google-reveal-chrome-3-beta/)

## 分享这篇文章