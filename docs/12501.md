# W3C 标记验证服务增加了实验性的 HTML5 支持

> 原文：<https://www.sitepoint.com/w3c-markup-validation-service-adds-experimental-html5-support/>

几天前，W3C 在他们的在线标记验证服务中增加了对 HTML5 的实验支持[。在](https://www.w3.org/QA/2008/11/w3c_validator_now_with_html5.html) [Validator.nu](http://html5.validator.nu/) 上有一个 HTML5 验证器已经有一段时间了，但是在[W3C 自己的网站](http://validator.w3.org/)上增加 HTML5 验证是一个重要的催化剂，可以说是早就该有的。

有两种基本方法可以触发验证器的 HTML5 解析器。当然，一种方法是通过从“DOCTYPE”菜单中选择该选项，显式地将验证器切换到它的“HTML5(实验)”模式。(或者，如果您正在使用链接到它的其他工具，请在 URI 的查询字符串中使用`doctype=HTML5`键/值对。)

更简单的方法是在文档顶部指定一个 HTML5 `DOCTYPE`,让验证器自动检测。下面是一个最简单的 HTML5 文档，可以成功验证。注意顶部非常短的`DOCTYPE`。

```
<!DOCTYPE html>
<html>
<head><title>HTML 5 Markup Validation Test</title></head>
<body>
    <p>I'm an HTML 5 document.</p>
</body>
</html>
```

HTML5 的支持者说，该规范简化了不必要的复杂性，并使 HTML 语言现代化，从而支持当今网站的需求。

为此，HTML5 规范增加了像`[video](https://www.w3.org/html/wg/html5/#the-video-element)`这样的元素，用来——你猜对了——以一种直接的方式将视频嵌入到网页中。元素的行为很像一个 T2 元素。像`img`元素一样，`video`元素带有一个必需的`src`属性，该属性指向一个视频资源(比如一个`.mov`或`.ogg`文件)。与`img`元素不同，`video`元素是*而不是*自结束( *void* ))元素，因此您可以(并且可能应该)为嵌入的视频提供一些替代内容。(想想`<noscript>`)。还有一个免费的`[audio](https://www.w3.org/html/wg/html5/#the-audio-element)`元素。

除了新的[嵌入内容](https://www.w3.org/html/wg/html5/#embedded-content-1)和[交互性](https://www.w3.org/html/wg/html5/#interactive-content)，HTML5 还增加了大量[附加语义元素](https://www.w3.org/html/wg/html5/#dom)。比较陌生的标签有`[<header>](https://www.w3.org/html/wg/html5/#the-header-element)`、`[<section>](https://www.w3.org/html/wg/html5/#the-section-element)`、`[<nav>](https://www.w3.org/html/wg/html5/#the-nav-element)`和`[<aside>](https://www.w3.org/html/wg/html5/#the-aside-element)`，所有这些标签都是不言自明的。

今天，这些类型的结构在 HTML4.01 中经常被实现为嵌套的`<div>`或列表的大杂烩，并且主要依赖于对`class`或`id`属性的常规使用来提供语义丰富性。例如，在 HTML4.01 中，我们可能会创建一个标题，就像这样:

```
<div id="Header">
    <h1>I'm a headline in a header!</h1>
</div>
```

在 HTML5 中，我们可以用语义上有意义的`<header>`替换语义上无意义的`<div>`。

```
<header>
    <h1>I'm a headline in a header!</h1>
</header>
```

我对这一宣布感到兴奋，因为让 W3C 标记验证服务支持 HTML5 为前端开发人员提供了一个新工具。这是采用新技术成长所需要发生的事情。现在，更多的前端开发人员(包括我自己)可以更容易地尝试了解 HTML5 中的变化。

## 分享这篇文章