# 大量 SitePoint 参考更新完成:这将拯救你的理智

> 原文：<https://www.sitepoint.com/massive-sitepoint-reference-update-completed-itll-save-your-sanity/>

![browsers-sm](img/7e2721f5d8f395716df4a13ddf624201.png "browsers-sm")如果你最近一直在浏览 [SitePoint HTML 和 CSS 参考](https://reference.sitepoint.com/)，你可能已经注意到**兼容性表已经更新！**

Ian Lloyd、Paul O'Brien 和 Tommy Olsson 一直在努力工作，用最新的浏览器测试 HTML 元素和 CSS 属性，并接受参考用户的反馈，在适当的时候更新信息。

CSS 和 HTML 参考现在显示以下浏览器的兼容性信息:

*   Internet Explorer 版本 5.5、6、7 和 8
*   火狐 1.0、1.5、2、3 和 3.5 版本
*   ![Safari icon](img/a4dad69f73625923d783bbb593a653be.png "Safari icon") Safari 版本 1.3、2、3.1 和 4
*   Opera 9.2、9.5 和 10
*   ![Chrome icon](img/3b6ad085d297ec7c5c827088d703654b.png "Chrome icon")铬 2

(*图标由[浏览器版本图标](http://browserversionicons.com/)* 提供)

关于 SitePoint Reference，我最喜欢的是兼容性注释部分，因为它帮助你做出明智的决定，防止你陷入兼容性测试陷阱；这种情况会让你抓狂，因为你不知道为什么你的 CSS 能在除了一个浏览器之外的所有浏览器中工作。

例如，我们现在可以从参考文献中看到，浏览器对 CSS3 属性选择器有合理的支持:

![Browser CSS compatibility table](img/54a55e74d9b6856891bbb59b43f7191b.png "Browser CSS compatibility table")

所以，假设你正在做一个会给你带来名声和财富的大项目。现在，决定是否在项目中依赖 CSS3 属性选择器变得更容易了。你可以看到 IE6 根本不提供支持，所以你可以选择是否支持这个浏览器。如果您决定继续使用 CSS3 属性选择器，那么您会得到警告，IE7 和 8 可能会有问题。您阅读兼容性说明部分，这就是您的发现。

在 IE8 中，如果你试图通过`rowspan`或`colspan`属性匹配一个`td`或`th`元素，你最终会选择所有的`td`或`th`元素。这是一个边缘案例，可能不会影响这个项目，但知道这一点很好。

然而，在 IE7 中，如果你试图用一个`for`属性值指向一个`label`元素，你会遇到一个问题。如果你试图用`for`值`"name"`选择一个`label`元素，选择器`label[for="name"]`将在 IE7 中失败；你需要使用`label[htmlFor="name"]`来代替。

事实证明，这正是您要在部署的客户端字段验证库中设置字段标签样式的方式。你的客户有大量的 IE7 用户，这个小技巧让你少了半天的沮丧。

感谢 SitePoint CSS 参考—你是最棒的！

## 分享这篇文章