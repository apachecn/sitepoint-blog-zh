# 在几分钟内实现可扩展的多层滚动视差效果

> 原文：<https://www.sitepoint.com/vertical-scrolling-parallax-effect/>

我一直想实现一个视差效果，但我找不到一个“简单”的技术。事实上，对于没有任何修改 JavaScript 或 CSS 经验的人来说，本教程并不容易。但是这很简单，我将本教程分解成的步骤应该足够简单，即使是新手也可以遵循。

这是您将要创建的最终版本及其资源文件:

**演示:[下载](https://www.sitepoint.com/wp-content/uploads/2012/05/vertical-parallax-resources.zip)(ZIP–700k)**

> 这种视差效果最初是由乔纳森·尼科尔在像素英亩/F6 设计公司开发的。没有他的工作，我不可能把这些放在一起，我想确认他的努力工作。

本教程的其余部分将着重于引导你如何慢慢地建立这种效果，以便你可以修改它，以适应你的具体需要。太多的其他教程让你陷入了争论，遗漏了许多编辑设计所需的细节。

我喜欢这个视差版本(我试过*几十个*，因为它有三个独立的层，你可以很容易地调整。您可以轻松地添加文本或使用图像，尽管本教程将使用图像作为背景、浮动元素。

如果您想使用自己的图像，请确保它们是透明的 PNG 文件。我没有测试过 GIF 格式，JPG 文件不会是透明的。除非你在背景图片中使用完美的正方形，否则你会想要透明的 PNG 格式。

所以，等一下，当我们进入有趣的视差效果的兔子洞时，找点乐子。

### 步骤 1:下载文件

要开始学习，请下载完成本教程所需的资源。您将需要四个预构建的文件:

1.  jQuery 1.6——jQuery 引擎，让一切都运转起来
2.  modernizr . js——一个 jQuery 效果，如果导航元素被点击，它会给我们一个平滑的滚动效果
3.  **Parallax . js**–让视差效果起作用的 jQuery 效果
4.  **main . css**–我的 CSS 文件

其他文件包括我在本教程中使用的图像，您将(希望)稍后替换这些图像。使用上面的链接下载完整的文件集合。

### 第二步:标题内容

在您喜欢的 HTML 或文本编辑器中打开一个新的 HTML 文档。您将复制并粘贴以下代码:

`<html>
<head>
<title>Parallax Tutorial - Start Here</title>
<meta name="description" content="Parallax Tutorial" />
<link rel="stylesheet" media="all" href="css/main.css" />
<script src="js/modernizr.custom.37797.js"></script>
<script src="js/jquery-1.6.1.min.js"></script>
<script src="js/parallax.js"></script>
</head>
<body>
</body>
</html>`

这不会让你得到很多，但这是一个关键的开始。在 HTML 的 head 部分，我们有指向我们文件的链接，所以请确保将这个 HTML 文件保存在主文件夹中，将 css 文件保存在名为“CSS”的子文件夹中，将 JavaScript 保存在名为“js”的文件夹中，否则将无法工作。打开下载中的“01-start-here.html”文件，仔细检查您的工作。

### 步骤 3:添加内容

现在，我们可以开始向 HTML 添加内容了。这段代码位于标签之间:

`<div id="wrapper">
<div id="content">
<!-- Page #1 -->
<article id="page-number-1">
<header>
<h1>Parallax Demo</h1>
</header>
<p>This is a sample Parallax scrolling site with three pages. You can add all kinds of extra navigation elements on your own time, but I just wanted a simple, ready to rollout, vertical parallax system that I could easily implement and edit later.</p>
<nav class="next-prev">
<hr />
<a class="next page-number-2" href="#page-number-2">Next</a>
</nav>
</article>
<!-- Page #2 -->
<article id="page-number-2">
<header>
<h1>Wow!</h1>
</header>
<p>Did you like that nice, smooth movement? Pretty slick, eh? You can either scroll or click on the navigation elements below. It's your decision. You're an adult. I'll leave that up to you. </p>
<nav class="next-prev">
<a class="prev page-number-1" href="#page-number-1">Prev</a>
<hr />
<a class="next page-number-3" href="#page-number-3">Next</a>
</nav>
</article>
<!-- Page #3 -->
<article id="page-number-3">
<header>
<h1>More stuff...</h1>
</header>
<p>You can add as many pages as you want, but it takes some work to get all the CSS, and JS working together. Just remember that each page you add needs to have this HTML, the CSS, and the parallax.js file updated with the new page. Don't worry, you'll be a pro before you know it!</p>
<nav class="next-prev">
<a class="prev page-number-2" href="#page-number-2">Prev</a>
<hr />
<a class="next page-number-4" href="#page-number-4">Next</a>
</nav>
</article>
<!-- Page #4 -->
<article id="page-number-4">
<header>
<h1>Last page!</h1>
</header>
<p>Of course, there are all kinds of HTML and JS additions you can make. From navigation bars to the Lettering.js jQuery plugin, there's no limit. This parallax technique only relies upon the basic CSS and lightweight jQuery plugins to create the effect. Time for you tweak it and make it your own!</p>
<nav class="next-prev">
<a class="prev page-number-3" href="#page-number-3">Prev</a>
<hr />
</nav>
</article>
</div>`

现在，我们需要慢下来，看看我们刚刚做了什么。我使用了几个预定义的元素，我们需要检查一下。

*   你的网站的每一页都需要以开头来恰当地指定内容。
*   每页的标题部分由标签指定。这是单击其中一个导航按钮后滚动页面时页面停留的位置。
*   是放置导航链接的位置。您当然可以创建自己的版本，但这些都是预建的版本。

要添加新页面，您必须编辑三个文件:

*   html 文档
    *   例如，添加一个包含和惟一 ID–ID = " page-number-5 "的新页面。
    *   更新上一页的导航元素以包含您的新页面。引用你给它的 ID 名。
*   CSS 文档
    *   在“内容文章”部分，将您的页面 ID 添加到列表中
    *   在“content article”标签下添加一个新的#page-number-5 标签(或者任何你称之为你的页面的东西)。将位置设为绝对位置，高度以 1090 像素为增量——因此第五页的高度为:4360 像素；举个例子。
*   Parallax.js
    *   为新页面添加新功能(不要大惊小怪！这比听起来容易多了！)
    *   只需复制并粘贴“页码-4”函数，并重命名其中的 ID

从可下载资源的“02-add-content.html”文件中查看这个步骤的完成版本。

### 步骤 4:添加背景图像

在内容下方和结束标签上方添加以下代码:

`<div id="parallax-bg1">
<img id="bg1-1" src="img/cloud1.png" alt="cloud"/>
<img id="bg1-2" src="img/cloud2.png" alt="cloud"/>
<img id="bg1-3" src="img/cloud1.png" alt="cloud"/>
<img id="bg1-4" src="img/cloud2.png" alt="cloud"/>
<img id="bg1-5" src="img/cloud1.png" alt="cloud"/>
</div>`

这将添加背景或“最远”层中的图像。换句话说，这些图像移动得最少。您可以使用 CSS 文档单独定位每个图像。只需打开 CSS 文档，搜索“背景图片”，你就会看到每个是如何定位的。

要添加新图像，您需要为每个图像复制并重命名一个新的 CSS 属性。

参见下载中的“03-background-images.html”文件，了解完成的步骤。

### 步骤 5:添加中间背景图像

这就像上一步一样，所以将这段代码粘贴到背景图像的代码下面:

`<div id="parallax-bg2">
<img id="mg2-1" src="img/cloud3.png" alt="cloud"/>
<img id="mg2-2" src="img/cloud3.png" alt="cloud"/>
<img id="mg2-3" src="img/cloud3.png" alt="cloud"/>
<img id="mg2-4" src="img/cloud3.png" alt="cloud"/>
<img id="mg2-5" src="img/cloud3.png" alt="cloud"/>
<img id="mg2-6" src="img/cloud3.png" alt="cloud"/>
<img id="mg2-7" src="img/cloud3.png" alt="cloud"/>
</div>`

请注意，我一遍又一遍地使用同一个图像，而且比背景图像多使用了几个。这就是这种效果的美妙之处，因为如果你想要或添加许多不同的图像或文本，它允许你重用元素。只需为您想要添加的每个新图像创建一个新的 CSS 属性，并根据需要对其进行定位。

您可以在“中间背景图像”部分下编辑 CSS 文件中每个元素的位置。参见下载中“04-midground-images.html”文件的最后一部分。

### 步骤 6:添加前景图像

你现在应该是冠军了。与步骤 5 和 6 相同:

`<div id="parallax-bg3">
<img id="fg3-1" src="img/twitter.png" width="529" height="386" alt="Big freaking Twitter bird"/>
<img id="fg3-2" src="img/facebook.png" width="603" height="603" alt="Facebook in your face!"/>
<img id="fg3-3" src="img/linkedin.png" width="446" height="446" alt="LinkedIn logo"/>
<img id="fg3-4" src="img/landscape.png" width="1104" height="592" alt="Landscape foreground"/>
<img id="fg3-5" src="img/design-festival-logo.png" width="135" height="136" alt="Bluified Design Festival logo"/>
</div>`

同样，可以在 CSS 文档中的“前景图像”下轻松编辑这些图像的位置。

参见文件“05-foreground-image.html”中的完成步骤。

### 步骤 7:添加导航元素(可选)

我增加这一步是因为张克帆·尼科尔做得非常好，我认为在本教程中保留他的想法是值得的。您可以将这段代码放在中的任何地方，但是我喜欢将它放在顶部(但是在开始内容 div 的下面):

`<nav id="primary">
<ul>
<li>
<h1>Intro</h1>
<a class="page-number-1" href="#page-number-1">View</a>
</li>
<li>
<h1>Wow!</h1>
<a class="page-number-2" href="#page-number-2">View</a>
</li>
<li>
<h1>More stuff...</h1>
<a class="page-number-3" href="#page-number-3">View</a>
</li>
<li>
<h1>Last page!</h1>
<a class="page-number-4" href="#page-number-4">View</a>
</li>
</ul>
</nav>`

如果您想要更多的页面，只需添加另一个

*   And your content. Make sure that the anchor text is updated correctly and everything is ready.

资源文件中带有导航代码的版本是“06-navigation.html”。

### 结论

添加令人印象深刻的效果并不需要第三方浏览器插件或大量专业知识。您可以使用标准化技术和广泛使用的脚本库来创建滚动效果。在此之后，设计师要添加自己的天赋。我希望你喜欢滚动效果！请随意在评论中分享您(或其他人)的实现。

## 分享这篇文章