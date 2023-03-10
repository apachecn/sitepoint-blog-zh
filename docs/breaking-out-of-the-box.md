# 打破 CSS 布局的框框

> 原文：<https://www.sitepoint.com/breaking-out-of-the-box/>

作为 [CSS 主题周](https://www.sitepoint.com/css-theme-week-more-css-than-you-can-handle)的一部分，本周的每一天，我们都会发表一篇由我们编辑精心挑选的文章。

我从不喜欢网络标准的设计师那里听到的关于 CSS 最常用的一个论点是，“CSS 设计太无聊了。它们太四四方方了。”我不禁觉得这种说法有点傻。对我来说，这就像说用木头建造的建筑是方方正正的。建筑师可以建造一个看起来很无聊的建筑。但是，使用同样的工具，建筑师可以创造出令人惊叹的艺术作品。这完全取决于创造力和实验。

的确，CSS 非常依赖网格——一切都在 *x* 和 *y* 轴上流动(并且可以这样定位，很像 Photoshop 中构建的设计)。然而，这并不意味着你的设计一定很无聊。如果你理解网格是如何工作的，你可以分解或抽象网格，使你的布局更加动态和有趣。为了实现这个目标(同时支持灵活性和可维护性)，CSS 设计比基于表格的布局提供了更多。

在开始之前，请务必下载我用来制作这些示例的文件,这样您就可以跟着做了。

##### 网格和线框

“在你打破规则之前，你必须知道规则。”

我在读书的时候，在整个设计和美术课上，都听到这句俗语。如果你上过印刷或布局的课程，你会知道网格是设计中最重要的要素之一。但是，如果您不熟悉这个概念，这里有一个非常简短的概述:

*   网格提供了组织。网格可以作为页面布局的“蓝色计划”精心设计的网格不仅美观，还能为内容提供组织性。
*   **一个网格可以定义更多的信息。一个组织良好的布局可以帮助提供页面之间的一致性，并定义在不同的点上提供什么类型的信息(例如:侧边栏可以用来提供次要信息；版权和法律信息等。可能放在页脚中，等等)。**
*   **一个网格可以统一一个系列。**为在任何媒体(名片、广告、网站布局等)上放置徽标、标题、图像和/或文本制定计划指南。)可以帮助将所有东西“绑”在一起。因此，网格可以是一个品牌非常重要的元素。

对于网页设计，线框是用来规划网站的网格。[线框](http://en.wikipedia.org/wiki/Website_wireframe)是一个基本的视觉指南，用于建议界面中基本设计元素的布局和位置。

线框是一个很好的方式来设计你的网站布局，而不用担心艺术细节。它们可以让你专注于内容的放置(导航、正文、标题等)。).这种技术在印刷设计中也有应用，但是在这篇文章中，我们将重点放在网络上。

线框也为你的站点的标记和样式表提供了一个指南。例如，如果你的站点有一个多栏布局(一个内容区域，和一个或两个侧栏)，你可以使用标记(通常是一个`<div>`标签，虽然这可以根据内容的不同而不同)来定义这些区域，将它们设计成列、行或由你的线框决定的任何东西。你可以用同样的方法来设计你的页眉，或者说报头，和页脚。

了解前端的“设置”会让你更深入地了解网站应该如何构建(当你开始应用艺术细节时，这是一件好事)。

现在，通常情况下，在我所参与的典型设计工作流程中，设计是在线框创建后完成的。然后，在任何编码开始之前创建内容。然而，出于本文的目的，内容是心血来潮创建的，所以代码将在早期发挥作用，只是为了学习。一旦这个过程变得更加自然，就可以建立一个“常规”的工作流程。但是现在，让我们来探索 CSS 的力量。(哇，最后一点听起来很戏剧化！)

##### 开始我们的设计

这是一个您通常在网站上看到的网格示例:

![1568_image1](img/3fc0ce9508ef48cd69611914da9e8e73.png)

我决定利用 960px 的宽度:我除以 5，得到 192px。我用它作为我的网格单位——列将是这个宽度，所有其他设计元素将与它成比例。标题也是 192 像素高。

这种布局的标记和样式可能非常简单；它可能看起来像这样的 HTML(我已经添加了虚拟副本):

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> 

<html  lang="en" xml:lang="en"> 

  <head> 

    <title>Example Layout</title> 

    <meta http-equiv="content-type" content="text/html; charset=utf-8" /> 

    <link type="text/css" rel="stylesheet" href="style.css"/> 

  </head> 

  <body> 

    **<div id="page"> 

      <div id="header"> 

        <p>Header content</p> 

      </div> 

      <div id="content-primary"> 

        <p><strong>Primary content.</strong> Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Suspendisse pharetra, arcu in dapibus sagittis, felis leo rhoncus erat, non porttitor urna libero vitae nibh. Ut convallis eros sed magna.</p> 

        <p>Aenean enim purus, adipiscing a, vehicula ut, tempor vitae, justo. Nam laoreet mauris vitae elit. Vivamus eros quam, euismod bibendum, pellentesque ut, tristique ut, nisl. <a href="#">Vestibulum ante ipsum primis</a> in faucibus orci luctus et ultrices posuere cubilia Curae; Suspendisse non metus. Nullam tempor dictum sapien. Duis enim. In ligula. Cras ut enim. Sed dapibus ante in eros. Nulla facilisi. In rhoncus eleifend nunc. Sed risus nulla, pretium in, porta eget, lacinia eu, nunc. In hac habitasse platea dictumst. Vestibulum feugiat lectus et justo. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Sed aliquet dolor nec ipsum.</p> 

        <p><a href="#">Ut erat nibh</a>, aliquet ut, congue at, tempus in, nunc. Ut aliquet leo et lectus volutpat molestie. Vivamus nunc. Nulla facilisi. Suspendisse dictum nunc tempus elit. Nullam urna quam, bibendum quis, tincidunt a, nonummy euismod, metus. Etiam convallis, dui venenatis feugiat elementum, justo lacus lobortis libero, vel iaculis nibh lectus eu urna. Vivamus arcu. In facilisis quam et lacus. Suspendisse sit amet neque ac enim lobortis ullamcorper. Etiam faucibus sapien ut nunc. Nullam consectetuer vehicula arcu.</p> 

      </div> 

      <div id="content-secondary"> 

        <p><strong>Secondary content.</strong> Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Suspendisse pharetra, arcu in dapibus sagittis, felis leo rhoncus erat, non porttitor urna libero vitae nibh. Ut convallis eros sed magna. Aenean enim purus, adipiscing a, vehicula ut, tempor vitae, justo. Nam laoreet mauris vitae elit. This is interesting information. Ut erat nibh, aliquet ut, congue at, tempus in, nunc. Ut aliquet leo et lectus volutpat molestie. Vivamus nunc. <a href="#">Read&nbsp;more&nbsp;&rarr;</a> </p> 

      </div> 

      <div id="footer"> 

        <p>Footer</p> 

      </div> 

    </div>** 

  </body> 

</html>
```

这是 CSS:

```
*** { 

  margin: 0; 

  padding: 0; 

} 

body { 

  text-align: center; 

} 

#page { 

  margin: 0 auto; 

  width: 960px; 

  text-align: left; 

} 

#header { 

  height: 192px; 

} 

#content-primary { 

  float: right; 

  width: 768px; 

} 

#content-secondary { 

  float: right; 

  width: 180px; 

} 

#footer { 

  clear: right; 

} 

.clear { 

  clear: both; 

}**
```

所有元素的边距和填充都用星号(*)重置。这将设置应用于所有内容的样式。阅读更多关于这种技术的内容，如 Andrew Krespanis 记录的[。](http://leftjustified.net/journal/2004/10/19/global-ws-reset/)

这个例子使用了将设计居中的标准方法:我为正文设置了 text-align: center，为宽度设置了 margin: 0 auto(在这个例子中是 960px)，为包含 div(我称之为“page”)设置了 text-align: left。

接下来，内容 div(`content-primary`和`content-secondary`)向右浮动。`content-primary`首先出现在标记中，因此这是正确对齐相邻列所需的方向。最后，页脚中的列被清除。

到目前为止，这是我们所知道的:

![1568_image2](img/49050f5780cf13f94ba650e3b16c2f9c.png)

##### 添加背景

让我们打开一些背景，这样我们可以更清楚地看到边界。注意，对于这个例子，我使用了[仿列技术](http://alistapart.com/articles/fauxcolumns/)来创建列。这种技术基本上采用背景图像，并垂直重复它以创建前两种列颜色(并避免不均匀的高度):

![1568_image3](img/230780ebc2ac5ff2c8335304e249f0ea.png)

实现这一点的 CSS 可以在`#page`选择器中找到:

```
#page {  

  margin: 0 auto;  

  width: 960px;  

  **background: url(page.gif) repeat-y;**  

  text-align: left;  

}
```

现在我们有了基本的结构，我们将添加一些空白和填充来创建一些空白。

我们页面的宽度— 960px —被 12 等分，所以我们将使用这个值作为我们的边距和填充的基础(稍后我们将使用它作为字体大小)。为了提供更多一点的喘息空间，让我们将它翻倍，并使用 24px 作为我们的边距。空白很重要。然后，我们将每列减去 48px，以适应新的边距。这是 CSS:

```
* {  

  margin: 0;  

  padding: 0;  

}  

body {  

  **background: #eee;**  

  text-align: center;  

}  

#page {  

  margin: 0 auto;  

  width: 960px;  

  **background: #fff url(page.gif) repeat-y;**  

  text-align: left;  

}  

#header {  

  height: 192px;  

  **background: #966;**  

}  

**#header p {  

  padding: 24px;  

}**  

#content-primary {  

  float: right;  

  **margin: 24px;  

  *width: 720px;***  

}  

#content-secondary {  

  float: right;  

  **margin: 24px;  

  *width: 144px;***  

}  

#footer {  

  clear: right;  

  **background: #333;**  

}    

**#footer p {  

  padding: 24px;  

}**  

.clear {  

  clear: both;  

}
```

到目前为止，这就是我们所拥有的。

![1568_image4](img/110d6d6e706c1da77aee3b5bb6efe165.png)

如你所见，我们基本上已经有了线框——添加了颜色和内容。现在，我们可以超越线框，让设计更具实验性。

##### 内容下降

让我们继续添加一些图像、菜单、标题等等，这样我们就有更多的工作要做了。不过，这不是一个高级教程，所以我们将保持事情相当简单。这是 HTML:

```
 <div id="page">  

      <div id="header">  

        <p>Header content</p>  

        **<ul>  

          <li><a href="#">Link</a></li>  

          <li><a href="#">Link</a></li>  

          <li><a href="#">Link</a></li>  

        </ul>**  

      </div>  

      <div id="content-primary">  

        **<h1>Welcome to my awesome website.</h1>  

        <h2>Indeed, this is an awesome website.</h2>**  

        <p><strong>Primary content.</strong> Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Suspendisse pharetra, arcu in dapibus sagittis, felis leo rhoncus erat, non porttitor urna libero vitae nibh. Ut convallis eros sed magna.</p>  

        **<img src="photo.jpg" alt="" />**  

        <p>Aenean enim purus, adipiscing a, vehicula ut, tempor vitae, justo. Nam laoreet mauris vitae elit. Vivamus eros quam, euismod bibendum, pellentesque ut, tristique ut, nisl. <a href="#">Vestibulum ante ipsum primis</a> in faucibus orci luctus et ultrices posuere cubilia Curae; Suspendisse non metus. Nullam tempor dictum sapien. Duis enim. In ligula. Cras ut enim. Sed dapibus ante in eros. Nulla facilisi. In rhoncus eleifend nunc. Sed risus nulla, pretium in, porta eget, lacinia eu, nunc. In hac habitasse platea dictumst. Vestibulum feugiat lectus et justo. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Sed aliquet dolor nec ipsum.</p>  

        **<h2>Oooh, more copy&hellip;</h2>**  

        <p><a href="#">Ut erat nibh</a>, aliquet ut, congue at, tempus in, nunc. Ut aliquet leo et lectus volutpat molestie. Vivamus nunc. Nulla facilisi. Suspendisse dictum nunc tempus elit. Nullam urna quam, bibendum quis, tincidunt a, nonummy euismod, metus. Etiam convallis, dui venenatis feugiat elementum, justo lacus lobortis libero, vel iaculis nibh lectus eu urna. Vivamus arcu. In facilisis quam et lacus. Suspendisse sit amet neque ac enim lobortis ullamcorper. Etiam faucibus sapien ut nunc. Nullam consectetuer vehicula arcu.</p>  

      </div>  

      <div id="content-secondary">  

        **<h3>Important Information</h3>**  

        <p><strong>Secondary content.</strong> Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Suspendisse pharetra, arcu in dapibus sagittis, felis leo rhoncus erat, non porttitor urna libero vitae nibh. Ut convallis eros sed magna. Aenean enim purus, adipiscing a, vehicula ut, tempor vitae, justo. Nam laoreet mauris vitae elit. This is interesting information. Ut erat nibh, aliquet ut, congue at, tempus in, nunc. Ut aliquet leo et lectus volutpat molestie. Vivamus nunc. <a href="#">Read&nbsp;more&nbsp;&rarr;</a> </p>  

      </div>  

      <div id="footer">  

        <p>***Photograph by <a href="http:/jinabolton.com">Jina Bolton</a>.***</p>  

      </div>  

    </div>
```

这里，添加了一个菜单(或导航系统)。我们还添加了一些标题和一张照片。现在，我必须承认，默认的排版设置和颜色让我有点抓狂，所以让我们继续添加一些美学样式，让事情看起来更好一些。看看这个 CSS:

```
* {  

  margin: 0;  

  padding: 0;  

}  

body {  

  background: #eee;  

  **font: 12px/18px "Lucida Grande", Verdana, sans-serif;**  

  text-align: center;  

  **color: #333;**  

}  

**a:link, a:visited {  

  color: #633;  

}  

a:hover {  

  color: #966;  

}  

h1 {  

  margin-bottom: 11px;  

  padding-top: 15px;  

  padding-bottom: 21px;  

  border-bottom: 1px solid #ccc;  

  font: normal italic 1.5em/18px Georgia, serif;  

  color: #633;  

}  

h2, h3{  

  font-weight: normal;  

  font-size: 1em;  

  line-height: 18px;  

}  

h2 {  

  padding-top: 10px;  

  padding-bottom: 20px;  

  font-size: 1.25em;  

  text-transform: uppercase;  

  color: #999;  

}  

h3 {  

  padding-bottom: 6px;  

  font-weight: bold;  

  text-transform: uppercase;  

  letter-spacing: -1px;  

  color: #633;  

}**  

#page {  

  margin: 0 auto;  

  width: 960px;  

  background: #fff url(page.gif) repeat-y;  

  text-align: left;  

}  

#header {  

  height: 192px;  

  background: #966;  

}  

#header p {  

  **float: left;  

  padding: 60px 24px 24px 24px;  

  font-size: 1.5em;  

  line-height: 1em;  

  color: #fff;**  

}  

**#header ul {  

  padding-top: 62px;  

  padding-right: 12px;  

  text-align: right;  

  list-style: none;  

}  

#header li {  

  display: inline;  

  margin: 0 12px;  

}  

#header li a {  

  font-size: 1.25em;  

  line-height: 1em;  

  text-decoration: none;  

  color: #fff;  

}  

#header li a:hover {  

  color: #300;  

}**  

#content-primary {  

  float: right;  

  margin: 24px;  

  width: 720px;  

}  

**#content-primary p {  

  padding-bottom: 18px;  

}  

#content-primary img {  

  float: left;  

  margin: -6px 18px 0 -6px;  

  padding: 3px;  

  border: 3px solid #eee;  

  background: #fff;  

}**  

#content-secondary {  

  float: right;  

  margin: 24px;  

  width: 144px;  

  **font-size: 11px;  

  color: #300;**  

}  

**#content-secondary p {  

  padding-bottom: 18px;  

}**  

#footer {  

  clear: right;  

  **height: 192px;**  

  background: #333;  

  **font-size: 10px;  

  line-height: 1em;  

  text-shadow: 1px 1px 1px #333;  

  color: #fff;**  

}  

**#footer a:link, #footer a:visited {  

  color: #c99;  

}**      

#footer p {  

  padding: 24px;  

}  

.clear {  

  clear: both;  

}
```

除了设置字体样式，我们还调整了间距，给东西更多的呼吸空间。页脚的高度被设置为 192 像素(不仅仅是为了一致性，我喜欢胖页脚)。我还给了`img`负的边距，这样填充和边框会延伸到边界之外，使它排列得更好。

让我们看看我们的进展。

![1568_image5](img/8e4fc239ab1d73d7d110fef8b62ca6db.png)

现在，如果所有的 CSS 设计看起来都像这个页面，我可以理解一个设计师对 CSS 布局令人厌烦的担忧。然而，正如我们将要看到的，让事情变得更有趣并不需要太多。

##### 破坏电网

我的设计老师被亲切地称为“网格纳粹”他会测量所有的东西，确保设计的所有部分都匹配并对齐。然而，尽管网格对他来说非常重要，但他强调，知道如何以正确的方式打破网格可以真正为设计带来活力。

那么，如何以正确的方式打破网格呢？嗯，重要的是要记住比例是一件美丽的事情。我喜欢通过将对象偏移到页边距之外来稍微拆分列。对于这个例子，我们可以使用偏移图像，并对主要内容应用左侧空白(以获得更多我们喜欢的空白)。看一下这个线框，其中的图像超出了由我们的线框创建的区域的边界:

![1568_image6](img/3b8b656a7e2620617f97974b73c2fa4c.png)

在 CSS 中使用负边距很容易实现这个特性:

```
#content-primary {   

  float: right;   

  ***margin: 12px 24px 60px 24px;   

  width: 528px;***   

}   

#content-primary img {   

  float: left;   

  ***margin: -6px 18px 0 -114px;***   

  padding: 3px;   

  border: 3px solid #eee;   

  background: #fff;   

}   

#content-secondary {   

  float: right;   

  ***margin: 30px 216px 60px 24px;***   

  width: 144px;   

  font-size: 11px;   

  color: #300;   

}
```

首先，我应用了一些仅用于美学目的的规则——如果你愿意，你可以决定跳过这一部分。我决定让两列的[基线](http://en.wikipedia.org/wiki/Baseline_(typography))正确对齐。为了实现这一点，我调整了`#content-primary`和`#content-secondary`的上边距，直到基线按预期对齐。然后，我在这两列的底部设置了一个更大的边距(60px)——我喜欢我创建的设计的底部间距比预期的大一点。

接下来，我减小了`#content-primary`的宽度，如线框图像所示。现在我们可以给图像一个 114 像素的负边距，这将允许图像延伸到边距的一半。

因为我们把`#content-primary`变小了，所以我们需要给`#content-secondary`一个更大的右边距，这样它就会像预期的那样坐在左边。我也想将`h1`的边界延伸到边缘，只是为了平衡一下。您也可以使用负边距来实现这一点:

```
h1 {   

  margin-bottom: 11px;   

  ***margin-left: -108px;   

  padding: 15px 0 21px 108px;***   

  border-bottom: 1px solid #ccc;   

  font: normal italic 1.5em/18px Georgia, serif;   

  color: #633;   

}
```

左侧空白被设置为等于负边距，因此文本将保持不变，但边框会按预期向外延伸。

另一个打破“四四方方”外观的好方法是使用装饰性的背景图片，它也可以延伸到页边距。我们使用与上面大致相同的方法来创建这个外观:

```
h2 {   

  **margin-left: -204px;   

  *padding: 10px 0 20px 204px;   

  background: url(h2_ornament.gif) 3px 0 no-repeat;***   

  font-size: 1.25em;   

  text-transform: uppercase;   

  color: #999;   

}
```

像这样的小变化可能简单而微妙，但它们有可能真正产生影响，打破直线。

![1568_image7](img/4d932dc7c335e5e68fe2357f0f405bd3.png)

虽然“打破网格”肯定有所帮助，但这种布局还需要更多的东西。

##### 应用抽象

安东尼·高迪认为直线属于人类，而曲线属于上帝。如果你想看抽象的，看看他的作品。

在这个项目中，我们并不完全是全力以赴的抽象，但是当你使用抽象的时候，把大师们的作品放在你的脑海中是很有趣的。无论是与网格一起使用，还是单独使用(就像高迪的风格)，抽象都可以添加一些小的“东西”，让设计变得真正有趣。

对于我们的布局，`#header`和`#footer`非常简单，所以让我们给这些部分添加一些时髦的背景图片。无论是每个人都喜欢的圆角、对角线还是漩涡，背景图像都由你决定…要有创意。

让我们从页脚开始。(页脚应该放在第一位没有什么特别的原因——我只是想选择它。)让我们添加一个背景图像来“装饰”页脚的顶部:

![1568_image8](img/5a88ec4dfe035ad6f0a8b317e0e4a039.png)

页脚的其余部分将用深灰色填充。下面是相关的 CSS:

```
#footer {   

  clear: right;   

  height: 192px;   

  ***background: #333 url(footer.gif) top no-repeat;***   

  font-size: 10px;   

  line-height: 1em;   

  text-shadow: 1px 1px 1px #333;   

  color: #fff;   

}
```

页脚内的文本现在应该向下显示，这样它就停留在深灰色区域:

```
#footer p {   

  ***padding: 132px 24px 0 24px;***   

}
```

我们可以通过为主体创建一个背景图像来创建一个错觉，即页脚延伸到包含 div ( `#page`)的边界之外。此图像将扩展灰色区域，因此它可以是一个简单的水平重复图像:

```
body {   

  ***background: #eee url(bg.gif) 0 100% repeat-x;***   

  font: 12px/18px "Lucida Grande", Verdana, sans-serif;   

  text-align: center;   

  color: #333;   

}
```

所以现在我们有了一个视觉上更吸引人的页脚:

![1568_image9](img/c740cb88fc646e997ff1ace468a01903.png)

让我们为标题做同样的事情。首先，我们将添加一个打破布局直线外观的背景图像:

![1568_image10](img/c1aa2d45f906d250efe207038ccddb90.png)

该图像将被放置在页眉的底部，我们将使用背景色来填充剩余的空间:

```
#header {   

  height: 192px;   

  ***background: #966 url(header.gif) bottom no-repeat;***   

}
```

接下来，让我们对标题内容进行填充，使其更好地显示在背景图像周围。您在这里采用的方法将根据您的标题内容而有所不同:

```
#header p {   

  float: left;   

  ***padding: 60px 24px 24px 126px;***   

  font-size: 1.5em;   

  line-height: 1em;   

  color: #fff;   

}
```

我们可以使用这种方法来应用一个额外的背景图像，使标题看起来延伸到了`#page`之外。要轻松做到这一点，我们可以在页面周围包装一个 div，并在应用背景图像时使用它(因为 body 已经为`#footer`使用了一个图像)。下面是最终的 HTML:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">    

<html  lang="en" xml:lang="en">    

  <head>    

    <title>Example Layout</title>    

    <meta http-equiv="content-type" content="text/html; charset=utf-8" />    

<link type="text/css" rel="stylesheet" href="style.css"/>    

  </head>    

  <body>    

    <div id="wrap">    

      <div id="page">    

        <div id="header">    

          <p>Header content</p>    

          <ul>    

            <li><a href="#">Link</a></li>    

            <li><a href="#">Link</a></li>    

            <li><a href="#">Link</a></li>    

          </ul>    

        </div>    

        <div id="content-primary">    

          <h1>Welcome to my awesome website.</h1>    

          <h2>Indeed, this is an awesome website.</h2>    

          <p><strong>Primary content.</strong> Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Suspendisse pharetra, arcu in dapibus sagittis, felis leo rhoncus erat, non porttitor urna libero vitae nibh. Ut convallis eros sed magna.</p>    

          <img src="photo.jpg" alt="" />    

          <p>Aenean enim purus, adipiscing a, vehicula ut, tempor vitae, justo. Nam laoreet mauris vitae elit. Vivamus eros quam, euismod bibendum, pellentesque ut, tristique ut, nisl. <a href="#">Vestibulum ante ipsum primis</a> in faucibus orci luctus et ultrices posuere cubilia Curae; Suspendisse non metus. Nullam tempor dictum sapien. Duis enim. In ligula. Cras ut enim. Sed dapibus ante in eros. Nulla facilisi. In rhoncus eleifend nunc. Sed risus nulla, pretium in, porta eget, lacinia eu, nunc. In hac habitasse platea dictumst. Vestibulum feugiat lectus et justo. Cum sociis natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Sed aliquet dolor nec ipsum.</p>    

          <h2>Oooh, more copy&hellip;</h2>    

          <p><a href="#">Ut erat nibh</a>, aliquet ut, congue at, tempus in, nunc. Ut aliquet leo et lectus volutpat molestie. Vivamus nunc. Nulla facilisi. Suspendisse dictum nunc tempus elit. Nullam urna quam, bibendum quis, tincidunt a, nonummy euismod, metus. Etiam convallis, dui venenatis feugiat elementum, justo lacus lobortis libero, vel iaculis nibh lectus eu urna. Vivamus arcu. In facilisis quam et lacus. Suspendisse sit amet neque ac enim lobortis ullamcorper. Etiam faucibus sapien ut nunc. Nullam consectetuer vehicula arcu.</p>    

        </div>    

        <div id="content-secondary">    

          <h3>Important Information</h3>    

          <p><strong>Secondary content.</strong> Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Suspendisse pharetra, arcu in dapibus sagittis, felis leo rhoncus erat, non porttitor urna libero vitae nibh. Ut convallis eros sed magna. Aenean enim purus, adipiscing a, vehicula ut, tempor vitae, justo. Nam laoreet mauris vitae elit. This is interesting information. Ut erat nibh, aliquet ut, congue at, tempus in, nunc. Ut aliquet leo et lectus volutpat molestie. Vivamus nunc. <a href="#">Read&nbsp;more&nbsp;&rarr;</a> </p>    

        </div>    

        <div id="footer">    

          <p>Photograph by <a href="http://jinabolton.com">Jina Bolton</a>.</p>    

        </div>    

      </div>    

    </div>    

  </body>    

</html>
```

然后我们可以添加所需的 CSS:

```
**#wrap {    

  background: url(wrap.gif) repeat-x;    

}**
```

这是最终的 CSS:

```
* {    

  margin: 0;    

  padding: 0;    

}    

body {    

  background: #eee url(bg.gif) 0 100% repeat-x;    

  font: 12px/18px "Lucida Grande", Verdana, sans-serif;    

  text-align: center;    

  color: #333;    

}    

a:link, a:visited {    

  color: #633;    

}    

a:hover {    

  color: #966;    

}    

h1 {    

  margin-bottom: 11px;    

  margin-left: -108px;    

  padding: 15px 0 21px 108px;    

  border-bottom: 1px solid #ccc;    

  font: normal italic 1.5em/18px Georgia, serif;    

  color: #633;    

}    

h2, h3{    

  font-weight: normal;    

  font-size: 1em;    

  line-height: 18px;    

}    

h2 {    

  margin-left: -204px;    

  padding: 10px 0 20px 204px;    

  background: url(h2_ornament.gif) 3px 0 no-repeat;    

  font-size: 1.25em;    

  text-transform: uppercase;    

  color: #999;    

}    

h3 {    

  padding-bottom: 6px;    

  font-weight: bold;    

  text-transform: uppercase;    

  letter-spacing: -1px;    

  color: #633;    

}    

#wrap {    

  background: url(wrap.gif) repeat-x;    

}    

#page {    

  margin: 0 auto;    

  width: 960px;    

  background: #fff url(page.gif) repeat-y;    

  text-align: left;    

}    

#header {    

  height: 192px;    

  background: #966 url(header.gif) bottom no-repeat;    

}    

#header p {    

  float: left;    

  padding: 60px 24px 24px 126px;    

  font-size: 1.5em;    

  line-height: 1em;    

  color: #fff;    

}    

#header ul {    

  padding-top: 62px;    

  padding-right: 12px;    

  text-align: right;    

  list-style: none;    

}    

#header li {    

  display: inline;    

  margin: 0 12px;    

}    

#header li a {    

  font-size: 1.25em;    

  line-height: 1em;    

  text-decoration: none;    

  color: #fff;    

}    

#header li a:hover {    

  color: #300;    

}    

#content-primary {    

  float: right;    

  margin: 12px 24px 60px 24px;    

  width: 528px;    

}    

#content-primary p {    

  padding-bottom: 18px;    

}    

#content-primary img {    

  float: left;    

  margin: -6px 18px 0 -114px;    

  padding: 3px;    

  border: 3px solid #eee;    

  background: #fff;    

}    

#content-secondary {    

  float: right;    

  margin: 30px 216px 60px 24px;    

  width: 144px;    

  font-size: 11px;    

  color: #300;    

}    

#content-secondary p {    

  padding-bottom: 18px;    

}    

#footer {    

  clear: right;    

  height: 192px;    

  background: #333 url(footer.gif) top no-repeat;    

  font-size: 10px;    

  line-height: 1em;    

  text-shadow: 1px 1px 1px #333;    

  color: #fff;    

}    

#footer a:link, #footer a:visited {    

  color: #c99;    

}        

#footer p {    

  padding: 132px 24px 0 24px;    

}    

.clear {    

  clear: both;    

}
```

结果如下:

![1568_image11](img/e1813a5846f6e99bb5b456594af1d4a2.png)

##### 更进一步

要“打破框框”，还有很多事情可以做。你可以让你的背景图像更具实验性，或者让页边距变得更疯狂——这两者都在[*CSS*](https://www.sitepoint.com/books/cssdesign1/)的艺术&科学中有详细的探讨，我是该书的合著者之一。虽然我们在这里讨论的绝对是一个最小的方法，但希望它能让你想到你可以尝试的有趣的事情(并超越 CSS 设计必须乏味的想法)。所以好好享受吧。看看这本书，还有一些我最喜欢的使用有趣的开箱技巧的网站:

[苹果 iPod Shuffle](http://www.apple.com/ipodshuffle/) :我真的很喜欢 iPod Shuffle 和耳机，它们看起来像是从容器后面“偷看”出来的。

![1568_image12](img/f187a5463f5acc08158d266499123ed6.png)

廷塞克:我非常喜欢布满整个屏幕的水平“条”——我认为这是避免完全封闭、四四方方的感觉的好方法。便利贴的角度也有助于打破设计，以及廷塞克的背景照片。

![1568_image13](img/cf1c332139e2d688ec27112b412e164a.png)

CSS Zen Garden，Entry #194 :背景图片确实有助于避免这种布局感觉四四方方，即使它遵循一个基本的网格。

![1568_image14](img/8ed82ee0fefaad1b46932436f1827662.png)

## 分享这篇文章