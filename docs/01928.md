# 使用 CSS 背景滤镜创建令人惊叹的图像效果

> 原文：<https://www.sitepoint.com/create-stunning-image-effects-with-css-backdrop-filter/>

![CSS backdrop-filter property](img/e47902cfd164abd8240bf8504010fd10.png)

今天，我们将学习 CSS [背景滤镜](https://drafts.fxtf.org/filters-2/#BackdropFilterProperty)属性，它是在滤镜效果模块 2 级规范中引入的。特别是，我们将涵盖`backdrop-filter`属性的**语法**、**浏览器支持**和**实际应用**。

*本文演示中的所有图片都来自[Pixabay.com](https://pixabay.com/)。*

## 背景滤镜不同于滤镜

你可能已经听说过 CSS 过滤器。如果你需要一个关于这个主题的复习，CSS 滤镜效果:模糊，灰度，亮度和更多！是一本很棒的读物。

虽然`filter`属性帮助我们在给定元素上应用模糊和棕褐色等效果，但`backdrop-filter`属性将帮助我们在元素后面的**区域应用相同的效果。这里有一个演示来说明这种差异(确保您使用的浏览器像 Chrome 一样，并且启用了实验性 Web 平台功能标志——下一节将详细介绍浏览器支持):**

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看钢笔[滤镜 vs 背景滤镜](http://codepen.io/SitePoint/pen/MbrNWW/)。