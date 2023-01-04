# 摆弄着辛纳特拉

> 原文：<https://www.sitepoint.com/fiddling-around-with-sinatra/>

JSFiddle 是一个已经存在了一段时间的网站。它允许你创建一个“小提琴”,基本上是一个包含 HTML、CSS 和 JavaScript 的独立页面。这些可以用来展示一种特殊的技术或错误，或者只是展示你流畅的标记技巧。

去年，出现了几个做类似事情的网站。首先有[dabble et](http://dabblet.com/)和最近刚刚推出的[码笔](http://codepen.io/)。我认为在 Sinatra 尝试克隆这种类型的应用程序可能会很有趣。这应该相当简单，因为 Sinatra 使用[倾斜](https://github.com/rtomayko/tilt/)来呈现视图。这让你可以访问不同的渲染引擎，以及 CSS 预处理的 [Sass](http://sass-lang.com/) 和 [Less](http://lesscss.org/) 。

我的想法是创建一个名为 Riddle(一个 Ruby 风格的小提琴)的简单应用程序，允许用户创建一个“谜语”，它基本上是一个自包含的 HTML，CSS 和 JavaScript。首先，我将限制用户使用 markdown 或普通的 HTML 进行标记，使用 SCSS 或普通的 CSS 进行样式化。希望这将有助于展示 Sinatra 的视图助手如何工作，以及如何使用 Sinatra 服务 CSS 和 JavaScript。

## 模拟了

我想让应用程序的站点架构正确，所以我首先关注视图，以展示用户如何在应用程序中导航。我们想要一个起始页，列出所有已经创建的谜语，让用户点击查看。顶部还应该有一个按钮来创建一个新的谜语。这将显示一个表单，您可以在其中添加 HTML、CSS 和 JavaSript。一旦用户单击保存，他们将被带到新创建的视图。

所以，让我们开始编码吧！我们首先需要创建一个名为 main.rb 的空文本文件，并添加以下代码行: