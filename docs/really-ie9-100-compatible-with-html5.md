# 真的吗？IE9 100%兼容 HTML5？

> 原文：<https://www.sitepoint.com/really-ie9-100-compatible-with-html5/>

访问 IE 测试中心网站你可能会认为 IE9 已经 100%兼容 HTML5。他们构建了一个测试结果表，将当前的 IE9 平台预览版与所有其他主流浏览器的当前版本进行了比较。IE9 在所有的测试中都获得了 100%的分数，甚至包括标有“HTML5”的测试。

然而，仔细观察，很明显他们只是在 HTML5 标签下增加了非常有限的功能子集的测试:

*   HTML5 JavaScript 文本选择 API
*   在 HTML 中嵌入 SVG
*   JavaScript `getElementsByClassName`函数

[![HTML table of test results showing IE9 scoring 100% for all tests](img/418b172ad9d3e0bef6845da60f828c4d.png "Test results table")](https://www.sitepoint.com/really-ie9-100-compatible-with-html5/)

所以，这不是一个真正的 HTML5 兼容性测试。正如 SitePoint 总部的 Dave 所言:

> 我们已经创建了这个 IE9 一致性测试套件，并得出结论，这些其他浏览器都不是 IE9。

访问 HTML5 测试网站描绘了一幅完全不同的画面。从我做的测试来看，最好的浏览器是谷歌 Chrome 5，得分 142/160，Safari 4 达到 120，Opera 10.5 获得 ~~38~~ 102，Firefox 3.6 得分 101。评分最差的浏览器是 IE8 和第二个 IE9 平台预览版；都得了 19/160。

为了证明任何人都可以玩测试结果游戏，[Internet Explorer 9 HTML5 Canvas 活动网站](http://www.freeciv.net/yet-another-html5-browser-test/)发布了一组 html 5 测试结果，证明 IE9 始终得分为 0%。

就我个人而言，我对 IE9 感到兴奋。谁会想到微软会支持 SVG 呢？现在判断 IE9 对 HTML5 和 CSS3 等技术的支持程度还为时过早；到目前为止，我们只有一个早期的平台预览。不过，我有很高的期望！

与此同时，像这样公然误导的测试结果对任何人都没有帮助，只会制造混乱和不信任。

## 分享这篇文章