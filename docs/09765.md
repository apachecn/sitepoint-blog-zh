# 在 jQuery 动画中使用不透明度制作动画

> 原文：<https://www.sitepoint.com/animating-opacity-jquery-animate/>

当使用 jQuery.animate 使用不透明度来淡入或淡出时，所有版本的 Internet Explorer 都会遇到字体渲染问题。

这给你留下了两个选项:

使用 jQuery 的不透明(“显示”)或不透明(“隐藏”)快捷属性来处理 IE 的缺点，或者在完成动画后将过滤器 css 属性设置为“无”显然，如果你正在从完全隐藏中淡入一些东西，选项 1 是有意义的，否则你别无选择，只能使用选项 2。

我遇到了这个问题，因为我在 FCL.TABS 中使用了不透明的字体，导致我在 tabs 内容中遇到了字体渲染问题。结果，我给 FCL 打了补丁。选项卡使用不透明度的“显示”和“隐藏”属性，而不是“0？以及“1？问题已经解决了。

## 破损的代码

```
$tabContent.css('opacity', 0);
$tabContent.animate(
{
opacity: 1
}, 350);
```

## 固定代码

```
$tabContent.css('opacity', 'hide');
$tabContent.animate(
{
opacity: 'show'
}, 350);
```

## 分享这篇文章