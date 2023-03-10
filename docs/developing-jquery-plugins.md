# jQuery 插件开发简介

> 原文：<https://www.sitepoint.com/developing-jquery-plugins/>

*这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)和[朱利安·莫茨](https://www.sitepoint.com/author/jmotz)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

你可能以前做过交互式组件，比如滑块、图库或交互式表单。虽然您可能会逐个站点地创建这些插件，但是一个很好的省时方法是将您的功能构建为 jQuery 插件来加速您的开发。

jQuery 插件允许您定义一次您的功能，然后根据需要将它放到您的项目中，让您更快地启动和运行。

我们将看看如何构建自己的 jQuery 插件。我们将着眼于你需要知道的所有领域，让你立刻开始构建插件。

我们将使用我创建的名为 *fancytoggle* 的插件来展示插件的不同部分。这是一个简单的插件，用于切换嵌套元素(如列表项)的可见性，为 FAQ 之类的东西创建手风琴式的小部件。整体的想法是关于插件的概念，但是这个例子可以帮助你了解它在实践中是如何工作的。

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [FancyToggle](http://codepen.io/SitePoint/pen/VjeNNP/) 。