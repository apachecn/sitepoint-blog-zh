# 逐渐增强的 CSS 布局:浮动到 Flexbox & Grid

> 原文：<https://www.sitepoint.com/css-layouts-floats-flexbox-grid/>

很难实现复杂而灵活且响应迅速的网格布局。多年来，各种技术不断发展，但大多数技术，如[仿柱](https://alistapart.com/article/fauxcolumns)，都是黑客行为，而不是稳健的设计方案。

这些攻击大多是建立在 CSS *float* 属性之上的。当 [Flexbox 布局](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox)模块被引入到*显示*属性选项列表时，一个新的选项世界成为可能。现在，您不仅可以定义容器堆叠项目的方向，还可以包装、对齐(项目和行)、排序、收缩等。他们在一个容器里。

有了这些权力，开发人员开始为各种布局创建自己的规则组合。灵活性占主导地位。然而，Flexbox 是为处理一维布局而设计的:行或列。相反，CSS 网格布局[允许二维的行和列布局](https://www.sitepoint.com/introduction-css-grid-layout-module/)。

## 渐进增强与优雅降级

很难创建一个支持每个用户浏览器的网站。通常使用两种选择——“适度降级”和“渐进增强”。

**优雅降级**确保网站即使出现故障也能继续运行。例如，如果一个元素对于屏幕来说太大了，那么`float: right`可能会失败，但是它会绕到下一个空白空间，这样块仍然可用。

**渐进增强**采取相反的方法。该页面从最少的功能开始，并在支持时添加功能。上面的例子可以在允许元素浮动之前使用 CSS 媒体查询来验证屏幕是否是最小宽度。

就网格布局而言，每个浏览器决定了其组件的外观。在本文中，您将通过一些真实的示例了解如何将一些 web 内容从旧策略发展到新策略。更具体地说，如何将模型从基于浮动的布局逐步增强到 Flexbox，然后是 CSS Grid。

## 页面的旧浮动布局

看看下面的 HTML 页面:

```
<main>
  <article>
    article content
  </article>

  <aside>
    aside content
  </aside>
</main> 
```

这是一个 web 页面中常见的网格布局的小例子:两个 div 共享同一个容器( *body* )。

![A float example](img/f97547be58c776093e875ca8bee96c88.png)

下面的 CSS 可以在我们将要创建的所有例子中用来设置正文样式:

```
body {
  font-family: Segoe UI;
  font-style: normal;
  font-weight: 400;
  font-size: 1rem;
} 
```

此外，我们每个 div 的 CSS 片段支持浮动效果:

```
main {
  width: 100%;
}

main, article, aside {
  border: 1px solid #fcddd1;
  padding: 5px;
  float: left;
}

article {
  background-color: #fff4dd;
  width: 74%;
}

aside {
  width: 24%;
} 
```

您可以在这里看到实际的例子:

参见 [CodePen](https://codepen.io) 上 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )的笔[浮动布局示例](https://codepen.io/SitePoint/pen/YOGWdv/)。