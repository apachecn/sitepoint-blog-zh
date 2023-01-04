# 你好 jQuery Mobile

> 原文：<https://www.sitepoint.com/hello-jquery-mobile/>

在本文中，我将向您介绍 jQuery Mobile，以下简称为 jQM。我们将逐步完成用 jQM 创作一个网站所需的绝对最低要求，触及一些初步的陷阱，并建立一个参考项目，当你迈出自己的第一步时，你可以与它牵手。

jQuery Mobile 将自己称为“智能手机和平板电脑的触摸优化 web 框架”，并进一步称为“所有流行移动设备平台的统一用户界面系统，建立在坚如磐石的 jQuery 和 jQuery UI 基础之上”。然后，本质上，使用这个框架创作您的文档，并且实际上保证在当前的移动设备上有一个好的结果。

### 建立

其中我们为我们的第一个实验建立了一个本地开发环境。下载 [jQM](http://jquerymobile.com/download/) ，我建议你也下载一份 jQM 所依赖的 [jQuery](http://docs.jquery.com/Downloading_jQuery) 的副本，在本地使用，不管你最终是否在产品中使用托管版本。将所有下载的文件移到您的项目中。

我选择将所有这些文件保存在项目根目录下的一个`/jqm/`文件夹中。这是为了保留 jQM 框架的脚本、CSS 和 sprite 图像之间的链接。你可以选择更传统的`/js/`和`/css/`和`/img/`文件夹，但是需要维护。请记住 jQM 项目的流动性，以及它当前的 *alpha* 状态。

### 基础

创建您的根 index.html 文件或等效文件。然后，如果你浏览 jQM 的文档和演示，浏览组件、页面和对话框，到解释页面基本结构的部分，你会找到她的样板模板。为了快速上手，复制并粘贴一个完整的单页模板，然后切换出多页`片段的`。``

 ``我已经更新了 `of the document to point to my `/jqm/`文件夹中的链接。在其生命的早期阶段，样板代码中有一些小问题，比如在`<footer>`容器之后的注释，但是它们是可以被原谅的。一些小的调整，我提出了我自己的样板模板代码的修订。`

```
<!DOCTYPE html> 
<html> 
    <head> 
        <title>Page Title</title> 
        <link rel="stylesheet" href="jqm/jquery.mobile-1.0a4.1.min.css" />
        <script src="jqm/jquery-1.5.2.min.js"></script>
        <script src="jqm/jquery.mobile-1.0a4.1.min.js"></script>
    </head> 
    <body> 
        <!-- Start of first page -->
        <div data-role="page" id="foo">
            <div data-role="header">
                <h1>Foo</h1>
            </div><!-- /header -->
            <div data-role="content">   
                <p>Hi, I'm Foo.</p> 
                <p>I'm first in the source order so I'm shown as the first page.</p>        
                <p>View the internal page called <a href="#bar">Bar</a></p> 
            </div><!-- /content -->
            <div data-role="footer">
                <h4>Page Footer</h4>
            </div><!-- /footer -->
        </div><!-- /page -->
        <!-- Start of second page -->
        <div data-role="page" id="bar">
            <div data-role="header">
                <h1>Bar</h1>
            </div><!-- /header -->
            <div data-role="content">   
                <p>Hello, I'm Bar.</p>  
                <p>I'm second in the source order, and I'm an internal page.</p>        
                <p><a href="#foo">Back to Foo</a></p>   
            </div><!-- /content -->
            <div data-role="footer">
                <h4>Page Footer</h4>
            </div><!-- /footer -->
        </div><!-- /page -->
    </body>
</html>
```

基本页面结构的文档解释说，你可以愉快地链接到外部页面，同样也可以愉快地在同一个文档中包含内部页面。您的选择将基于常识因素，取决于您正在构建的网站或应用程序，但为了简单起见，我们的参考项目将在内部存放其所有页面。我们稍后将回到错综复杂的问题。

现在，请注意重要的结构元素。每个包含页面的元素都包含`data-role="page"`，就像将`class=""`添加到元素中一样。每个页面包含三个以上的元素，分别是`data-role="header"`、`data-role="content"`和`data-role="footer"`。这个`data-role=""`的用法就是 jQM 的滚动方式。

现在启动您的项目，准备好这些代码，观察带有相关 jQM `data-role`属性的简单 HTML 结构的效果。你应该有两个链接的“页面”，免费包括幻灯片过渡。对于早熟者，尝试添加额外的文档和相关链接，观察外部链接如何在没有任何特殊配置的情况下也“正常工作”。

这里是第一个亲提示。为每个`data-role="page"`容器添加一个`data-title=""`属性，它将模仿 HTML 文档的`中的传统`<title>`。虽然 jQM 在为 Ajax 导航自动更新页面标题方面做得很好，但是在多页面站点上，您可以添加这个属性来手动定义标题，这样做有助于历史堆栈。`

 `### 语境

我们的参考项目关注于阐明如何单独使用 CSS 和零图像来创建各种形状。鉴于我们限制带宽受限的移动设备下载的负责任的目标，以及移动浏览器及其对 CSS3 的高级支持所带来的机遇，还有什么更好的背景呢？

这个奇特的 CSS 作品的最大功劳归于 [Chris Coyier 和他的 CSS 形状](http://css-tricks.com/examples/ShapesOfCSS/)，其余归于他的崇拜和有才华的公众，因为他们围绕 CSS3 only 形状的讨论和贡献。重要的是，请注意，我们参考网站的最终版本不会显示所有可能的形状，因此您有必要按照上面的链接亲自查看。

因此，为了阐明 CSS 形状，我们将更改样板代码，以便第一页显示 CSS 形状的示例，第二页显示从单个 HTML 元素创建该形状所需的 CSS。但首先转向 HTML5。该文档指出，jQM 站点必须以 HTML5 doctype 开始，以充分利用该框架。

### HTML5

想要深入了解 HTML5，只需看看真实世界的 html 5&CSS3 就可以了。当我构建我们的参考站点时，我会加入一些新的 HTML5 元素，去掉一些旧的元素。额外的奖励是在评论中指出我偏离了上述书中所提倡的教义。

*   将所有`<div data-role="page">`元素更改为`<section data-role="page">`
*   将所有`<div data-role="header">`元素更改为`<header data-role="header">`
*   将所有`<div data-role="content">`元素更改为`<article data-role="content">`
*   将所有`<div data-role="footer">`元素更改为`<footer data-role="footer">`

让我们抛弃`<!-- /header -->`类型的注释，我们不需要它们，因为标记本身就说明了一切。我们可以安全地将`type="text/javascript"`从脚本链接中移除，将`type="text/css"`从样式表链接中移除。撒上一个 `tag, first in the `<head>`。最后，请注意我在坚持明确的 XHTML 风格的结束斜线时的简单礼貌。`

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>CSS3 Shapes</title> 
        <link rel="stylesheet" href="jqm/jquery.mobile-1.0a4.1.min.css" />
        <script src="jqm/jquery-1.5.2.min.js"></script>
        <script src="jqm/jquery.mobile-1.0a4.1.min.js"></script>
    </head>
    <body>
        <section data-role="page" id="infinity" data-title="Infinity">
            <header data-role="header">
                <h1>Infinity</h1>
            </header>
            <article data-role="content">
                <div class="infinity"></div>
                <style>
                    .infinity {
                        position: relative;
                        width: 212px;
                        height: 100px;
                    }

                    .infinity:before,
                    .infinity:after {
                        content: "";
                        position: absolute;
                        top: 0;
                        left: 0;
                        width: 60px;
                        height: 60px;    
                        border: 20px solid #fc2e5a;
                        -moz-border-radius: 50px 50px 0 50px;
                             border-radius: 50px 50px 0 50px;
                        -webkit-transform: rotate(-45deg);
                           -moz-transform: rotate(-45deg);
                            -ms-transform: rotate(-45deg);
                             -o-transform: rotate(-45deg);
                                transform: rotate(-45deg);
                    }

                    .infinity:after {
                        left: auto;
                        right: 0;
                        -moz-border-radius: 50px 50px 50px 0;
                             border-radius: 50px 50px 50px 0;
                        -webkit-transform:rotate(45deg);
                           -moz-transform:rotate(45deg);
                            -ms-transform:rotate(45deg);
                             -o-transform:rotate(45deg);
                                transform:rotate(45deg);
                    }
                </style>
                <p><a href="#infinity_css" data-transition="pop">Infinity CSS</a></p>
            </article>
            <footer data-role="footer">
                <h4>BuildMobile: jQuery Mobile</h4>
            </footer>
        </section><section data-role="page" id="infinity_css" data-title="Infinity">
            <header data-role="header">
                <h1>Infinity</h1>
            </header>
            <article data-role="content">   
                <pre>
                    <code>
.infinity {
    position: relative;
    width: 212px;
    height: 100px;
}

.infinity:before,
.infinity:after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 60px;
    height: 60px;    
    border: 20px solid #fc2e5a;
    -moz-border-radius: 50px 50px 0 50px;
         border-radius: 50px 50px 0 50px;
    -webkit-transform: rotate(-45deg);
       -moz-transform: rotate(-45deg);
        -ms-transform: rotate(-45deg);
         -o-transform: rotate(-45deg);
            transform: rotate(-45deg);
}

.infinity:after {
    left: auto;
    right: 0;
    -moz-border-radius: 50px 50px 50px 0;
         border-radius: 50px 50px 50px 0;
    -webkit-transform:rotate(45deg);
       -moz-transform:rotate(45deg);
        -ms-transform:rotate(45deg);
         -o-transform:rotate(45deg);
            transform:rotate(45deg);
}
                    </code>
                </pre>
                <p><a href="#infinity" data-transition="pop" data-direction="reverse">Infinity Shape</a></p>
            </article>
            <footer data-role="footer">
                <h4>BuildMobile: jQuery Mobile</h4>
            </footer>
        </section>
    </body>
</html>
```

### 过渡和方向

更深入地挖掘，注意在基于文本的链接中添加了一个`data-transition="pop"`属性。向链接添加一个`data-transition`属性，将默认的从`slide`转换为任何支持的选项，包括`slideup`、`slidedown`、`pop`、`fade`和`flip`。由于 CSS 转换的使用，动画应该在许多移动设备上进行硬件加速。

jQM 礼貌地将转换代码归于 jQT，或 jQTouch，并做了一些小的修改。jQT 由 David Kaneda 构建，由 Jonathan Stark 维护，毫无疑问，在不久的将来，我们将在 BuildMobile 上发布对 jQT 库的介绍，因为它提供了显著而有趣的差异。

还要注意从第二页到第一页的链接中添加了`data-direction="reverse"`。这迫使向后转换，使得`data-transition="pop"`收缩而不是增长。但是请仔细注意，`data-direction="reverse"`指定了一个反向转换，而没有真正回到历史。

现在是思考页面流动的好时机，不管是内部的还是其他的，选择合适的过渡和方向。有一些关于重定向和链接到目录的高级文档，我们可能会在后面的教程中涉及到。当然，你可以在文档中读到更多关于导航模型、Ajax、散列和历史的技术细节。

### 基本列表视图

我们的参考站点包含许多形状，因此我们将添加一个导航列表。添加一个带有`data-role="page"`的新容器，并确保它是源代码中的第一个。在`data-role="content"`容器中，为我们的每个形状添加一个`<ul>`和一个`<li>`。因为我们的页面是内部的，每个`<li>`应该包含一个基于 id 的`href="#shape"`。

简单地将`data-role="listview"`添加到`<ul>`中，就可以实现 jQM 的魔力。列表将被转换成一个全角元素，在左侧显示锚文本和标准的右箭头图标。单击或轻敲任何链接，都会将标准的从右到左幻灯片动画触发到链接的页面中。

在列表中添加一些果汁也很简单。将`data-filter="true"`添加到`<ul>`进行即时客户端搜索。可选地添加`data-filter-placeholder=""`和你选择的占位符文本。列表的文档很丰富，但是您会发现高级功能很容易实现，并且遵循我们到目前为止已经介绍过的相同模式。

### 导航注意事项

我们的参考网站有一个导航页面，九个“形状”页面，每个页面都有一个“CSS”页面。所有栏都有一个由 jQM 框架自动添加的“后退”按钮，这是`data-role="header"`的标准配置。但是没有干预，它就有缺陷，打破了心智模式。

如果你要在“形状”和“CSS”页面之间切换几次，每个转换都会被添加到历史堆栈中。单击“后退”按钮将会反转历史，并在“形状”和“CSS”页面之间切换多次，最后返回到您开始的导航页面。

所以我们将`data-rel="back"`添加到链接中，它模仿 back 按钮，返回一个历史条目并忽略锚的默认`href`。尽管被忽略，但重要的是添加一个有意义的`href`，它实际上指向引用页面的 URL，以便 C 级浏览器可以跟随它，并且流程不会中断。

我预测这种情况将会改变，将来我们可能连默认的“返回”按钮都没有了。我再次将您引回到导航模型的文档中。现在，请注意从一个 Ajax 加载的页面到多个内部页面的链接需要添加`rel="external"`或`data-ajax="false"`来进行完全重新加载，清除 URL 中的 Ajax 散列。

### 关闭

除了内容中的 CSS 之外，没有一行 CSS 是在制作“Shapes”参考网站时编写的，当然，我不考虑内容。除了 jQM 框架中现有的精灵之外，不需要任何图像。jQM 框架和它所依赖的 jQuery 库的总重量大约是 234KB，加上我们的参考文档的 20KB。

时间已经不多了，在这篇文章中，我们仅仅触及了页面、对话框、工具栏和列表视图的一些文档。您自己很容易理解文档，但是您可能希望在注释中请求更多关于按钮、表单和一般内容格式的内容。

在以后的文章中，我将更深入地探讨这个 API 的各个方面。但是现在，让我们做一些众包游击队测试。在你的手机上启动参考网站，无论是安卓手机、iPhone 还是 iPad，告诉世界什么是可行的，什么是不可行的。我将在下一期文章中尝试解决这些问题。

### 额外学分

让一个网站的行为更像一个应用程序，在标签中加入一个标签。

更好的是，对于非 retina 智能手机，将`添加到`中，并将 PNG 放入根目录。然后将`添加到`和根目录下的 PNG 中，进行视网膜显示。

*   [GitHub jQuery 手机演示](https://github.com/buildmobile/jqm)
*   [3 简易 jQuery 移动演示](http://3easy.org/buildmobile/jquerymobile/)

