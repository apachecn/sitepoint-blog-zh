# IE8 版本定位:微软考虑反馈

> 原文：<https://www.sitepoint.com/ie8-version-targeting-microsoft-considers-feedback/>

在 Tech Times #183 中，我报道了微软在 IE8 中支持浏览器版本定位的计划，I E8 将默认呈现与 IE7 相同的网页，除非微软新提出的`<meta>`标签指示浏览器以浏览器新的“超级标准”模式呈现页面。

如前所述，这些计划在 web 标准社区中引发了一场辩论。虽然 Eric Myer 和 Jeffrey Zeldman 等一些领导者已经开始接受微软的方法，但 Jeremy Keith 等许多受人尊敬的声音仍在叫嚣，特别是对微软的计划，即除非 HTML 4.x 文档包含新的`X-UA-Compatible` `<meta>`标签，否则它们将永远以 IE7 的形式呈现。

这个问题还远未明确，继续分裂着社区，微软正在专心倾听。就在一周前，[网络标准项目](http://www.webstandards.org/)的成员(他们中的一些人参与了与微软合作开发其浏览器版本定位计划)与微软的克里斯·威尔逊一起参加了[圆桌讨论](http://www.webstandards.org/2008/02/24/wasp-round-table-ie8s-default-version-targeting-behavior/)，讨论社区对该公司 IE8 计划的反应，以及一些为更好地调整这些计划而提出的想法。

如果你对这场辩论感兴趣，这场讨论当然值得一听(或者说[文字记录](http://www.glendathegood.com/wasp/transcript.html)值得一看)，但是**这一切都归结于克里斯·威尔逊的临别赠言**:

> “[……]我们还没有看到任何能够保护我们用户体验的东西，以及我们已经想出的东西，但我们肯定会努力减少 web 开发人员的痛苦。”

因此，至少在目前，听起来微软对“IE7 默认”行为的计划立场坚定，这种行为让很多人感到不安。

但也不全是坏消息:讨论中确实包含一些有趣的花絮，可能会带来一些希望。

*   Aaron Gustafson 明确表示，Internet Explorer 的版本定位功能将像适用于 CSS 一样适用于 JavaScript，这使得微软能够对其 JavaScript 标准合规性进行一些早该进行的改进。

    例如，Internet Explorer 对`document.getElementById`的实现总是通过名称和 id 属性来选择元素。如果不是因为计划中的版本定位功能，修复这个问题将完全破坏现有的依赖于 Internet Explorer 的非标准 JavaScript 行为的 web 应用程序。

*   据克里斯·威尔逊称，IE8 的公开测试版将包括一个功能，让开发者以“超级标准”模式测试所有网站的渲染。这将为开发者和微软提供一个机会来衡量 IE8 默认为全标准模式会造成多大的破坏。

*   令人沮丧的是，一个新的浏览器版本带来的许多不兼容性仍然只是归结于浏览器的用户代理字符串。因此，微软正在考虑提供一个用户可访问的选项，使 IE8 在用户代理字符串中宣布自己为 IE7。

希望 IE8 测试版比 IE7 测试版更清楚地表明，兼容性问题是 IE8 改进标准符合性的结果。根据 Wilson 的说法，许多开发人员只是简单地认为 IE7 测试版中他们网站的渲染问题是测试版中的错误，因此直到 IE7 发布后才费心修复它们。

## 分享这篇文章