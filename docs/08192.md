# 使用 Animate.css 制作简单的 CSS3 动画

> 原文：<https://www.sitepoint.com/animate-css-css3-animation/>

尽管我最初对动画*是否应该在 CSS 中定义持保留意见，但我不得不承认它们非常有用，比 JavaScript 中的等效效果更快、更流畅。然而，制作优秀的动画并不容易——它需要大量的耐心、尝试、错误、测试和厂商前缀诡计。*

[**Animate.css**](http://daneden.me/animate/) 让编码变得稍微可以忍受一些。只需选择一种效果，观看它的运行，下载代码，并在 HTML 元素中添加几个类。你可以下载完整的 2500 行 CSS 文件或者[使用你需要的效果创建一个定制版本](http://daneden.me/animate/build)。

Animate.css 是由英国曼彻斯特的设计师兼学生 Dan Eden 开发的:

> 我正在做一些带有 CSS3 动画关键帧的个人项目，发现自己在重复代码。所以我开始创建一个我可以随意加入的动画库。在我做出自己满意的东西后，我在 GitHub 上发布了代码，它就从那里起飞了。
> 
> 人们喜欢动画！

我们当然有。我目前最喜欢的是底部特价区的“铰链”。非常酷。我正在努力想一个用例，但是从现在开始可能会应用到我所有的链接上！

## 前缀困境

虽然前缀可能是一种不可避免的邪恶，Animate.css 展示了它们是多么的笨拙。关键帧及其关联的变换属性必须用-webkit、-moz、-ms、-o 和无前缀的值来定义。这导致了大量特定于浏览器的重复代码，难以维护和调试

```
 @-webkit-keyframes rollIn {
	0% { opacity: 0; -webkit-transform: translateX(-100%) rotate(-120deg); }
	100% { opacity: 1; -webkit-transform: translateX(0px) rotate(0deg); }
}

@-moz-keyframes rollIn {
	0% { opacity: 0; -moz-transform: translateX(-100%) rotate(-120deg); }
	100% { opacity: 1; -moz-transform: translateX(0px) rotate(0deg); }
}

@-ms-keyframes rollIn {
	0% { opacity: 0; -ms-transform: translateX(-100%) rotate(-120deg); }
	100% { opacity: 1; -ms-transform: translateX(0px) rotate(0deg); }
}

@-o-keyframes rollIn {
	0% { opacity: 0; -o-transform: translateX(-100%) rotate(-120deg); }
	100% { opacity: 1; -o-transform: translateX(0px) rotate(0deg); }
}

@keyframes rollIn {
	0% { opacity: 0; transform: translateX(-100%) rotate(-120deg); }
	100% { opacity: 1; transform: translateX(0px) rotate(0deg); }
} 
```

CSS 预解析器可以缓解这种情况，但是它们并不适合所有人，而且项目必须随着 CSS3 的发展而不断更新。我很快得出结论，Florian Rivoal 关于从第一天起就支持无前缀属性的建议是一个好的举措。

然而，就目前而言，如果你想要一些快速的效果而不需要自己动手，那么 [**Animate.css**](http://daneden.me/animate/) 是一个很好的选择。

## 分享这篇文章