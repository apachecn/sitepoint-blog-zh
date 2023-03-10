# 什么是 CSS3，萨斯，s CSS，指南针，少，波旁

> 原文：<https://www.sitepoint.com/css3-compass-sass/>

CSS3，萨斯，SCSS，指南针，LESS，波旁之间的一些快速差异。**简洁明了的要点和链接。**对于初学 CSS 的人，请阅读下面的内容，然后开始使用 COMPASS！

## CSS3

[![css3](img/a0a55dd953a66513a04d734445c37647.png)](http://www.css3.info/)

*   CSS3 是 CSS 的最新版本，为浏览器提供了额外的功能，如动画。

[学习 CSS3](http://www.css3.info/) [演示](https://jsfiddle.net/potench/Nm6Z7/show/) [文档](http://www.w3schools.com/css3/default.asp) [测试你的浏览器支持 CSS3](http://tools.css3.info/selectors-test/test.html%22)

## 萨斯和 SCSS

[![sass](img/795fa413330f7e49e50d4cbca76eff6b.png)](http://sass-lang.com)

*   SASS(语法上很棒的样式表)。
*   SASS 是 CSS3 的超集(扩展),提供了额外的功能，如嵌套、变量、混合等
*   SASS 可以在你编辑它们的时候动态生成 CSS 文件。
*   Sass 是使用 Ruby 在服务器端处理的。
*   SASS 使用换行符和缩进来确定分隔线，很像 HAML。

*   **SCSS(时髦的样式表)。**
*   SCSS 在功能上与 SASS 相同，只是它使用花括号来分隔规则。

[网站](http://sass-lang.com) [文档](http://sass-lang.com/docs.html) 

### 快速示例

```
CSS
li {
  font-family: serif;
  font-weight: bold;
  font-size: 1.2em;
}

SCSS
li {
  font: {
    family: serif;
    weight: bold;
    size: 1.2em;
  }
}

SASS
li
  font:
    family: serif
    weight: bold
    size: 1.2em
```

## 指南针

[![compass](img/7baa16c9212df920cd6591f8e8c21791.png)](http://compass-style.org)

*   COMPASS 是一个使用 SASS 的 CSS 框架。
*   COMPASS 提供了一个有价值的 CSS3 库，跨浏览器混合，排版模式。
*   COMPASS 构建图像精灵，并轻松地将它们包含在 CSS 中。
*   通过命令行轻松更新最新的浏览器前缀。

[网站](http://compass-style.org) [演示](http://compass-style.org/examples/compass/css3/opacity/) [如何安装 1](http://compass-style.org/install/) [如何安装 2](http://thesassway.com/beginner/getting-started-with-sass-and-compass)

### 快速示例

这个罗盘代码:

```
#opacity-10 {
    @include opacity(0.1);
}
```

生成此 CSS:

```
//For IE<9

#opacity-10 {
    filter: progid:DXImageTransform.Microsoft.Alpha(Opacity=10);
    opacity: 0.1;
}

//For all other browsers

#opacity-10 {
    opacity: 0.1;
}
```

## 较少的

[![less](img/e36f5c846a40e82f166b80e9e96c6923.png)](http://lesscss.org/)

*   LESS 是一个 JavaScript 库，所以它是前端处理的。
*   LESS 共享了 SASS 的许多常见功能，如嵌套、变量、混合等..
*   萨斯有指南针，少则没有。

[网站](http://lesscss.org/)试玩[文档](http://lesscss.org/#docs) [少 vs 萨斯](http://css-tricks.com/sass-vs-less/)

## 波旁威士忌酒

[![bourbon](img/fbade55af6483fd329e4e3bf69e4d36e.png)](http://bourbon.io/)

*   一个用于 SASS 的简单轻量级 mixin 库。
*   [Codekit](http://www.incident57.com/codekit/) 开箱即可处理波本调酒，无需任何配置。
*   锤子也预配置了波旁威士忌。

[网站](http://bourbon.io/) [演示](http://bourbon.io/docs/#timing-functions) [文档](http://bourbon.io/docs/)

如果我错过了任何可能对那些刚开始的人有益的东西，请留下评论！:)

## 分享这篇文章