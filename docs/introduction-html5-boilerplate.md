# HTML5 样板文件介绍

> 原文：<https://www.sitepoint.com/introduction-html5-boilerplate/>

即使对于最熟练的设计老手来说，开始一个新的标准也是一件令人畏惧的事情，即使一种语言是为了使编码更容易而设计的，比如 HTML5。也就是说，对 HTML 4.01 的透彻了解是可以建立的，这只是一个知道哪些[标签在](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5/HTML5_element_list)中，哪些不在的问题。

HTML5 样板通过提供专业的前端模板来帮助设计人员开始使用新标准，该模板允许您使用一组 HTML5 就绪的功能和元素来创建快速、健壮和适应性强的网站。

它是由保罗·爱尔兰人和 T2·迪维娅·马年创建的，是一个开源项目，非常适合创建支持旧浏览器的跨浏览器网站，同时也为 HTML5 做好了准备。

可以从 [HTML5 样板](http://html5boilerplate.com/)网站下载它的完整版本，或者不包括所有解释性文档的精简版本。轻量级版本推荐给以前使用过它的人，一旦你熟悉了这个模板就可以使用了。对于那些有信心可以完全使用它的人，还有一个可定制的选项，允许您选择您需要的元素。

## 盒子里有什么？

HTML5 样板文件中的核心特性包括您需要开始的所有必要元素，以及支持文档:

*   超文本标记语言
*   半铸钢ˌ钢性铸铁(Cast Semi-Steel)
*   Java Script 语言
*   。htaccess (Apache web 服务器配置)
*   Crossdomain.xml
*   杂项(忽略文件、gitignore 等等)

还包括 Modernizr 以允许您在 IE 中设计新的 HTML5 元素，并帮助检测所有浏览器中的 HTML5 或 CSS3 功能，包括 IE 的早期版本(v9 之前)。

让我们先来看看 HTML，它的核心是由一些针对相关 IE 特定类的 IE 条件注释和针对旧版本 IE 的 CSS 组成的。这些为使用条件类技术的设计者带来了一定的好处，比如更容易与 WordPress 和 Drupal 等 CMS 集成。

它还修复了旧版本 IE 的许多 CSS，以及适用于 `tag. This validates as HTML5 and the `no-js`类的 IE 类，允许您在最终用户浏览器中启用或禁用 JavaScript 时添加自定义样式。HTML 还包括与 Modernizr 和 Dojo 相同的元素，以及用于 Google Frame、用于 jQuery 的 Google CDN 和内容区域的 Google Analytics 跟踪代码的选项。`

 `您还可以将 HTML5 样板文件与 [Initializr](http://www.initializr.com/) 一起使用(点击获取[演示页面)，它基于样板文件生成模板，允许您选择想要的和不想要的元素。有了这个，你也可以选择从一开始就使用一个](http://www.initializr.com/try)[响应模板](http://verekia.com/initializr/responsive-template)，而不是从一个空白页开始。

## 入门指南

一旦你下载了 HTML5 样板文件，是时候建立网站的基本结构，添加内容，风格和功能，并开始测试了！首先，基本结构看起来像下面这样:

```
.
├── css
│   ├── main.css
│   └── normalize.css
├── doc
├── img
├── js
│   ├── main.js
│   ├── plugins.js
│   └── vendor
│       ├── jquery.min.js
│       └── modernizr.min.js
├── .htaccess
├── 404.html
├── index.html
├── humans.txt
├── robots.txt
├── crossdomain.xml
├── favicon.ico
└── [apple-touch-icons]

```

所以，让我们看看如何使用它们，从 CSS 开始，这个目录应该包含你站点的所有 CSS 文件。注意，这个目录已经包含了一些帮助你入门的 CSS，以及 normalize CSS。

起始 CSS 包括:

*   HTML5 样板默认值
*   常见助手
*   占位符媒体查询
*   打印样式

这不依赖于条件类名、条件样式表或 Modernizr，并且可以“开箱即用”，无论您的开发偏好如何。

## 常见助手

可以将`.ir`类添加到应用图像替换的元素中。当用图像替换内容时，有必要设置一个特定的宽度和高度元素，以便图像出现。

```
.ir {
background-image: url(http://yoursite.cimg/logo.jpg);
background-size: 100% auto;
width:450px;
height:450px
}
```

详述所有 CSS 类的代码片段会涉及大量的文本，所以我们将着眼于最相关的部分。但是，请记住存在以下类:

*   。`hidden`(添加任何想要隐藏的元素)
*   `.visuallyhidden`(对浏览器隐藏文本，但对屏幕阅读器可用)
*   `.invisible`(添加到任何元素以隐藏而不影响布局)
*   `.clearfix`(添加确保任何浮动的子元素都包含在元素中)
*   分页媒体样式(仅在少数浏览器中受支持)

## 媒体查询

HTML5 样板文件的一个美妙之处在于，它让“移动优先”和 RWD 网站的入门变得很简单。然而，并不是每个人都同意使用媒体查询是最好的方法，正如 Jason Grigsby 在 2010 年讨论的那样。

在 HTML5 样板文件中，占位符有助于构建不同分辨率和屏幕尺寸的移动样式。文档建议您根据站点内容调整媒体查询，而不是“镜像特定设备的固定尺寸”。

虽然我相信“移动优先”的方法是一种明智的方法，但并不是每个人都想采用这种方法，如果你是其中之一，那么你可以编辑或删除占位符媒体查询。对于移动应用，你可能想看一看[移动样板](https://github.com/h5bp/mobile-boilerplate)。

## 打印样式

这些工具去除了所有的背景颜色，将字体改为没有阴影的深灰色，以帮助最终用户节省打印机墨水。它们还被设计成减少页数以减少纸张的使用，这对那些对环境感兴趣的客户来说是一个方便的小卖点！

![print styles](img/e604e554fbc3ea5f2be912491eb79790.png)

## 其他目录

JS 目录还包括一些帮助你开始的初始代码，也是你想要存储所有网站 JS 文件、库和插件的地方。**。htaccess 目录**包含 Apache 服务器的默认服务器配置；如果你想使用不同的设置，那么你[可以在这里找到它们](https://github.com/h5bp/server-configs)。

还有一个非常基本的 404 页面，你可以调整以适应，以及强制性的 index.html 文件，其中包含必要的基本框架，让你开始构建所有的页面。

## 核标准情报中心

样板文件还提供了 favicon 和 apple touch 图标的“初始集”,你可以用自己的图标替换它们。根据样板文件开发者 Paul Irish 的说法，苹果触摸图标也可以在许多浏览器和设备上使用，他也提供了代码的演示。

快速的网络搜索会显示，很难找到任何人对 HTML5 样板没有一句好话，并且已经产生了一些关于使用最佳片段的文章。

毫无疑问，模板使得开始用 HTML5 编码变得更加容易，更重要的是，新的元素使用起来很简单，可以使网站看起来很棒。

## 示例网站

![1.21 Jiggawatts](img/cc2242ee76919d1e786a4a7a82718b2f.png)

在 1.21 Jiggawatts 中，快速浏览一下代码就能告诉你你需要知道的一切，不仅代码片段泄露了它，网站设计者还在源代码中给出了它应该出现的地方。

其他网站包括:[巴拉克·奥巴马](http://www.barackobama.com/)、[梅赛德斯·奔驰](http://a-class.mercedes-benz.com/com/en/)和[美国宇航局](http://data.nasa.gov/)，不一而足。要获得完整的展示，请看 HTML5 样板，一个在线展示。

## 更多资源

Rob Larson 的 IBM 文章给出了一些重新安排文件夹结构的技巧，还包括“Web 的标记”片段，供那些使用文件协议运行 Windows 时在 IE 中测试模板的人使用。

要在以这种方式工作时取消安全 nag 框，请在 doctype 声明后插入以下内容:

```
< !doctype html>
< !—saved from url=(0014) about:internet -->
```

山姆·夏洛克的 WordPress 主题结合了样板文件和主题框架，可以在这里找到，对于 CSS 框架的粉丝来说，添加 [960.gs](http://960.gs/) 也很简单。

HTML5 样板文件是启动任何需要跨浏览器功能的新项目的优秀资源。现在市场上有许多设备，很难知道从哪里开始，HTML5 样板文件为您提供了移动设备、平板电脑和桌面所需的所有框架代码。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。有关于可访问性的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?191-Accessibility-amp-Usability?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章`