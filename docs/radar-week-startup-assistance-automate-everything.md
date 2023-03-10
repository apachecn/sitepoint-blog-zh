# 本周我们关注的是:Windows 95、启动帮助和自动化

> 原文：<https://www.sitepoint.com/radar-week-startup-assistance-automate-everything/>

欢迎来到《在我们的雷达上》,这是一个来自 web 开发世界的新闻、趋势和其他酷东西的每周综述。

## 为什么 Windows 95 将微软从 9 版推向 10 版

在上周宣布 Windows 8 之后将会是 Windows 10 之后，人们开始问为什么第 9 项被跳过了。现在看来，迁移到 10 是由于早期测试第 9 版，以及 Windows 95 和 98 的问题。微软以外的懒惰开发人员只检查版本号是否以 9 开头，假设它是 Windows 95 或 98。

这是给新的 Windows 9 带来麻烦的现有代码。

`if (version.StartsWith("Windows 9"))
{
/* 95 and 98 */
} else {`

}

## 开发自己的网站和应用程序

我们最好在自己的代码中避免上述编码错误，所以在一篇关于[50+创业公司如何管理他们的代码](http://foundersgrid.com/code-management)的文章中，这里有一些给小开发者的建议。[一夜成名也不容易，下面是产品搜索和黑客新闻如何做到的](https://medium.com/@jadlimcaco/an-overnight-success-660739c2a7e9)。

有了像 [Sway](https://sway.com/) 这样的服务，你可以很容易地为一个演示或开发收集信息，而 [Webplate](http://getwebplate.com/) 是一个前端框架，让你专注于构建你的网站或应用程序。

如果你要移动化，那么这里有 [5 个成功应用的移动设计模式](http://sitepoint-versioning.cmail1.com/t/y-l-itddhjl-jytuuijjdu-n/)，这里还有 [20 多个文档和前端开发者指南](https://www.sitepoint.com/20-more-docs-guides-front-end-developers/)。

如果你打算申请开发代码的工作，这里有 [5 个 JavaScript 面试练习](https://www.sitepoint.com/5-javascript-interview-exercises/)，然后你可以[参加测试](https://sitthetest.com/)并展示你对开发的了解，也许甚至可以使用[的 JavaScript 骨骼跟踪和 Leap Motion](https://www.sitepoint.com/skeletal-tracking-javascript-leap-motion/) 来实现一些惊人的东西。

## 电子邮件中的 CSS 形状

本周，我们得到了一些关于[写 CSS 和 Sass 的很好的建议，这些建议并不糟糕](http://ashleynolan.co.uk/blog/writing-css-and-sass-that-doesnt-suck)，当我们接手一个新项目时，我们需要寻找和改进一些东西。相反，如果你想开发一些新鲜的东西，[bascss](http://www.basscss.com/)给了我们一个低级的 css 工具。我们还看到了一些在电子邮件中可靠地创建 [CSS 形状的好技术。](http://stylecampaign.com/blog/2014/10/css-shapes-in-email/)

## 自动化一切

自动化这个星期已经成为每个人的话题。[响应式图像](http://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/)现在可以通过 srcset 属性轻松实现，而[响应式线条高度](http://viljamis.com/blog/2014/responsive-line-height/)也可用于创建不同屏幕尺寸的最佳可读性。
我们也有一个使用 [fixmyjs](https://github.com/jshint/fixmyjs) 自动修复 JavaScript 林挺错误的[探索，你也可以尝试一下](http://addyosmani.com/blog/fixmyjs/)。另一个同样值得一试的自动换算器是 [jsfmt](https://github.com/paulirish/sublime-jsfmt) ，它已经在我的 Sublime Text 和 Atom 编辑器中找到了自己的方式。

我们本周遇到的其他有趣的 JavaScript 内容包括:

*   [HTML js](https://github.com/nhanaswigs/htmljs)–HTML 渲染引擎和数据绑定
*   [学习 AngularJS 玩超级马里奥兄弟](https://github.com/blnight/ngMario)
*   rwd perf-一个响应性网页设计的性能测试工具

本周哪些链接引起了你的注意？你想通过玩马里奥来学习哪种框架或语言？

## 分享这篇文章