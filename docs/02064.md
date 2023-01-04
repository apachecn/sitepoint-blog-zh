# 如何在 CSS 中垂直居中文本和图标

> 原文：<https://www.sitepoint.com/atoz-css-quick-tip-vertical/>

在本文中，我们将概述水平和垂直居中的各种方法——这在过去总是很棘手，但现在却轻而易举。

## 如何使用 Flexbox 进行水平和垂直居中

一个 [Flexbox](https://www.sitepoint.com/creating-flexible-layouts-with-flexbox/) 可以在水平和垂直空间对齐元素，使 CSS 中的文本、图标或任何其他元素居中。

要使用 Flexbox 使元素居中，我们可以使用下面的代码片段:

```
.container {
display: flex;
justify-content: center;
align-items: center;
}
```

这个紧凑的代码片段将我们的`.container`转换成一个 flex 容器，该容器自动将其子元素转换成 flex 项目。然后这些柔性项目可以用`justify-content`水平居中，用`align-items`垂直居中。

通过 [CodePen](https://codepen.io) 上的 site point([@ site point](https://codepen.io/SitePoint))
用 Flexbox 看笔[、水平、垂直对中。](https://codepen.io/SitePoint/pen/BaxYaXX)