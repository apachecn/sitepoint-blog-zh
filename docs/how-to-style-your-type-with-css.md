# 如何用 CSS 设计你的字体

> 原文：<https://www.sitepoint.com/how-to-style-your-type-with-css/>

几周前，我写了一系列关于排版规则的帖子，以及如何应用[对比](https://www.sitepoint.com/focus-on-typography-part-1-contrast/)、[空间](https://www.sitepoint.com/focus-on-typography-part-4-space/)、[大小](https://www.sitepoint.com/focus-on-typography-part-3-size/)和[层次](https://www.sitepoint.com/focus-on-typography-part-2-hierarchy/)的概念，我们可以制作出吸引人的、有吸引力的、重要的是易读的文本。这些规则同样适用于印刷作品和网络。让我们看一个简单的单个网页的例子，它主要由文本组成，以及我们如何使用 CSS 来设置它的样式。在这个例子中，我不打算关注设置容器或`div`来保存文本。

在下图中，你可以看到一页关于电影《大白鲨》的纯文本。

在这个阶段，文本的大小、字体和颜色都是一样的。

![Jaws](img/e580297a7872e4917c261b7a6818a54e.png)

让我们从放入一些标题开始。这将涵盖使用大小和层次来区分文本块和标题的概念。我给标题“大白鲨”添加了`<h1>`标签，给副标题添加了`<h2>`标签，给子副标题添加了`<h3>`标签。我还添加了一个 ~~< h4 >~~ 段落标记到这段文字中，上面写着“来自维基百科，免费的百科全书”(顺便提一下，我就是从这里得到这个例子的文字的。)

<ins>我最初用了一个`<h4>`标签来表示“来自维基百科，免费的百科全书”，这是不正确的。在`<h1>`和`<h2>`标签之间放一个`<h4>`并不是好的做法。因此，在这种情况下，我可以使用段落标记，稍后将对其应用类样式。</ins>

![text1](img/acc6b92e868e989a9b58d4a33ade0996.png)

通过这些简单的改变，我们已经可以清楚地看到哪些文本是最重要的。现在，我将在 HTML 页面上附加一个级联样式表，并开始设计样式。

在 HTML 页面顶部的 `<head>`部分，我添加了以下内容；

```
<link href="style.css" rel="stylesheet" type="text/css" />
```

其中“style.css”是我的样式表的名称，它位于与我的 HTML 页面相同的文件夹中。

我设置的第一个样式是针对`<body>`标签的。我选择了深蓝色的背景，白色的文字和 arial 字体。因此，我的样式表中的代码如下所示:

```
 body {
  font-family: Arial, Helvetica, sans-serif;
  color: #FFFFFF;
  background-color: #003366;
  } 
```

![Jaws text](img/9267bee66a4cc1923e8268c0946dcc16.png)

现在文本颜色和背景之间有了很好的对比，但是我也想在`<h1>`标签和其他文本之间创造更多的对比。将`<body>`文本设置为 12 像素，将`<h1>`设置为 36 像素，浅蓝色和乔治亚字体，底部带点边框，结果如下:

```
 body {
  font-family: Arial, Helvetica, sans-serif;
  color: #FFFFFF;
  background-color: #003366;
  font-size: 12px;
  }

h1 {
  font-family: Georgia, "Times New Roman", Times, serif;
  font-size: 36px;
  color: #00CCFF;
  border-bottom-width: 1px;
  border-bottom-style: dotted;
  border-bottom-color: #00CCFF;
} 
```

![image0075](img/9879f414f75feade65119d7a1abd858b.png)

我想让`<h2>`和`<h3>`标签匹配`<h1>`的颜色，但是不要太大，也不要加下划线。

```
 h2 {
  font-family: Georgia, "Times New Roman", Times, serif;
  color: #00CCFF;
  font-size: 24px;
} 
```

![image0095](img/c63a9824e75126fc0c4fd431c189f5c4.png)

对于`h3`标题，我将颜色设置为白色，但是将字体改为 Arial，并将尺寸减小到 16 像素。

```
 h3 { 
  font-family: Arial, Helvetica, sans-serif; 
  font-size: 16px; 
  color: #FFFFFF; 
} 
```

![](img/6cf26d493156bb483c97f4025a325495.png)

我想应用的另一个排版原则是空间的概念，我将在标题中使用字母间距，在正文中使用行距。为了在`<h1>`标签中的字母之间增加额外的空间，我们添加了以下内容:

```
 letter-spacing: 5px; 
```

![Jaws heading](img/9d97991d63a2da4de52a262cd95d8ed7.png)

要在正文文本的各行之间添加空格，以便于阅读，请在正文中添加以下内容:

```
 line-height: 20px; 
```

和下面的`<h1>`标签。

```
 line-height:40px; 
```

![jaws text](img/515cdfbf4340cf13b26c104d911876fa.png)

为了完成文本的样式，我想改变的最后一件事是页面顶部的 ~~h4 heading~~ p 标签，上面写着“来自维基百科，免费的百科全书”。我将文本保持较小的尺寸，但是将文本设置为大写。

<ins>我在这里错误地使用了`<h4>`标签。更好的选择是使用段落样式。所以我创建了一个名为 byline 的类。</ins>

```
 .byline {
  text-transform: uppercase;
} 
```

![final jaws](img/70de5702275b0e09c3d2101ce9598d25.png)

这就完成了造型。最终的样式表如下所示:

```
 body {
  font-family: Arial, Helvetica, sans-serif;
  color: #FFFFFF;
  background-color: #003366;
  font-size: 12px;
  line-height: 20px;
  }

h1 {
  font-family: Georgia, "Times New Roman", Times, serif;
  font-size: 36px;
  color: #00CCFF;
  border-bottom-width: 1px;
  border-bottom-style: dotted;
  border-bottom-color: #00CCFF;
  letter-spacing: 5px;
  line-height: 40px;
}

h3 {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 16px;
  color: #FFFFFF;
}

h2 {
  font-family: Georgia, "Times New Roman", Times, serif;
  color: #00CCFF;
  font-size: 24px;
  }

.byline {
  text-transform: uppercase;
} 
```

这是一个简单的例子，但它展示了如何获取一些纯文本并用几个属性对其进行样式化。就你能做什么而言，这只是冰山一角。你在用 CSS 来设计你的字体方面走了多远？你有什么建议想要分享吗？

**相关阅读:**

*   [关注排版，第四部分:空间](https://www.sitepoint.com/focus-on-typography-part-4-space/)
*   [关注排版，第三部分:尺寸](https://www.sitepoint.com/focus-on-typography-part-3-size/)
*   [关注排版，第二部分:层次结构](https://www.sitepoint.com/focus-on-typography-part-2-hierarchy/)
*   [关注排版，第一部分:对比](https://www.sitepoint.com/focus-on-typography-part-1-contrast/)

## 分享这篇文章