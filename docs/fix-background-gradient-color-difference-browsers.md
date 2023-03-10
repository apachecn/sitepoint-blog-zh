# 修复浏览器之间的背景渐变色差

> 原文：<https://www.sitepoint.com/fix-background-gradient-color-difference-browsers/>

**跨浏览器修复背景渐变颜色**。在开发过程中，我注意到 FireFox 12 和 Chrome Canary 21 之间有一个主要的颜色差异。这显然与 CSS3 在不同浏览器中的呈现方式有关。

![color-difference-much-firefox-chrome-canary](img/1ab65510b897610a61ece2bc96058a35.png "color-difference-much-firefox-chrome-canary")


### 之前的 CSS

```
background-image: -moz-linear-gradient(top, #5CB6F2, #FFF);
background-image: -webkit-gradient(linear, left top, left bottom, from(#0ae), to(#fff));
background-image: -webkit-linear-gradient(top, #0ea, white);
```

### CSS 之后

```
background: #FFFFFF; /* for non-css3 browsers */
filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#5CB6F2', endColorstr='#FFFFFF'); /* for IE */
background: -webkit-gradient(linear, left top, left bottom, from(#5CB6F2), to(#FFF)); /* for webkit browsers */
background: -moz-linear-gradient(top,  #5CB6F2,  #FFF); /* for firefox 3.6+ */
```

都修好了！:)

![color-difference-much-firefox-chrome-canary2](img/573ed1a924fccda0dbbbbc0f9f5d20b0.png "color-difference-much-firefox-chrome-canary2")

出于兴趣，下面是它在 Internet Explorer 9 中的样子。

![color-difference-much-ie9](img/ea82d5a3b644017e5a731d8941406cfd.png "color-difference-much-ie9")

## 分享这篇文章