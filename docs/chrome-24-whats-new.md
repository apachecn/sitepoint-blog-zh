# Chrome 24 有什么新功能

> 原文：<https://www.sitepoint.com/chrome-24-whats-new/>

自从我上次写关于谷歌 Chrome 新功能的文章已经过去六个月了。浏览器一直在稳步发展，并且[吸引着新用户](https://www.sitepoint.com/browser-trends-january-2013/)，但是很少有令人兴奋的事情可以报道。

Chrome 24 是在 Firefox 18 之后几天发布的。你可能没有注意到自动更新，但是对 web 开发者来说有很多有趣的特性…

## MathML 支持

数学标记语言是一种描述数学符号和公式的 XML 模式。它产生的结果类似于 Microsoft Office 公式编辑器或 OpenOffice / LibreOffice 中的数学应用程序。

在 MathML 之前，您需要一个图形来生成一个等式，例如:

![equation](img/f335443ec935b4b26397662938d80e59.png)

现在可以对其进行编码:

```
<math mode="display" >
  <mrow>
    <mi>x</mi>
    <mo>=</mo>
    <mfrac>
      <mrow>
        <mo form="prefix">−</mo>
        <mi>b</mi>
        <mo>±</mo>
        <msqrt>
          <msup>
            <mi>b</mi>
            <mn>2</mn>
          </msup>
          <mo>−</mo>
          <mn>4</mn>
          <mo>⁢</mo>
          <mi>a</mi>
          <mo>⁢</mo>
          <mi>c</mi>
        </msqrt>
      </mrow>
      <mrow>
        <mn>2</mn>
        <mo>⁢</mo>
        <mi>a</mi>
      </mrow>
    </mfrac>
  </mrow>
</math>
```

MathML 现在可以在 Chrome、Safari、Firefox 中使用，并且在 Opera 中有部分支持。还没有在 IE 中实现的迹象。我怀疑在未来一段时间内，SVG 可能是更容易的跨浏览器选择。

## 索引数据库支持

IndexedDB 是一种客户端数据存储技术。虽然我们在 [localStorage 和 sessionStorage](https://www.sitepoint.com/offline-web-application-tutorial/) 中已经有了键值对，但 IndexedDB 提供了更好的数据结构和索引支持。它是被认可的 [Web SQL 数据库](https://www.w3.org/TR/webdatabase/)的继承者，后者从未成为一个被认可的 W3C 规范。

IndexedDB 本身需要一篇文章，但现在这项技术在 Chrome、Firefox 和 IE10 中得到支持，变得更加可行。它很可能会出现在 Safari 中，因为像 Chrome 一样，它使用 webkit 引擎。我毫不怀疑歌剧院正在为此努力。更多信息，请参考 [W3C IndexedDB 规范](https://www.w3.org/TR/IndexedDB/)。

## CSS 自定义过滤器

Adobe 建议使用自定义滤镜，它允许您对任何 HTML 内容应用图像或类似电影的滤镜效果。典型的选项包括灰度到颜色的过渡、扭曲、动画阴影等等。本质上，它类似于很多很多年前在 IE 中实现的 SVG 过滤器或微软专有的 CSS 过滤器属性。

Chrome 的实现是实验性的，需要 webkit 前缀，并且必须在大约三分之二处的**关于:标志**中启用——参见**启用 CSS 着色器**。这看起来很令人兴奋，但不要指望一两年内会有可靠的技术。

更多信息，请参考最近的 [W3C 滤镜效果规范](https://dvcs.w3.org/hg/FXTF/raw-file/tip/filters/index.html)和 [Adobe 的演示页面](http://adobe.github.com/web-platform/samples/css-customfilters/)。

## 风格日志记录

Chrome 的开发者工具控制台现在支持风格化日志记录，使用的语法与在 [Firefox 的 Firebug 插件](https://www.sitepoint.com/firebug-1-11-whats-new/)中实现的语法相同。例如:

```
console.log("%cBlue text!", "color:blue;");
```

您可以应用任何 CSS 样式来突出显示错误或其他情况。

## 这样更快

Chrome 一直很快，但最新版本比以往更快。特别是，谷歌声称启动时间更短，并报告去年整体改善超过 26%。

如果你不相信他们，试试新的性能标签— **chrome://performance/** 。您需要首先在 **about:flags** 中启用性能监控。

## 大量错误修复

在 Chrome 24 中已经有超过 [20 个 bug 被压扁。此外，还向发现高严重性漏洞的开发者支付了 6000 美元。](http://googlechromereleases.blogspot.co.uk/2013/01/stable-channel-update.html)

Chrome 的速度、稳定性和对标准的支持使其成为无可匹敌的浏览器。很高兴看到 Google 和 Webkit 团队没有变得自满。愿它长久持续下去。

## 分享这篇文章